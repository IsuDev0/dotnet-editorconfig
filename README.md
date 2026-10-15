# ⚙️ C# / .NET EditorConfig

![Version](https://img.shields.io/badge/version-1.0.0-555555?style=flat-square&labelColor=333333)&nbsp;
![.NET](https://img.shields.io/badge/.NET-10-555555?style=flat-square&labelColor=333333)&nbsp;
![C#](https://img.shields.io/badge/C%23-14-555555?style=flat-square&labelColor=333333)&nbsp;
[![License](https://img.shields.io/badge/license-MIT-555555?style=flat-square&labelColor=333333)](#license)

A reusable, opinionated coding standard for C#/.NET projects, defined in a single .editorconfig file. 
It brings together formatting, naming, code style, and code quality rules to promote consistency and maintainability across projects without configuring them from scratch. 
The configuration includes .NET analyzer settings and scoped exceptions for common scenarios and can be customized to suit individual project and framework requirements.

## Key Conventions

Consistent coding conventions make code easier to read, understand, and maintain. Even small decisions about formatting, naming, and syntax can influence how clearly code communicates its intent. This configuration takes a deliberate approach to these choices, balancing clarity, consistency, and conciseness without treating any particular style as universally correct.

The conventions below highlight some of these decisions, explaining their purpose and illustrating their application through practical examples. They reflect personal preferences rather than universal rules and can be [customized](#customization) to suit different projects or teams.

Examples focus on individual conventions and may omit surrounding code.

<details>
<summary><strong>Brace Style</strong> — K&amp;R</summary>

---

Opening braces are placed on the same line as the declaration or statement they belong to, following the K&R style. Unlike styles that place opening braces on separate lines, this approach reduces vertical space, keeps related code closer together, and allows more surrounding context to remain visible.

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

Braces are generally preferred for control statements because they make block boundaries explicit and help prevent mistakes when code is modified. However, in short, straightforward cases, such as a simple if statement with a single-line body, they may add unnecessary visual clutter without significantly improving readability. Omitting braces is therefore allowed in such cases, although their use remains recommended.

Braces are required when a control statement's header or body spans multiple lines. As statements become longer or more complex, explicit block boundaries make their structure easier to follow and reduce ambiguity.

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

For readability and easier debugging, braced control blocks should span multiple lines rather than be compressed into a single line. This keeps statements visually distinct and provides clearer locations for breakpoints. Compact property declarations may remain on one line. This recommendation is separate from the enforced brace requirement.

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

Instance fields, including readonly fields, and mutable static fields must be private. Exposing fields directly creates dependencies on a type's internal representation and allows mutable state to be modified without validation. Keeping fields private preserves encapsulation, while properties and methods provide controlled access to the underlying data.

Constants and static readonly fields are exempt from this restriction because they cannot be reassigned after initialization. They may be exposed directly when appropriate, although a readonly reference does not guarantee that the referenced object is immutable.

**Non-private fields**

```csharp
// Disallowed: exposes instance state directly.
public int Value;

// Disallowed: exposes shared mutable state.
internal static int TotalCount;
```

**Controlled access**

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
<summary><strong>Field Naming</strong> — Prefixes and casing</summary>

---

Private instance fields, including readonly fields, use `_camelCase`, while private mutable static fields use `s_camelCase`. These prefixes distinguish instance state from shared mutable state at a glance, without requiring explicit qualification.

Constants and static readonly fields use PascalCase regardless of accessibility. Unlike instance fields and mutable static fields, they may be exposed as part of a type's API when appropriate. This naming convention remains consistent across all accessibility levels.

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
private const int PrivateLimit = 100;

// Static readonly fields: PascalCase at every accessibility level.
public static readonly int PublicValue = 100;
internal static readonly int InternalValue = 100;
private static readonly int PrivateValue = 100;
```

</details>

<details>
<summary><strong>Expression &amp; Block Bodies</strong> — Simple, focused members</summary>

---

Expression bodies provide a concise way to express simple operations, while block bodies offer a more explicit structure for procedural code. The goal is to balance readability and conciseness rather than favor shorter syntax in every situation.

Members should remain focused on clear responsibilities, regardless of their body style. When an implementation becomes difficult to understand, extracting complex logic into a separate method may improve clarity. These are design considerations; analyzers enforce syntax preferences without evaluating the quality of an implementation.

**Expression bodies**

Expression bodies are required for properties, indexers, and accessors where possible, keeping straightforward operations concise without unnecessary block syntax. They are also preferred for operators when the implementation fits clearly on a single line.

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

**Block bodies**

Block bodies are preferred for constructors and methods, even when they contain only one statement. This provides a consistent structure for procedural operations, makes individual statements easier to distinguish, and allows implementations to grow without changing their overall layout.

```csharp
public Example(int value) {
    _value = value;
}

public int GetValue() {
    return _value;
}
```

**Local functions & lambdas**

Expression bodies are suggested for local functions and lambdas when their implementation fits clearly on a single line. This keeps small, self-contained operations concise without sacrificing readability. More complex implementations should prioritize clarity over compactness.

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
<summary><strong>Type Inference</strong> — Selective use of <code>var</code></summary>

---

Type inference with var is preferred for built-in types and when the initializer makes the type apparent, such as during object creation. In these cases, explicit type declarations often introduce unnecessary repetition without improving readability.

When the initializer does not clearly reveal the resulting type, explicit declarations are preferred. This makes the type immediately visible without requiring the reader to inspect method signatures or other parts of the code. Since var uses compile-time type inference, the choice concerns readability rather than type safety.

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

This configuration can be adopted in both new and existing C#/.NET projects. Starting with a new project makes it easier to follow the conventions from the outset, while existing codebases may require code changes or project-specific overrides to accommodate the standard.

### Environment

The configuration was developed and tested with the following tools and versions:

- **Language and framework:** C# 14 / .NET 10
- **Target IDE:** Visual Studio 2026+
- **Tested SDK:** .NET SDK 10.0.401 (CLI)

> [!NOTE]
> The SDK version indicates the toolchain used for verification. .editorconfig does not select or install an SDK.

**Analyzer dependencies**

- [Microsoft.CodeAnalysis.NetAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers) (included in the .NET SDK)
- [Microsoft.CodeAnalysis.CSharp.CodeStyle](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle) (included in the .NET SDK)

Visual Studio is not required for command-line analysis. Other IDEs may differ in their support for individual settings and diagnostics.

### 1. Add the configuration

Place .editorconfig in the repository root to make its rules available to all projects within the repository. The root = true setting prevents inheritance from parent directories, ensuring that external configurations do not interfere with the standard. Nested .editorconfig files can still override individual settings when needed.

If the repository already contains a root .editorconfig, review and merge the existing settings rather than replacing the file outright. This helps preserve project-specific conventions and avoid unintended changes.

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

The .NET 10 SDK enables code quality analyzers by default, but code style diagnostics are not generally included in builds. Enabling build-time code style analysis helps enforce consistent coding conventions beyond the editor and detect violations during compilation.

To enable supported code style diagnostics, add EnforceCodeStyleInBuild to the project's .csproj file or a root Directory.Build.props to apply it across multiple projects. If the file already exists, add the property to an existing <PropertyGroup> rather than duplicating the surrounding structure.

```xml
<Project>
  <PropertyGroup>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>
</Project>
```

Diagnostics configured as errors can cause the build to fail, helping identify violations before code is integrated or distributed.

> [!NOTE]
> Visual Studio may skip analyzer checks in builds triggered by Run or Debug. As a result, an application may launch even when an explicit Build or Rebuild would fail due to analyzer errors. To disable this optimization, set OptimizeImplicitlyTriggeredBuild to false, as described under Additional build settings.

<details>
<summary><strong>Additional build settings</strong></summary>

---

The following optional settings provide additional control over analyzer behavior and build enforcement. Uncomment only the settings you need and add them to your `.csproj` or `Directory.Build.props`, preserving the existing project structure.

```xml
<Project>
  <PropertyGroup>
    <!-- Explicitly enable .NET code quality analyzers -->
    <!-- <EnableNETAnalyzers>true</EnableNETAnalyzers> -->

    <!-- Use .NET 10 analysis rules independently of the SDK version -->
    <!-- <AnalysisLevel>10.0</AnalysisLevel> -->

    <!-- Run analyzers in Visual Studio Run/Debug builds -->
    <!-- <OptimizeImplicitlyTriggeredBuild>false</OptimizeImplicitlyTriggeredBuild> -->

    <!-- Treat compiler and analyzer warnings as errors -->
    <!-- <TreatWarningsAsErrors>true</TreatWarningsAsErrors> -->
  </PropertyGroup>

  <!-- Expose project properties to IDE0130 during CLI builds -->
  <!--
  <ItemGroup>
    <CompilerVisibleProperty Include="RootNamespace" />
    <CompilerVisibleProperty Include="ProjectDir" />
  </ItemGroup>
  -->
</Project>
```

The `CompilerVisibleProperty` entries provide the project information required by [`IDE0130`](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/style-rules/ide0130) to verify namespace and folder alignment during command-line builds.

Disabling `OptimizeImplicitlyTriggeredBuild` ensures analyzer execution during Visual Studio Run/Debug builds but may increase application launch times.

For more information, see the [MSBuild properties reference](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props).

</details>

### 3. Verify file detection

Before relying on the configuration, verify that MSBuild detects the repository's .editorconfig for the intended project. From the repository root, run:

```sh
dotnet msbuild src/Example/Example.csproj -getItem:EditorConfigFiles
```

Check the EditorConfigFiles list in the output:

- **File found:** An Identity entry points to the expected .editorconfig. Other configuration files may also be listed.
- **File not found:** The list is empty ("EditorConfigFiles": []) or does not contain the expected file.

If the configuration is missing, verify its name and location, and ensure the command targets the correct project. The .editorconfig must be located in the project directory or one of its parent directories.

This command checks file discovery without building the project or modifying source code. It does not verify which rules apply to individual files, as these depend on matching sections and local overrides.

> [!IMPORTANT]
> If the configuration is detected but Visual Studio does not apply its settings, open Tools > Options > Text Editor > General and ensure Follow project coding conventions is enabled. Then reopen the affected source file.

## Usage

Compatible IDEs automatically detect .editorconfig and use its supported settings for formatting and code analysis. Analyzer diagnostics highlight violations at their configured severity levels, although support for individual rules may vary between IDEs.

Adding .editorconfig does not automatically reformat existing code. Use the IDE's formatting tools to apply formatting conventions, while analyzer violations may require separate code fixes or manual changes. With build-time analysis enabled, supported diagnostics are also reported during compilation.

### Shared Exceptions

Not all code benefits from the same analyzer rules. Tests, fakes, DTOs, generated migrations, and dependency injection code serve different purposes and may require different conventions. This configuration provides scoped exceptions to accommodate these scenarios, reducing unnecessary diagnostics while preserving the baseline standards elsewhere.

The table below summarizes the scope and purpose of each exception. Directory-based exceptions apply to all C# files within matching directories and their subdirectories.

| Scenario | Scope | Matching names | Purpose |
| --- | --- | --- | --- |
| Tests | Directories | `Tests`, `tests`, `*.Tests` | Accommodate testing conventions, including underscore-separated names and locally created test data. |
| Fakes | Directories | `Fakes`, `fakes` | Allow lightweight test doubles by relaxing selected validation and optimization rules. |
| DTOs | Directories | `DTOs`, `Dtos`, `dtos` | Support data transfer representations, including string-based URIs and writable collections. |
| EF Core migrations | Directories | `Migrations`, `migrations` | Reduce unnecessary diagnostics in scaffolded migration code. |
| Dependency injection | Files | `DependencyInjection.cs` | Allow unused registration return values and namespaces independent of folder structure. |

Exceptions are applied based on file paths, regardless of the types declared within them. When scopes overlap, later sections override only the settings they explicitly redefine. For example, files in Tests/Fakes inherit the test exceptions before applying the Fakes overrides.

The complete set of overrides is defined under Shared Exceptions in .editorconfig. Adjust the selectors to match your repository's directory and file naming conventions.

### Customization

The configuration is intended as a reusable starting point rather than a fixed set of requirements. Individual rules and shared exceptions can be adjusted to suit project-specific needs without redefining the entire standard. Only settings that differ from the baseline need to be overridden.

#### Option 1 — Root configuration

---

The simplest approach is to add project-specific overrides under Project-Specific Overrides at the end of the root .editorconfig. This keeps all configuration rules in a single file and is suitable for projects that require only a few adjustments.

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

This changes CA1707 to a warning for C# files in src/Example and its subdirectories. Paths are relative to the root .editorconfig, and when sections overlap, later definitions take precedence for the settings they redefine.

#### Option 2 — Child configuration

---

For projects with more extensive or independent requirements, a child .editorconfig can keep local overrides alongside the affected code. This separates project-specific conventions from the shared baseline, making both easier to maintain.

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

The [*.cs] section applies to C# files in the child configuration's directory and its subdirectories. Omit root = true to preserve inheritance from the parent configuration. Only explicitly overridden settings change; all remaining rules continue to follow the shared baseline.

## License

This project is licensed under the MIT License.

A copy of the license is also included at the end of .editorconfig to ensure it accompanies the configuration when reused in other projects. When copying, modifying, or redistributing the file, retain the copyright and permission notices as required by the license.
