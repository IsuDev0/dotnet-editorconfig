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

The conventions below reflect deliberate choices about how code should look and be structured. They can be [customized](#customization) to suit a project's needs or a team's preferences.

Examples illustrate individual conventions and may omit surrounding code.

<details>
<summary><strong>Brace Style</strong> — K&amp;R</summary>

---

Opening braces stay on the same line as the declaration or statement that introduces the block. This compact layout keeps more surrounding code visible.

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

Braces are required when a control statement's header or body spans multiple lines. Short statements can remain concise, while longer constructs have explicit boundaries.

Using braces for shorter statements is also recommended to reduce ambiguity during later edits. The configured rule does not prevent every mistake caused by omitted braces.

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

For clearer stepping and breakpoint placement, keep braced control blocks on multiple lines. Compact property declarations may remain on one line. This layout recommendation is separate from the enforced brace requirement.

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

Instance fields, including readonly fields, and mutable static fields must be private. Constants and static readonly fields are exempt from this restriction.

Private fields keep stored state under the type's control and reduce dependencies on its internal representation. Expose data through properties or methods that define the intended access and validate changes where needed.

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

These names help distinguish instance state from shared state and make a field's role clear where it is used.

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

Properties and operators should remain focused. If an implementation becomes difficult to express clearly, consider moving complex logic into a separate method. This is a design guideline. The analyzer checks the body style without evaluating the member's responsibilities.

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

Block bodies are preferred for constructors and methods, even when they contain only one statement.

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

This avoids repeating obvious type information and keeps the type visible when it would otherwise be unclear.

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
> Adopting it in an existing project may require code changes or adjustments to the configuration.

### Environment

- **Language and framework:** C# 14 / .NET 10
- **Target IDE:** Visual Studio 2026+
- **Tested SDK:** .NET SDK 10.0.401 (CLI)

> [!NOTE]
> The SDK version identifies the toolchain used for verification.
> `.editorconfig` does not select or install an SDK version.

**Analyzer dependencies**

- [Microsoft.CodeAnalysis.NetAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers) (included in the .NET SDK)
- [Microsoft.CodeAnalysis.CSharp.CodeStyle](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle) (included in the .NET SDK)

Visual Studio is not required for command-line analysis. Other IDEs may differ in their support for individual settings and diagnostics.

### 1. Add the configuration

Copy [`.editorconfig`](.editorconfig) to the repository root, above the projects that should follow the standard.
The file sets `root = true` to stop inheritance from parent directories. Child configurations can still override its settings.

If the repository already has a root `.editorconfig`, review it before replacing it and preserve any project-specific settings that are still needed.

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
To include supported code style diagnostics in builds, add the property below to the project's `.csproj` file or to a root `Directory.Build.props` to share it across projects.

If the file already exists, add the property to a `<PropertyGroup>` within its existing `<Project>` element.

```xml
<Project>
  <PropertyGroup>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>
</Project>
```

Rules configured as errors can cause the build to fail.

> [!NOTE]
> Visual Studio may skip analyzers in builds triggered by **Run** or **Debug**. An application can therefore launch even when an explicit **Build** or **Rebuild** fails on an analyzer error.
> To run analyzers in these builds as well, set `OptimizeImplicitlyTriggeredBuild` to `false`, as shown under **Additional build settings**.

<details>
<summary><strong>Additional build settings</strong></summary>

---

Uncomment the entries you need and add them to the same `.csproj` or `Directory.Build.props` file.

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

The `CompilerVisibleProperty` entries provide the project information that
[`IDE0130`](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/style-rules/ide0130)
needs to check namespace and folder alignment during command-line builds.

Setting `OptimizeImplicitlyTriggeredBuild` to `false` may increase the time needed to launch the application from Visual Studio.
For details about these settings, see the
[MSBuild properties reference](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props).

</details>

### 3. Verify file detection

From the repository root, run the command below with the path to the project's `.csproj` file:

```sh
dotnet msbuild src/Example/Example.csproj -getItem:EditorConfigFiles
```

Check the `EditorConfigFiles` list in the output:

- **File found:** an `Identity` entry points to the repository's `.editorconfig`. Other configuration files may also be listed.
- **File not found:** the list is empty (`"EditorConfigFiles": []`) or does not include the expected `.editorconfig`.

If the file is missing from the list, check its name and location. It must be named `.editorconfig` and placed in the project directory or a parent directory. Also check that the command targets the intended project.

This check confirms file discovery without building the project or changing source code. Which settings apply to each source file depends on matching sections and local overrides.

> [!IMPORTANT]
> If the file is found but Visual Studio does not apply its settings, open **Tools > Options** and make sure
> [**Follow project coding conventions**](https://learn.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options#troubleshoot-editorconfig-settings)
> is enabled. Then reopen the affected source file.

## Usage

Compatible IDEs should detect `.editorconfig` automatically and apply the settings they support. Analyzer diagnostics appear in the editor at their configured severity levels.

Use the IDE's formatting command to format existing code. With build-time analysis enabled, builds also report supported analyzer diagnostics.

### Shared Exceptions

The standard adjusts selected rules to accommodate common scenarios. The table summarizes their scope and purpose. Directory-based exceptions cover all C# files in matching directories and their subdirectories.

| Scenario | Scope | Matching names | Purpose |
| --- | --- | --- | --- |
| Tests | Directories | `Tests`, `tests`, `*.Tests` | Accommodate testing patterns such as names with underscores and locally created test data. |
| Fakes | Directories | `Fakes`, `fakes` | Allow simplified dependency implementations with fewer validation and optimization checks. |
| DTOs | Directories | `DTOs`, `Dtos`, `dtos` | Support data transfer representations, including string URIs and writable collections. |
| EF Core migrations | Directories | `Migrations`, `migrations` | Reduce analyzer noise in scaffolded migration code. |
| Dependency injection | Files | `DependencyInjection.cs` | Allow ignored registration return values and namespaces independent of folder structure. |

A file's path determines which exceptions apply, regardless of the class's role. When scopes overlap, later sections override only the settings they redefine. For example, C# files in `Tests/Fakes` inherit the test exceptions, then apply the `Fakes` overrides.

The complete overrides are listed under `Shared Exceptions` in [`.editorconfig`](.editorconfig). Adjust the selectors if the repository uses different directory or file names.

### Customization

Use either approach below to adjust the baseline rules or [shared exceptions](#shared-exceptions). Include only settings that should differ from the baseline.

#### Option 1 — Root configuration

---

The simplest approach is to add scoped overrides under `Project-Specific Overrides` at the end of the root `.editorconfig`:

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

This sets `CA1707` to a warning for C# files in `src/Example` and its subdirectories. Paths are relative to the root `.editorconfig`. When sections overlap, later values take precedence for the settings they redefine.

#### Option 2 — Child configuration

---

A child `.editorconfig` keeps local overrides alongside the code they affect. This separates project-specific choices from the shared baseline and makes them easier to find and maintain. Omit `root = true` so the child inherits the baseline.

```text
Repository/
├── .editorconfig              ← Shared baseline
└── src/
    └── Example/
        ├── .editorconfig      ← Local overrides
        └── Example.csproj
```

To apply the same override, add this to the child file in `src/Example`:

```ini
[*.cs]
dotnet_diagnostic.CA1707.severity = warning
```

Here, `[*.cs]` matches C# files in that directory and its subdirectories. The child overrides only the settings it defines. All others remain inherited.

## License

This repository is licensed under the [MIT License](LICENSE).

The license is also embedded at the end of [`.editorconfig`](.editorconfig) so it stays with the file when copied into another project. Retain the copyright and license notice when copying or redistributing the file, including modified versions.
