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

The choices below give this standard its particular style. They represent one approach to writing C# code and can be [customized](#customization) to match a project's needs or a team's preferences.

Examples illustrate individual conventions and may omit surrounding code.

<details>
<summary><strong>Brace Style</strong> — K&amp;R</summary>

Opening braces follow K&R style and stay on the same line as the declaration or statement that introduces the block. This keeps the layout compact and leaves more surrounding code visible.

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

Braces are required when a control statement's header or body spans multiple lines. They are also recommended in simpler cases to make block boundaries clear and reduce the risk of mistakes during later edits.

Keep braced blocks on multiple lines to make breakpoint placement and step-by-step debugging easier. Compact property declarations may remain on one line. This layout recommendation is separate from the configured multiline brace requirement.

**Control statements**

```csharp
// Allowed: braces are optional for a single-line body.
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

// Avoid: the entire block is placed on one line.
if (showGreeting) { Console.WriteLine("Hello, World!"); }
```

**Compact properties**

```csharp
// Allowed: a simple property can stay on one line.
public string Name { get; set; } = string.Empty;
```

</details>

<details>
<summary><strong>Field Visibility</strong> — Private fields</summary>

Instance fields, including readonly fields, and mutable static fields must be private. This keeps access to stored state under the type's control and reduces dependencies on its internal representation.

Expose data through properties or methods that provide the intended read and write access and validate changes where needed. Constants and static readonly fields are exempt from this visibility restriction.

**Non-private fields**

```csharp
// Disallowed: non-private instance fields.
public int Value;
protected int Step;

// Disallowed: non-private mutable static fields.
internal static int TotalCount;
```

**Controlled access**

```csharp
// Required: keep the instance field private.
private int _value;

// Expose read access through a property.
public int Value => _value;

// Validate input before changing the field through a method.
public void Add(int amount) {
    ArgumentOutOfRangeException.ThrowIfNegativeOrZero(amount);

    _value += amount;
}
```

</details>

<details>
<summary><strong>Field Naming</strong> — Prefixes and casing</summary>

Private instance fields use `_camelCase`, while private mutable static fields use `s_camelCase`. Constants and static readonly fields use `PascalCase` at every accessibility level.

These conventions make field roles recognizable at a glance and help distinguish instance state from state shared by the type.

```csharp
// Required: _camelCase for private instance fields.
private int _value;
private readonly int _limit;
private volatile int _status;

// Required: s_camelCase for private mutable static fields.
private static int s_totalCount;
private static volatile int s_sharedStatus;

// Required: PascalCase for constants at every accessibility level.
public const int PublicLimit = 100;
internal const int InternalLimit = 100;
protected const int ProtectedLimit = 100;
protected internal const int ProtectedInternalLimit = 100;
private protected const int PrivateProtectedLimit = 100;
private const int PrivateLimit = 100;

// Required: PascalCase for static readonly fields at every accessibility level.
public static readonly int PublicValue;
internal static readonly int InternalValue;
protected static readonly int ProtectedValue;
protected internal static readonly int ProtectedInternalValue;
private protected static readonly int PrivateProtectedValue;
private static readonly int PrivateValue;
```

</details>

<details>
<summary><strong>Expression & Block Bodies</strong> — Simple, focused members</summary>

Expression bodies are required for properties, indexers, and accessors where possible. For operators, they are preferred when the expression fits on a single line. The same single-line style is suggested for local functions and lambdas. Constructors and methods prefer block bodies, even for a single statement.

This keeps simple value access concise and gives procedural code a consistent structure. When a property or operator needs substantial logic, consider whether that logic would be clearer in a separate method.

**Expression bodies**

```csharp
// Required: expression bodies for properties, indexers, and accessors where possible.
public int Value => _value;

public int this[int index] => _values[index];

public int EditableValue {
    get => _value;
    set => _value = value;
}

// Preferred: expression bodies for operators that fit on a single line.
public static Example operator +(Example left, Example right) => new(left.Value + right.Value);
```

