# ⚙️ C# / .NET EditorConfig

![Version](https://img.shields.io/badge/version-1.0.0-555555?style=flat-square&labelColor=333333)&nbsp;
![.NET](https://img.shields.io/badge/.NET-10-555555?style=flat-square&labelColor=333333)&nbsp;
![C#](https://img.shields.io/badge/C%23-14-555555?style=flat-square&labelColor=333333)&nbsp;
[![License](https://img.shields.io/badge/license-MIT-555555?style=flat-square&labelColor=333333)](#license)

A reusable, opinionated coding standard for C#/.NET projects, defined in a single `.editorconfig` file. It combines formatting, naming, code style, and code quality rules to promote consistency and maintainability across projects. The configuration includes .NET analyzer settings and scoped exceptions for common scenarios and can be customized to meet project and framework requirements.

## Key Conventions

The following sections highlight selected coding conventions, explain the reasoning behind them, and illustrate their use with practical examples. Code examples may omit surrounding context to focus on specific conventions.

<details>
<summary><strong>Brace Style</strong></summary>

---

**K&R Style**

Opening braces follow the K&R style and appear on the same line as the declaration or statement they belong to. This uses less vertical space and keeps more surrounding code visible. Both K&R and Allman are valid choices, and this preference can be customized to maintain consistency with existing project conventions.

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
<summary><strong>Brace Usage</strong></summary>

---

**Brace Requirements**

Braces are generally recommended for control statements because they make block boundaries clear and reduce the risk of mistakes when code is modified. They may be omitted when both the header and body are simple and each fits on a single line, but are required if either spans multiple lines.

```csharp
// Allowed: braces omitted for a simple statement.
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

---

**Formatting Recommendations**

For readability and easier debugging, control blocks with braces should span multiple lines, even when they contain only one statement. This is a formatting recommendation and is not enforced. Compact property declarations may remain on one line.

```csharp
// Discouraged: placing the entire control block on one line.
if (showGreeting) { Console.WriteLine("Hello, World!"); }

// Allowed: a compact property declaration.
public string Name { get; set; } = string.Empty;
```

</details>

<details>
<summary><strong>Field Visibility</strong></summary>

---

**Disallowed Field Exposure**

All instance fields, including `readonly` fields, and all mutable `static` fields must be private. Exposing these fields directly creates dependencies on a type's internal implementation and may allow state changes without validation.

`const` and `static readonly` fields are exceptions and may have any accessibility.

```csharp
// Disallowed: exposes instance state directly.
public int Value;

// Disallowed: exposes shared mutable state.
internal static int TotalCount;
```

---

**Controlled Access**

Keeping fields private supports encapsulation but does not guarantee it on its own. Properties and methods should provide controlled access to internal state, allowing changes to be validated while keeping internal implementation details hidden.

```csharp
// Keep the underlying state private.
private int _value;

// Provide read access without exposing the field.
public int Value => _value;

// Validate changes before modifying the state.
public void Add(int amount) {
    ArgumentOutOfRangeException.ThrowIfNegativeOrZero(amount);

    _value += amount;
}
```

</details>

<details>
<summary><strong>Field Naming</strong></summary>

---

**Private Fields**

Private instance fields, including `readonly` fields, use `_camelCase`, while private mutable `static` fields use `s_camelCase`. These prefixes distinguish instance state from shared mutable state at a glance, without requiring explicit qualification.

```csharp
// Private instance fields: _camelCase.
private int _value;
private readonly int _limit;
private volatile int _status;

// Private mutable static fields: s_camelCase.
private static int s_totalCount;
private static volatile int s_sharedStatus;
```

---

**Constants and Static Readonly Fields**

Constants and `static readonly` fields use `PascalCase` regardless of accessibility. Constants require compile-time values, while `static readonly` fields can be initialized at runtime and cannot be reassigned once type initialization is complete.

```csharp
// Constants: PascalCase at every accessibility level.
public const int PublicLimit = 100;
internal const int InternalLimit = 100;
private const int PrivateLimit = 100;

// Static readonly fields: PascalCase at every accessibility level.
public static readonly int PublicValue = 100;
internal static readonly int InternalValue = 100;
private static readonly int PrivateValue = 100;
```

</details>

<details>
<summary><strong>Expression &amp; Block Bodies</strong></summary>

---

**Expression Bodies**

Expression bodies are required for properties, indexers, and accessors wherever possible. These members should remain simple and focused, with more complex operations delegated to dedicated methods.

For operators, expression bodies are preferred when the implementation fits clearly on one line.

```csharp
// Properties, indexers, and accessors.
public int Value => _value;

public int this[int index] => _values[index];

public int EditableValue {
    get => _value;
    set => _value = value;
}

// Operators.
public static Example operator +(Example left, Example right) => new(left.Value + right.Value);
```

---

**Block Bodies**

Block bodies are preferred for constructors and methods, even when they contain only one statement. They provide a consistent structure and allow implementations to grow without changing the body style.

```csharp
public Example(int value) {
    _value = value;
}

public int GetValue() {
    return _value;
}
```

---

**Local Functions & Lambdas**

Local functions and lambdas should perform small, focused operations. Expression bodies are preferred for simple implementations, while block bodies are required when multiple statements are needed.

```csharp
public int Calculate() {
    // Local function.
    static int Double(int value) => value * 2;

    // Lambda expression.
    Func<int, int> increment = static value => value + 1;

    return increment(Double(_value));
}
```

</details>

<details>
<summary><strong>Implicit &amp; Explicit Typing</strong></summary>

---

**Implicit Typing**

The use of `var` is preferred for built-in types and for declarations where the initializer makes the type apparent, such as explicit object creation. This keeps declarations concise and avoids repeating type information where it is already visible.

```csharp
// Preferred: var keeps built-in type declarations concise.
var count = 10;
var message = "Hello, World!";

// Preferred: the initializer makes the type apparent.
var example = new Example(10);
```

---

**Explicit Typing**

An explicit declaration is preferred when the resulting type is not apparent from the initializer, unless it is a built-in type. This is particularly useful for method calls whose names do not clearly indicate the type they return. Stating the type explicitly keeps the code understandable without requiring readers to inspect method signatures or rely on IDE features.

```csharp
// Preferred: an explicit type clarifies the result of a method call.
Example loadedExample = Load();
```

</details>

## Getting Started

This configuration can be adopted in both new and existing C#/.NET projects. New projects can follow its conventions from the outset, while existing codebases may require code changes or project-specific overrides.

The following steps explain how to add the configuration, enable code style analysis during builds, and verify that `.editorconfig` is correctly detected.

### Environment

> [!NOTE]
> The configuration can also be used with older C# and .NET versions, although some rules may not be supported or applicable. Visual Studio is not required for command-line analysis, and support for individual settings and diagnostics may vary across IDEs.

**Target Environment**

- **Language and Framework:** C# 14 / .NET 10
- **Target IDE:** Visual Studio 2026+
- **Tested SDK:** .NET SDK 10.0.401 (CLI)

**Analyzer Dependencies**

- [`Microsoft.CodeAnalysis.NetAnalyzers`](https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers) (included in the .NET SDK)
- [`Microsoft.CodeAnalysis.CSharp.CodeStyle`](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle) (included in the .NET SDK)

---

### 1. Add the configuration

Place [`.editorconfig`](.editorconfig) in the root directory of your project or solution.

If a configuration already exists, review its settings before replacing it. Transfer any settings you need to retain to the `Project-Specific Overrides` section of the new file, preserving their intended scope and resolving any conflicts.

```text
Solution/
├── .editorconfig              ← Add the file here
├── Example.slnx
├── src/
│   └── Example/
│       └── Example.csproj
└── tests/
    └── Example.Tests/
        └── Example.Tests.csproj
```

---

### 2. Configure build-time analysis

Code quality analyzers are enabled by default in projects targeting .NET 5 or later, while code style analysis is disabled during builds by default. Enabling code style analysis allows supported diagnostics configured as warnings or errors to be reported during compilation.

To enable this analysis, set `EnforceCodeStyleInBuild` to `true` in the project's `.csproj` file or a shared `Directory.Build.props` file to apply the setting across multiple projects. If the file already exists, add the property to an existing `<PropertyGroup>`.

```xml
<Project>
  <PropertyGroup>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>
</Project>
```

Diagnostics configured as errors can cause the build to fail.

> [!NOTE]
> Visual Studio may skip analyzer checks in builds triggered by **Run** or **Debug**. An application may therefore launch even when an explicit **Build** or **Rebuild** fails on analyzer errors. To disable this optimization, set `OptimizeImplicitlyTriggeredBuild` to `false`, as described under **Additional build settings**.

<details>
<summary><strong>Additional build settings</strong></summary>

---

**Analyzer Configuration**

`EnableNETAnalyzers` explicitly enables the built-in .NET code quality analyzers, while `AnalysisLevel` selects the default rule set for a .NET release. This helps preserve rule selection across SDK upgrades, although individual rules may still change as analyzers are updated.

```xml
<PropertyGroup>
  <EnableNETAnalyzers>true</EnableNETAnalyzers>
  <AnalysisLevel>10.0</AnalysisLevel>
</PropertyGroup>
```

---

**Build Behavior**

`OptimizeImplicitlyTriggeredBuild` set to `false` disables an optimization that may skip analyzer checks during builds triggered by **Run** or **Debug** in Visual Studio. This can increase build and launch times.

`TreatWarningsAsErrors` causes compiler and analyzer warnings to be treated as errors, preventing successful builds when such warnings are reported.

```xml
<PropertyGroup>
  <OptimizeImplicitlyTriggeredBuild>false</OptimizeImplicitlyTriggeredBuild>
  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
</PropertyGroup>
```

---

**Analyzer Project Properties**

Depending on the SDK version, `RootNamespace` and `ProjectDir` may be exposed to analyzers by default. If explicit configuration is required, the following example exposes these properties so that `IDE0130` can check namespace and folder alignment.

Place this `<ItemGroup>` directly inside the `<Project>` element of your `.csproj` or shared `Directory.Build.props` file. If an appropriate `<ItemGroup>` already exists, add the entries to it.

```xml
<ItemGroup>
  <CompilerVisibleProperty Include="RootNamespace" />
  <CompilerVisibleProperty Include="ProjectDir" />
</ItemGroup>
```

</details>

---

### 3. Verify file detection

Before relying on the configuration, verify that MSBuild correctly detects the intended `.editorconfig` file. Run the following command, replacing the example path with the path to your project's `.csproj` file:

```sh
dotnet msbuild src/Example/Example.csproj -getItem:EditorConfigFiles
```

Check the `EditorConfigFiles` list in the output:

- **File Detected:** An `Identity` entry points to the expected `.editorconfig`. Other configuration files may also be listed.
- **File Not Detected:** The list is empty (`"EditorConfigFiles": []`) or does not include the expected file.

If the file is not detected, verify its name, location, and the project path specified in the command. Automatic discovery searches for `.editorconfig` files in each source file's directory and its parent directories. This command does not build the project or modify source code. It also does not verify which rules apply to individual files, as these depend on matching sections and local overrides.

> [!IMPORTANT]
> If MSBuild detects the configuration but Visual Studio does not apply its settings, open **Tools > Options > All Settings > Text Editor > General** and ensure [**Follow project coding conventions**](https://learn.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options#troubleshoot-editorconfig-settings) is enabled. Then reopen the affected source file.

## Usage

Compatible IDEs automatically detect `.editorconfig` and apply its supported formatting and code analysis settings. Violations are reported according to their configured severity levels, although support for individual rules may vary between IDEs. Adding the configuration does not automatically reformat existing code, so formatting tools, code fixes, or manual changes may be needed. When build-time analysis is enabled, supported diagnostics are also reported during builds.

### Shared Exceptions

The configuration includes predefined exceptions for common scenarios that may benefit from different analyzer settings. These scoped overrides help reduce unnecessary diagnostics while preserving the shared baseline elsewhere.

The table below outlines the scope and purpose of each exception.

| Scenario | Scope | Matching names | Purpose |
| --- | --- | --- | --- |
| Tests | Directories | `Tests`, `tests`, `*.Tests` | Accommodate testing conventions, including underscore-separated names and locally created test data. |
| Fakes | Directories | `Fakes`, `fakes` | Allow lightweight test doubles by relaxing selected validation and optimization rules. |
| DTOs | Directories | `DTOs`, `Dtos`, `dtos` | Allow data transfer objects to use string-based URIs and writable collections. |
| EF Core migrations | Directories | `Migrations`, `migrations` | Reduce unnecessary diagnostics in scaffolded migration code. |
| Dependency injection | Files | `DependencyInjection.cs` | Allow unused registration return values and namespaces independent of folder structure. |

Exceptions apply based on file paths rather than the types declared within them. Directory-based exceptions apply to C# files in matching directories and their subdirectories. When multiple sections match a file, later sections override only the settings they explicitly redefine. For example, files in `Tests/Fakes` match both sections, with the later `Fakes` section taking precedence for conflicting settings.

The complete set of exceptions is defined under `Shared Exceptions` in [`.editorconfig`](.editorconfig).

### Customization

The configuration provides a reusable baseline that can be adapted to meet project-specific requirements. Individual rules and [shared exceptions](#shared-exceptions) can be customized without modifying the entire standard. Only settings that differ from the baseline need to be overridden.

---

#### Option 1 — Root Configuration

For minor adjustments, the simplest approach is to add scoped overrides to the `Project-Specific Overrides` section at the end of the root `.editorconfig`. This keeps the shared configuration and project-specific adjustments in one file.

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

This sets the severity of `CA1707` to `warning` for C# files in `src/Example` and its subdirectories. File paths are relative to the root `.editorconfig`.

---

#### Option 2 — Child Configuration

For more extensive or independent adjustments, place a child `.editorconfig` in the directory containing the affected code. This keeps local overrides separate from the shared configuration while allowing them to inherit its settings.

```text
Solution/
├── .editorconfig              ← Shared baseline
└── src/
    └── Example/
        ├── .editorconfig      ← Local overrides
        └── Example.csproj
```

To apply the same override, add the following to the child `.editorconfig`:

```ini
[*.cs]
dotnet_diagnostic.CA1707.severity = warning
```

Here, `[*.cs]` matches C# files in the child configuration's directory and its subdirectories. Omit `root = true` to preserve inheritance from the parent `.editorconfig`. Any settings you do not explicitly override retain their inherited values.

## License

This project is licensed under the [MIT License](LICENSE).

The license text is also included at the end of [`.editorconfig`](.editorconfig) to ensure that its copyright and permission notices accompany the configuration when reused. These notices must be retained when copying, modifying, or redistributing the file, as required by the license.
