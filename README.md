# ⚙️ C# / .NET EditorConfig

![Version](https://img.shields.io/badge/version-1.0.0-555555?style=flat-square&labelColor=333333)&nbsp;
![.NET](https://img.shields.io/badge/.NET-10-555555?style=flat-square&labelColor=333333)&nbsp;
![C#](https://img.shields.io/badge/C%23-14-555555?style=flat-square&labelColor=333333)&nbsp;
[![License](https://img.shields.io/badge/license-MIT-555555?style=flat-square&labelColor=333333)](#license)

A shared, opinionated coding standard for C#/.NET projects, defined in a reusable `.editorconfig` file.
It brings together formatting, naming, code style, and code quality rules to support consistency and maintainability across different codebases.
The configuration includes .NET analyzer settings and scoped exceptions for common scenarios,
and can be adapted to the requirements of individual projects and frameworks.


## Key Conventions

The choices below give this standard its particular style. They can be [customized](#customization) to match a project's needs or a team's preferences.

Examples illustrate individual conventions and may omit surrounding code.

<details>
<summary><strong>Brace Style</strong> — K&amp;R</summary>
  
---
  
Opening braces stay on the same line as the declaration or statement that introduces the block. This keeps the layout compact and leaves more surrounding code visible.

```csharp
using System;

namespace Example;

internal static class Program {
    private static void Main() {
        Console.WriteLine("Hello, World!");
    }
}
```

</details>

<details>
<summary><strong>Brace Usage</strong> — Required for multiline statements</summary>
  
---
  
Braces are required when a control statement's header or body spans multiple lines. This allows concise statements while making the boundaries of longer constructs explicit.

Braces are also recommended for simpler statements to reduce ambiguity during later edits. The configured rule does not prevent every mistake caused by omitted braces.

```csharp
// Allowed: the condition and body statement each fit on one line.
if (showGreeting)
    Console.WriteLine("Hello, World!");

// Recommended: braces make the block boundaries explicit.
if (showGreeting) {
    Console.WriteLine("Hello, World!");
}

// Required: the condition spans multiple lines.
if (showGreeting &&
    isUserReady) {
    Console.WriteLine("Hello, World!");
}

// Required: the body statement spans multiple lines.
if (showGreeting) {
    Console.WriteLine(
        "Hello, {0}!",
        userName);
}
```

Keep braced control blocks on multiple lines for clearer stepping and breakpoint placement. Compact property declarations may remain on one line. This is a layout recommendation, separate from the enforced brace requirement.

```csharp
// Avoid: placing the entire control block on one line.
if (showGreeting) { Console.WriteLine("Hello, World!"); }

// Allowed: a compact property declaration.
public string Name { get; set; } = string.Empty;
```

</details>

<details>
<summary><strong>Field Visibility</strong> — Private fields</summary>
  
---
  
Instance fields, including readonly fields, and mutable static fields must be private. This keeps access to stored state under the type's control and reduces dependencies on its internal representation.

Expose data through properties or methods that provide the intended access and validate changes where needed. Constants and static readonly fields are exempt from this visibility restriction.

**Non-private fields**

```csharp
// Disallowed: a non-private instance field.
public int Value;

// Disallowed: a non-private mutable static field.
internal static int TotalCount;
```

**Controlled access**

```csharp
// Keep the instance field private.
private int _value;

// Expose read access through a property.
public int Value => _value;

// Validate input before changing the stored value.
public void Add(int amount) {
    ArgumentOutOfRangeException.ThrowIfNegativeOrZero(amount);

    _value += amount;
}
```

</details>

<details>
<summary><strong>Field Naming</strong> — Prefixes and casing</summary>
  
---
  
Private instance fields use `_camelCase`, while private mutable static fields use `s_camelCase`. Constants and static readonly fields use `PascalCase` at every accessibility level.

These conventions make a field's role recognizable at its point of use and help distinguish instance state from state shared by the type.

```csharp
// Private instance fields: _camelCase.
private int _value;
private readonly int _limit;
private volatile int _status;

// Private mutable static fields: s_camelCase.
private static int s_totalCount;
private static volatile int s_sharedStatus;

// Constants: PascalCase at every accessibility level.
public const int PublicLimit = 100;
internal const int InternalLimit = 100;
protected const int ProtectedLimit = 100;
protected internal const int ProtectedInternalLimit = 100;
private protected const int PrivateProtectedLimit = 100;
private const int PrivateLimit = 100;

// Static readonly fields: PascalCase at every accessibility level.
public static readonly int PublicValue;
internal static readonly int InternalValue;
protected static readonly int ProtectedValue;
protected internal static readonly int ProtectedInternalValue;
private protected static readonly int PrivateProtectedValue;
private static readonly int PrivateValue;
```

</details>

<details>
<summary><strong>Expression &amp; Block Bodies</strong> — Simple, focused members</summary>
  
---
  
Expression bodies keep simple value access concise, while block bodies give procedural code a consistent structure.

Properties and operators are expected to remain focused. When an implementation becomes difficult to express clearly, consider whether substantial logic belongs in a separate method. This is a design guideline, not a complexity check performed by the analyzer.

**Expression bodies**

Expression bodies are required for properties, indexers, and accessors where possible. For operators, they are preferred when the expression fits on a single line.

```csharp
// Required where possible: properties, indexers, and accessors.
public int Value => _value;

public int this[int index] => _values[index];

public int EditableValue {
    get => _value;
    set => _value = value;
}

// Preferred: an operator whose expression fits on one line.
public static Example operator +(Example left, Example right) => new(left.Value + right.Value);
```

**Block bodies**

Constructors and methods prefer block bodies, even when they contain only one statement.

```csharp
public Example(int value) {
    _value = value;
}

public int GetValue() {
    return _value;
}
```

**Local functions & lambdas**

Expression bodies are suggested when they fit on a single line.

```csharp
public int Calculate() {
    // Suggested: a single-line local function.
    static int Double(int value) => value * 2;

    // Suggested: a single-line lambda.
    Func<int, int> increment = static value => value + 1;

    return increment(Double(_value));
}
```

</details>

<details>
<summary><strong>Type Inference</strong> — Selective use of <code>var</code></summary>
  
---
  
`var` is preferred for built-in types and when the type is apparent from the initializer. Explicit types are suggested elsewhere.

This avoids repeating obvious type information while keeping less apparent types visible at the declaration.

```csharp
// Preferred: var for built-in types.
var count = 10;
var message = "Hello, World!";

// Preferred: var when the initializer clearly identifies the type.
var example = new Example(10);

// Suggested: an explicit type when the initializer does not reveal it.
Example loadedExample = Load();
```

</details>


## Getting Started

> [!TIP]
> The standard is easiest to adopt in new projects, where its conventions can be followed from the start.
> Existing projects can also use it, but may require code changes or adjustments to the configuration.


### 1. Add the configuration

Copy the [`.editorconfig`](.editorconfig) file to the repository root, above the projects that should follow the standard.
It sets `root = true`, which stops inheritance from parent directories. Child configurations can still override its settings.

If a root `.editorconfig` already exists, review its settings before replacing it and preserve any necessary project-specific configuration.

```text
Repository/
├── .editorconfig              ← Add the file here
├── Example.slnx
├── src/
│   └── Example/
│       └── Example.csproj
└── tests/
    └── Example.Tests/
        └── Example.Tests.csproj
```


### 2. Configure build-time analysis

SDK code quality analyzers are enabled by default for .NET 10 projects.
To also report supported code style diagnostics during builds, add the following property to the project's `.csproj` file.
Alternatively, use a root `Directory.Build.props` to share it across projects.

In an existing file, add the property to an existing `<PropertyGroup>` or add the group shown below inside `<Project>`.

```xml
<Project>
  <PropertyGroup>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>
</Project>
```

Rules configured as errors can cause the build to fail.

> [!NOTE]
> Visual Studio may skip analyzers in builds triggered by **Run** or **Debug**, allowing an application to launch even when an explicit **Build** or **Rebuild** fails on an analyzer error.
> To disable this optimization, set `OptimizeImplicitlyTriggeredBuild` to `false` using the additional settings below.

<details>
<summary><strong>Additional build settings</strong></summary>

---

Merge the entries you need into the same `.csproj` or `Directory.Build.props` file, uncommenting them as needed.

```xml
<Project>
  <PropertyGroup>
    <!-- Explicitly enable SDK code quality analyzers -->
    <!-- <EnableNETAnalyzers>true</EnableNETAnalyzers> -->

    <!-- Use .NET 10 analysis defaults without pinning the SDK -->
    <!-- <AnalysisLevel>10.0</AnalysisLevel> -->

    <!-- Retain analyzer checks in Visual Studio Run/Debug builds -->
    <!-- <OptimizeImplicitlyTriggeredBuild>false</OptimizeImplicitlyTriggeredBuild> -->

    <!-- Treat compiler and analyzer warnings as errors -->
    <!-- <TreatWarningsAsErrors>true</TreatWarningsAsErrors> -->
  </PropertyGroup>

  <!-- Provide project information for IDE0130 during CLI builds -->
  <!--
  <ItemGroup>
    <CompilerVisibleProperty Include="RootNamespace" />
    <CompilerVisibleProperty Include="ProjectDir" />
  </ItemGroup>
  -->
</Project>
```

The `CompilerVisibleProperty` entries supply the project information needed by
[`IDE0130`](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/style-rules/ide0130)
to check namespaces against the folder structure during command-line builds.

Disabling implicit build optimization may increase the time needed to start the application.
For more information, see the
[MSBuild properties reference](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props).

</details>


### 3. Verify file detection

Run the following command from the repository root, replacing the path with the project's actual `.csproj` path:

```sh
dotnet msbuild src/Example/Example.csproj -getItem:EditorConfigFiles
```

Check the `EditorConfigFiles` list in the output:

- **File found:** an `Identity` entry points to the repository's `.editorconfig`. Other configuration files may also be listed.
- **File not found:** the list is empty (`"EditorConfigFiles": []`) or contains only other files.

If the file is missing, confirm that it is named `.editorconfig`, is located in the project directory or one of its parent directories, and that the command targets the intended project.

This command checks file discovery without building the project or modifying source code. The settings applied to each source file depend on matching sections and local overrides.

> [!IMPORTANT]
> If the file is found but Visual Studio does not apply its settings, open **Tools > Options** and make sure
> [**Follow project coding conventions**](https://learn.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options#troubleshoot-editorconfig-settings)
> is enabled. Then reopen the affected source file.


## Usage

Compatible IDEs should detect `.editorconfig` automatically and apply its supported settings. Analyzer diagnostics appear in the editor according to the configured severity levels.

Use the IDE's formatting command to apply formatting to existing code. With build-time analysis enabled, supported analyzer diagnostics are also reported during builds.

### Shared Exceptions

The standard adjusts selected rules for common scenarios. Directory-based exceptions apply to all C# files in matching directories and their subdirectories.

| Scenario | Scope | Matching names | Purpose |
| --- | --- | --- | --- |
| Tests | Directories | `Tests`, `tests`, `*.Tests` | Support testing conventions, including names with underscores and locally created test data. |
| Fakes | Directories | `Fakes`, `fakes` | Allow simplified dependency implementations with fewer validation and optimization requirements. |
| DTOs | Directories | `DTOs`, `Dtos`, `dtos` | Support data transfer representations, including string URIs and writable collections. |
| EF Core migrations | Directories | `Migrations`, `migrations` | Reduce analyzer noise in scaffolded migration code. |
| Dependency injection | Files | `DependencyInjection.cs` | Allow ignored registration return values and namespaces independent of folder structure. |

Exceptions are selected by path, regardless of a class's role. When scopes overlap, later sections override only the settings they redefine. For example, `Tests/Fakes` inherits test exceptions and then applies the `Fakes` overrides.

See `Shared Exceptions` in [`.editorconfig`](.editorconfig) for the complete overrides. Adjust the selectors if the repository uses different directory or file names.


### Customization

Overrides can adjust both the baseline rules and the [shared exceptions](#shared-exceptions). Include only settings that need to change.

#### Option 1 — Root configuration

---

The simplest approach is to add overrides to `Project-Specific Overrides` at the end of the root `.editorconfig`:

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

This sets `CA1707` to a warning for C# files in `src/Example` and its subdirectories. Paths are relative to `.editorconfig`, and later matching settings take precedence.

#### Option 2 — Child configuration

---

A child `.editorconfig` keeps project-specific settings alongside the code, making them easier to find and maintain. Omit `root = true` to inherit the shared baseline.

```text
Repository/
├── .editorconfig              ← Shared baseline
└── src/
    └── Example/
        ├── .editorconfig      ← Local overrides
        └── Example.csproj
```

For the same override, add the following to the child file:

```ini
[*.cs]
dotnet_diagnostic.CA1707.severity = warning
```

Here, `[*.cs]` matches C# files in that directory and its subdirectories. All settings not overridden by the child remain inherited.


## License

This repository is licensed under the [MIT License](LICENSE).

A copy of the license is included at the end of [`.editorconfig`](.editorconfig) so it remains available when the configuration is copied into another project. Retain the copyright and license notice when copying or redistributing the file, including modified versions.
