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

**Brace Style**

Opening braces are placed on the same line as the declaration or statement they belong to, following the K&R style. While Allman is widely used in C#/.NET projects, both styles are valid choices, and neither is inherently superior. This configuration favors K&R because it uses less vertical space, keeps related code closer together, and leaves more surrounding context visible. Ultimately, consistency within a codebase matters more than the chosen brace style, and this preference can be customized to match existing project conventions.

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

**Brace Usage**

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

**Brace Usage - Recommendations and exceptions**

Control blocks with braces should span multiple lines, even when they contain only one statement. This keeps statements visually distinct, improves readability, and makes it easier to set breakpoints during debugging. This formatting preference is recommended rather than enforced. Compact property declarations may remain on one line.

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

**Disallowed field exposure**

All instance fields, including readonly fields, and all mutable static fields must be private. Exposing these fields directly creates dependencies on a type's internal implementation and may allow state changes without validation.

```csharp
// Disallowed: exposes instance state directly.
public int Value;

// Disallowed: exposes shared mutable state.
internal static int TotalCount;
```

---

**Controlled access**

Keeping fields private supports encapsulation but does not guarantee it on its own. Properties and methods should provide controlled access to internal state, ensuring that changes can be validated and internal implementation details remain hidden.

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
<summary><strong>Naming convention</strong></summary>

---

**Naming convention - Private fields**

Private instance fields, including readonly fields, use _camelCase, while private mutable static fields use s_camelCase. These prefixes distinguish instance state from shared mutable state at a glance, without requiring explicit qualification.

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

**Naming convention - const & static readonly fields**

Constants and static readonly fields use PascalCase regardless of accessibility. Both represent values that cannot be reassigned, making a shared naming convention appropriate. However, constants must be defined using compile-time values, while static readonly fields can be initialized at runtime.

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

**Expression bodies**

Properties, indexers, and accessors should remain simple and focused on their intended responsibilities. Expression bodies are therefore required wherever possible to encourage straightforward implementations and discourage unnecessary logic. More complex operations should be delegated to dedicated methods.

Operators are treated more flexibly, as some implementations may require multiple statements. However, single-line expression bodies remain preferred whenever practical.

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

**Block bodies**

Block bodies are preferred for constructors and methods, even when they contain only one statement. Unlike properties and accessors, these members often perform multiple operations, making a consistent block-based style easier to read and maintain. Expression bodies remain allowed for simple implementations, but switching between styles solely to reduce line count is generally discouraged.

```csharp
public Example(int value) {
    _value = value;
}

public int GetValue() {
    return _value;
}
```

---

**Local functions & lambdas**

Local functions and lambdas should perform small, focused operations within their surrounding scope. They keep related logic close to where it is needed without introducing unnecessary methods for code that is not reused elsewhere. Expression bodies are therefore preferred for simple implementations. When more complex logic requires multiple statements, block bodies must be used to maintain clarity and readability.

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

**Using var**

The use of var is preferred when the initializer makes the type immediately apparent, avoiding redundant declarations. This includes built-in types and explicit object creation, where repeating the type adds no meaningful information.

When the resulting type is not immediately clear, an explicit declaration is preferred, especially if the type may be unexpected. Although the compiler infers types at compile time and IDEs can often reveal them, code should remain understandable without additional inspection. Explicit types improve readability and help prevent incorrect assumptions about the values being used.

```csharp
// Preferred: var keeps built-in type declarations concise.
var count = 10;
var message = "Hello, World!";

// Preferred: the initializer makes the type apparent.
var example = new Example(10);

// Preferred: an explicit type clarifies the result of a method call.
Example loadedExample = Load();
```

</details>

## Getting Started

This configuration can be adopted in both new and existing C#/.NET projects. New projects can follow its conventions from the outset, while existing codebases may require code changes or project-specific overrides.

The following steps explain how to integrate the configuration into a project, enable code style analysis during builds, and verify that .editorconfig is correctly detected. Additional settings are also provided for projects that require more control over analyzer behavior and enforcement.

### Environment

> [!NOTE]
> The configuration can also be used with older C# and .NET versions, although some rules may not be supported or applicable. Visual Studio is not required for command-line analysis, and support for individual settings and diagnostics may vary across IDEs.

**Target environment**

- **Language and framework:** C# 14 / .NET 10
- **IDE:** Visual Studio 2026+
- **Tested SDK:** .NET SDK 10.0.401 (CLI)

**Analyzer dependencies**

- [Microsoft.CodeAnalysis.NetAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers) (included in the .NET SDK)
- [Microsoft.CodeAnalysis.CSharp.CodeStyle](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle) (included in the .NET SDK)

---

### 1. Add the configuration

Place .editorconfig in the root directory of your project or solution.

If an .editorconfig file already exists, you can replace it if its settings are no longer needed. Otherwise, review the existing configuration and transfer the relevant settings to the Project-Specific Overrides section of the new file, preserving their intended scope and resolving any conflicts.

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

---

### 2. Configure build-time analysis

Code quality analyzers are enabled by default in projects targeting .NET 5 or later, while code style diagnostics are generally disabled during builds. Enabling build-time code style analysis helps enforce coding conventions beyond the editor and detect violations during compilation.

To enable this analysis, set EnforceCodeStyleInBuild to true in the project's .csproj file or a shared Directory.Build.props file to apply the setting across multiple projects. If the file already exists, add the property to an existing <PropertyGroup>.

```xml
<Project>
  <PropertyGroup>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>
</Project>
```

Diagnostics configured as errors can cause the build to fail.

> [!NOTE]
> Visual Studio may skip analyzer checks in builds triggered by Run or Debug. An application may therefore launch even when an explicit Build or Rebuild fails on analyzer errors. To disable this optimization, set OptimizeImplicitlyTriggeredBuild to false, as described under Additional build settings.