**Block bodies**

```csharp
// Preferred: block bodies for constructors and methods, even for one statement.
public Example(int value) {
    _value = value;
}

public int GetValue() {
    return _value;
}
```

**Local functions & lambdas**

```csharp
public int Calculate() {
    // Suggested: an expression body for a single-line local function.
    static int Double(int value) => value * 2;

    // Suggested: an expression body for a single-line lambda.
    Func<int, int> increment = static value => value + 1;

    return increment(Double(_value));
}
```

</details>

<details>
<summary><strong>Type Inference</strong> — Selective use of <code>var</code></summary>

`var` is preferred for built-in types and when the type is apparent from the initializer. Explicit types are suggested elsewhere.

This avoids repeating obvious type information while keeping less apparent types visible at the declaration.

```csharp
// Preferred: var for built-in types.
var count = 10;
var message = "Hello, World!";

// Preferred: var when the type is apparent from the expression.
var example = new Example(10);

// Suggested: an explicit type when the expression does not reveal it.
Example loadedExample = GetExample();
```

</details>

## Environment

The standard targets the following environment:

- **Language and framework:** C# 14 / .NET 10
- **Target IDE:** Visual Studio 2026+
- **Verified toolchain:** .NET SDK 10.0.401 (CLI)

> [!NOTE]
> The SDK version identifies the toolchain used for verification. The `.editorconfig` file does not select or install an SDK.
> Projects that need to select a specific SDK version can use [`global.json`](https://learn.microsoft.com/en-us/dotnet/core/tools/global-json).

### Analyzers

The standard uses the following analyzers:

- [Microsoft.CodeAnalysis.NetAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers) (included in the .NET SDK)
- [Microsoft.CodeAnalysis.CSharp.CodeStyle](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle) (included in the .NET SDK)

### Editor and build support

EditorConfig-compatible editors and formatters apply the formatting settings they support.
Visual Studio also reports applicable analyzer diagnostics while editing.

The setup in [Quick Start](#quick-start) enables supported analyzer diagnostics during builds.
Some code style rules are available only in the IDE, and support for individual settings may vary between editors.
Visual Studio is not required for command-line analysis.

## Quick Start

> [!TIP]
> The standard is easiest to adopt in new projects, where its conventions can be followed from the start.
> Existing projects can also use it, but may require code changes or adjustments to the configuration.

The following steps cover adding the configuration, enabling build-time analysis, and checking that the project finds the file.

### 1. Add the configuration

Copy the [`.editorconfig`](.editorconfig) file to the repository root, above the projects that should follow the standard.
It sets `root = true`, which stops inheritance from `.editorconfig` files in parent directories.
Child configurations can still override its settings.

If a root `.editorconfig` already exists, review it before replacing it.
Preserve any necessary project-specific settings as [scoped overrides](#customization).

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
To also report supported code style diagnostics during builds, add the following setting to the project's `.csproj` file.
Alternatively, use a root `Directory.Build.props` to share it across projects.
In an existing file, add the setting inside its `<Project>` element.

Rules configured as errors can cause builds to fail as soon as build-time analysis is enabled.

```xml
<Project>
  <PropertyGroup>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>
</Project>
```

> [!NOTE]
> Visual Studio may skip analyzers in builds triggered by **Run** or **Debug**, allowing an application to launch even when an explicit **Build** or **Rebuild** fails on an analyzer error.
> To disable this optimization, set `OptimizeImplicitlyTriggeredBuild` to `false` using the additional settings below.

<details>
<summary><strong>Additional build settings</strong></summary>

The following settings are optional. Merge the entries you need into the same `.csproj` or `Directory.Build.props` file, uncommenting them as needed.

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
For more information about these settings, see the
[MSBuild properties reference](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props).

</details>

### 3. Verify that the project finds the configuration

Run this command from the repository root, replacing the path with the project's actual `.csproj` path:

```sh
dotnet msbuild src/Example/Example.csproj -getItem:EditorConfigFiles
```

In the output, check `EditorConfigFiles`:

- **File found:** an `Identity` entry points to the repository's `.editorconfig`. Other configuration files may also be listed.
- **File not found:** the output shows `"EditorConfigFiles": []`, or the list contains only other files.

If the shared file is missing, check its name and location, and confirm that the command targets the intended project.
The file must be named `.editorconfig` and, for the layout above, sit in the repository root above the project directories.

This command confirms file discovery without modifying source code or building the project. 
The settings applied to each source file depend on matching sections and local overrides.

> [!IMPORTANT]
> If the file is found but Visual Studio does not apply its settings, open **Tools > Options** and make sure
> [**Follow project coding conventions**](https://learn.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options#troubleshoot-editorconfig-settings)
> is enabled. Then reopen the affected source file.

## Shared Exceptions

The baseline adjusts selected rules for common scenarios that benefit from different settings. The reasons below summarize the purpose of each group and provide representative examples.

Directory-based exceptions apply to all C# files in matching directories and their subdirectories.

| Scenario | Scope | Matching names | Reason |
| --- | --- | --- | --- |
| Tests | Directories | `Tests`, `tests`, `*.Tests` | Keeps tests focused on readable, isolated scenarios, accommodating patterns such as descriptive names and locally created test data. |
| Fakes | Directories | `Fakes`, `fakes` | Supports deliberately simplified dependency implementations, where some validation and optimization requirements may be unnecessary. |
| DTOs | Directories | `DTOs`, `Dtos`, `dtos` | Lets data contracts determine the shape of transferred data, including string representations and writable collections when appropriate. |
| EF Core migrations | Directories | `Migrations`, `migrations` | Avoids repetitive changes to scaffolded migration code solely to satisfy baseline preferences. |
| Dependency injection | Files | `DependencyInjection.cs` | Supports common registration patterns, including ignored fluent return values and extension namespaces shared across folders. |

When scopes overlap, later matching sections override only the settings they redefine. For example, a `Fakes` directory inside `Tests` receives both sets of overrides, with the later section taking precedence for shared settings.

The complete rule overrides are defined in the `Shared Exceptions` section of [`.editorconfig`](.editorconfig).
Adjust its selectors if the repository uses different directory or file names.

## Customization

Use scoped overrides to adapt the standard to a project's requirements, including changes to the [shared exceptions](#shared-exceptions). Include only settings that need to differ from the baseline.

The examples below show two ways to change `CA1707`, which checks for underscores in identifiers, to a warning for C# files in `src/Example` and its subdirectories.

### Root configuration

The simplest approach is to add overrides to the `Project-Specific Overrides` section of the root `.editorconfig`. Define their scope using file selectors relative to that file.

Later matching sections take precedence for the settings they define.

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

### Child configuration

When a project needs several adjustments, a child `.editorconfig` keeps its overrides alongside the code they affect. This makes project-specific settings easier to find and maintain while keeping the shared baseline focused on common rules.

Create the file in the project or directory it should affect, and omit `root = true` to inherit the shared baseline.

```text
Repository/
├── .editorconfig              ← Shared baseline
└── src/
    └── Example/
        ├── .editorconfig      ← Local overrides
        └── Example.csproj
```

In this location, `[*.cs]` covers C# files in `src/Example` and all its subdirectories:

```ini
[*.cs]
dotnet_diagnostic.CA1707.severity = warning
```

The child configuration overrides only the settings it defines. All other settings remain inherited.

## License

This repository is licensed under the [MIT License](LICENSE).

A copy of the license is also included at the end of [`.editorconfig`](.editorconfig) so it stays with the configuration when copied into another project. Retain the copyright and license notice when copying or redistributing the file, including modified versions.