<details>
<summary><strong>Additional build settings</strong></summary>

---

**Analyzer Configuration**

EnableNETAnalyzers explicitly enables the built-in .NET code quality analyzers, while AnalysisLevel determines which analyzer rule set is applied. Specifying an analysis level helps maintain a consistent set of diagnostics when upgrading to newer SDK versions.

```xml
<PropertyGroup>
  <EnableNETAnalyzers>true</EnableNETAnalyzers>
  <AnalysisLevel>10.0</AnalysisLevel>
</PropertyGroup>
```

---

**Build Behavior**

OptimizeImplicitlyTriggeredBuild controls Visual Studio's build optimization for Run and Debug operations. Setting it to false disables an optimization that may skip analyzer checks, although this can increase build and launch times.

TreatWarningsAsErrors causes compiler and analyzer warnings to be treated as errors, preventing successful builds when such warnings are reported.

```xml
<PropertyGroup>
  <OptimizeImplicitlyTriggeredBuild>false</OptimizeImplicitlyTriggeredBuild>
  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
</PropertyGroup>
```

---

**Analyzer Project Properties**

Some analyzers require additional project information to function correctly during command-line builds. The CompilerVisibleProperty entries expose RootNamespace and ProjectDir to IDE0130, allowing it to verify namespace and folder alignment.

```xml
<ItemGroup>
  <CompilerVisibleProperty Include="RootNamespace" />
  <CompilerVisibleProperty Include="ProjectDir" />
</ItemGroup>
```

</details>

---

### 3. Verify file detection

Before relying on the configuration, verify that MSBuild correctly detects the intended .editorconfig file. Run the following command, replacing the example path with the path to your project's .csproj file:

```sh
dotnet msbuild src/Example/Example.csproj -getItem:EditorConfigFiles
```

Check the EditorConfigFiles list in the output:

- **File found:** An Identity entry points to the expected .editorconfig. Other configuration files may also be listed.
- **File not found:** The list is empty ("EditorConfigFiles": []) or does not include the expected file.

If the file is not detected, verify its name, location, and the project path specified in the command. For automatic discovery, .editorconfig must be located in the project directory or one of its parent directories. This command does not build the project or modify source code, nor does it verify which rules apply to individual files, as these depend on matching sections and local overrides.

> [!IMPORTANT]
> If MSBuild detects the configuration but Visual Studio does not apply its settings, open Tools > Options > Text Editor > General and ensure Follow project coding conventions is enabled. Then reopen the affected source file.

## Usage

Compatible IDEs automatically detect .editorconfig and apply its supported formatting and code analysis settings. Violations are reported according to their configured severity levels, although support for individual rules may vary between IDEs. Existing code is not automatically reformatted, so formatting tools, code fixes, or manual changes may be needed to comply with the configuration. When build-time analysis is enabled, supported diagnostics are also reported during builds.

### Shared Exceptions

The configuration includes predefined exceptions for common scenarios that may benefit from different analyzer settings. These scoped overrides help reduce unnecessary diagnostics while preserving the shared baseline elsewhere.

The table below outlines the scope and purpose of each exception.

| Scenario | Scope | Matching names | Purpose |
| --- | --- | --- | --- |
| Tests | Directories | `Tests`, `tests`, `*.Tests` | Accommodate testing conventions, including underscore-separated names and locally created test data. |
| Fakes | Directories | `Fakes`, `fakes` | Allow lightweight test doubles by relaxing selected validation and optimization rules. |
| DTOs | Directories | `DTOs`, `Dtos`, `dtos` | Support data transfer representations, including string-based URIs and writable collections. |
| EF Core migrations | Directories | `Migrations`, `migrations` | Reduce unnecessary diagnostics in scaffolded migration code. |
| Dependency injection | Files | `DependencyInjection.cs` | Allow unused registration return values and namespaces independent of folder structure. |

Exceptions apply based on file paths rather than the types declared within them. Directory-based exceptions apply to C# files in matching directories and their subdirectories. When multiple sections match a file, later sections override only the settings they explicitly redefine. For example, files in Tests/Fakes inherit the test exceptions, with any conflicting settings overridden by the Fakes section.

The complete set of exceptions is defined under Shared Exceptions in .editorconfig.

### Customization

The configuration provides a reusable baseline that can be adapted to meet project-specific requirements. Individual rules and shared exceptions can be customized without modifying the entire standard. Only settings that differ from the baseline need to be overridden.

#### Option 1 — Root configuration

For minor adjustments, add scoped overrides to the Project-Specific Overrides section at the end of the root .editorconfig. This approach keeps all settings in a single file and is suitable when only a few changes are needed.

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

This overrides CA1707 to warning for C# files in src/Example and its subdirectories. File paths are relative to the root .editorconfig.

#### Option 2 — Child configuration

For more extensive or independent adjustments, place a child .editorconfig in the directory containing the affected code. This keeps local overrides separate from the shared configuration while allowing them to inherit its settings.

```text
Repository/
├── .editorconfig              ← Shared baseline
└── src/
    └── Example/
        ├── .editorconfig      ← Local overrides
        └── Example.csproj
```

To apply the same override, add the following to the child .editorconfig:

```ini
[*.cs]
dotnet_diagnostic.CA1707.severity = warning
```

Here, [*.cs] matches C# files in the child configuration's directory and its subdirectories. Omit root = true to preserve inheritance from the parent .editorconfig. Only explicitly overridden settings change; all others remain inherited.

## License

This project is licensed under the MIT License.

The license text is also included at the end of .editorconfig to ensure that its copyright and permission notices accompany the configuration when reused. These notices must be retained when copying, modifying, or redistributing the file, as required by the license.
