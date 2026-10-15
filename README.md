# ⚙️ C# / .NET EditorConfig

![Version](https://img.shields.io/badge/version-1.0.0-555555?style=flat-square&labelColor=333333)&nbsp;
![.NET](https://img.shields.io/badge/.NET-10-555555?style=flat-square&labelColor=333333)&nbsp;
![C#](https://img.shields.io/badge/C%23-14-555555?style=flat-square&labelColor=333333)&nbsp;
[![License](https://img.shields.io/badge/license-MIT-555555?style=flat-square&labelColor=333333)](#license)

A reusable, opinionated coding standard for C#/.NET projects, defined in a single `.editorconfig` file. It brings together formatting, naming, code style, and code quality rules to promote consistency and maintainability without redefining the same standards for every project. The configuration includes .NET analyzer settings and scoped exceptions for common scenarios and can be customized to suit individual project and framework requirements.

## Key Conventions

Consistent coding conventions make code easier to read, understand, and maintain. Even small decisions about formatting, naming, and syntax affect how clearly code communicates its intent. This configuration approaches those decisions deliberately, balancing clarity, consistency, and conciseness without treating any particular style as universally correct.

The sections below highlight selected conventions, explain why they were chosen, and illustrate their use through practical examples. These conventions can be [customized](#customization) to suit different projects or teams. Examples focus on individual conventions and may omit surrounding code.

<details>
<summary><strong>Brace Style</strong> — K&amp;R</summary>

---

Opening braces are placed on the same line as the declaration or statement they belong to, following the K&R style. Compared with styles that place opening braces on separate lines, this approach uses less vertical space, keeps related code closer together, and leaves more surrounding context visible.

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

Braces are generally preferred for control statements because they make block boundaries explicit and help prevent mistakes when code is modified. In short, straightforward cases, however, they may add visual clutter without significantly improving readability. Omitting braces is therefore allowed in such cases, although using them remains recommended.

Braces are required when a control statement's header or body spans multiple lines. Explicit block boundaries make longer statements easier to follow, especially when line wrapping makes their structure less obvious.

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

For readability and easier debugging, braced control blocks should span multiple lines rather than be compressed into a single line. This keeps statements visually distinct and provides clearer locations for breakpoints. Compact property declarations may remain on one line. This formatting recommendation is separate from the enforced brace requirement.

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

Instance fields, including `readonly` fields, and mutable static fields must be private. Exposing them directly creates dependencies on a type's internal representation and can allow state to be modified without validation. Keeping these fields private preserves encapsulation, while properties and methods provide controlled access to the underlying data.

Constants and `static readonly` fields are exempt from this restriction because callers cannot reassign their values. They may be exposed directly when appropriate, although a `readonly` reference does not guarantee that the referenced object is immutable.

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

Private instance fields, including `readonly` fields, use `_camelCase`, while private mutable static fields use `s_camelCase`. These prefixes distinguish instance state from shared mutable state at a glance, without requiring explicit qualification.

Constants and `static readonly` fields use `PascalCase` regardless of accessibility. Unlike instance fields and mutable static fields, they may be exposed as part of a type's API when appropriate. A consistent naming convention makes their role recognizable at every accessibility level.

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

Expression bodies keep simple operations concise, while block bodies provide a more explicit structure for procedural code. The goal is to balance readability and conciseness rather than favor the shortest syntax in every situation.

Members should have clear, focused responsibilities regardless of body style. If an implementation becomes difficult to understand, extracting complex logic into a separate method may improve clarity. This is a design guideline: analyzers check syntax preferences, not whether a member has an appropriate responsibility.

**Expression bodies**

Expression bodies are required for properties, indexers, and accessors where possible, avoiding unnecessary block syntax for straightforward operations. They are also preferred for operators when the expression fits clearly on one line.

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

Block bodies are preferred for constructors and methods, even when they contain a single statement. Their consistent structure keeps statements distinct and allows implementations to grow without changing their overall layout.

```csharp
public Example(int value) {
    _value = value;
}

public int GetValue() {
    return _value;
}
```

**Local functions & lambdas**

Expression bodies are suggested for local functions and lambdas when they fit clearly on one line. This keeps small, self-contained operations concise; more complex implementations should prioritize clarity over compactness.

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

Type inference with `var` is preferred for built-in types and when the initializer makes the type apparent, such as during object creation. In these cases, an explicit type often repeats information without improving readability.

When the initializer does not clearly reveal the resulting type, an explicit declaration is preferred. This makes the type visible without requiring readers to inspect method signatures or other code. Because `var` uses compile-time type inference, the choice concerns readability rather than type safety.

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

### Environment

The configuration targets the following environment and has been verified with the listed SDK:

- **Language and framework:** C# 14 / .NET 10
- **Target IDE:** Visual Studio 2026+
- **Tested SDK:** .NET SDK 10.0.401 (CLI)

> [!NOTE]
> The SDK version identifies the toolchain used for verification. `.editorconfig` does not select or install an SDK.

**Analyzer dependencies**

- [Microsoft.CodeAnalysis.NetAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers) (included in the .NET SDK)
- [Microsoft.CodeAnalysis.CSharp.CodeStyle](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle) (included in the .NET SDK)

Visual Studio is not required for command-line analysis. Other IDEs may differ in their support for individual settings and diagnostics.

### 1. Add the configuration

Place [`.editorconfig`](.editorconfig) in the repository root so its rules apply to matching files throughout the repository. The `root = true` setting stops inheritance from parent `.editorconfig` files, while nested configurations can still override individual settings.

If the repository already contains a root `.editorconfig`, review and merge its settings rather than replacing it outright. This preserves project-specific conventions and avoids unintended changes.

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

The .NET 10 SDK enables code quality analyzers by default, but code style diagnostics are not generally included in builds. Enabling build-time code style analysis helps enforce conventions beyond the editor and detect violations during compilation.

To include supported code style diagnostics in builds, set `EnforceCodeStyleInBuild` to `true` in the project's `.csproj` or a root `Directory.Build.props` to share the setting across projects. If the file already exists, add the property to an existing `<PropertyGroup>`.

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

These optional settings provide more control over analyzer behavior and build enforcement. Uncomment only those you need and add them to the same `.csproj` or `Directory.Build.props`, preserving the existing project structure.

```xml
<Project>
  <PropertyGroup>
    <!-- Explicitly enable .NET code quality analyzers -->
    <!-- <EnableNETAnalyzers>true</EnableNETAnalyzers> -->

    <!-- Use the .NET 10 analyzer rule set with newer SDK versions -->
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

The `CompilerVisibleProperty` entries supply the project information [`IDE0130`](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/style-rules/ide0130) needs to check namespace and folder alignment during command-line builds.

Setting `OptimizeImplicitlyTriggeredBuild` to `false` may increase application launch times in Visual Studio.

For details, see the [MSBuild properties reference](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props).

</details>

### 3. Verify file detection

Before relying on the configuration, verify that MSBuild detects the intended `.editorconfig` for your project. From the repository root, run:

```sh
dotnet msbuild src/Example/Example.csproj -getItem:EditorConfigFiles
```

Check the `EditorConfigFiles` list in the output:

- **File found:** An `Identity` entry points to the expected `.editorconfig`. Other configuration files may also be listed.
- **File not found:** The list is empty (`"EditorConfigFiles": []`) or does not include the expected file.

If the file is missing, check its name, location, and the project targeted by the command. For automatic discovery, the file must be named `.editorconfig` and placed in the project directory or one of its parent directories.

This command checks file discovery without building the project or changing source code. It does not verify which rules apply to individual files; those depend on matching sections and local overrides.

> [!IMPORTANT]
> If the file is detected but Visual Studio does not apply its settings, open **Tools > Options** and ensure [**Follow project coding conventions**](https://learn.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options#troubleshoot-editorconfig-settings) is enabled. Then reopen the affected source file.

## Usage

Compatible IDEs detect `.editorconfig` automatically and apply the settings they support for formatting and code analysis. Analyzer diagnostics highlight violations at their configured severity levels, although support for individual rules may vary between IDEs.

Adding `.editorconfig` does not automatically reformat existing code. Use your IDE's formatting tools to apply formatting conventions; other analyzer violations may require code fixes or manual changes. With build-time analysis enabled, supported diagnostics are also reported during builds.

### Shared Exceptions

Not all code benefits from the same analyzer rules. Tests, fakes, DTOs, EF Core migrations, and dependency injection code serve different purposes and may need different conventions. Scoped exceptions accommodate these cases, reducing unnecessary diagnostics while preserving the baseline elsewhere.

The table summarizes each exception's scope and purpose. Directory-based exceptions apply to C# files in matching directories and their subdirectories.

| Scenario | Scope | Matching names | Purpose |
| --- | --- | --- | --- |
| Tests | Directories | `Tests`, `tests`, `*.Tests` | Accommodate testing conventions, including underscore-separated names and locally created test data. |
| Fakes | Directories | `Fakes`, `fakes` | Allow lightweight test doubles by relaxing selected validation and optimization rules. |
| DTOs | Directories | `DTOs`, `Dtos`, `dtos` | Support data transfer representations, including string-based URIs and writable collections. |
| EF Core migrations | Directories | `Migrations`, `migrations` | Reduce unnecessary diagnostics in scaffolded migration code. |
| Dependency injection | Files | `DependencyInjection.cs` | Allow unused registration return values and namespaces independent of folder structure. |

Exceptions apply based on file paths, regardless of the types declared within them. When scopes overlap, later sections override only the settings they explicitly redefine. For example, files in `Tests/Fakes` inherit the test exceptions before applying the `Fakes` overrides.

The complete overrides are defined under `Shared Exceptions` in [`.editorconfig`](.editorconfig). Adjust the selectors to match your repository's directory and file naming conventions.

### Customization

The configuration is a reusable starting point rather than a fixed set of requirements. Individual rules and [shared exceptions](#shared-exceptions) can be adjusted to suit project-specific needs without redefining the entire standard. Only settings that differ from the baseline need to be overridden.

#### Option 1 — Root configuration

The simplest approach is to add scoped overrides under `Project-Specific Overrides` at the end of the root `.editorconfig`. This keeps all rules in one file and works well when only a few adjustments are needed.

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

This sets `CA1707` to a warning for C# files in `src/Example` and its subdirectories. Paths are relative to the root `.editorconfig`. When sections overlap, later definitions take precedence for the settings they redefine.

#### Option 2 — Child configuration

A child `.editorconfig` keeps local overrides alongside the affected code. This is useful when individual projects need more extensive or independent settings, while keeping the shared baseline separate.

```text
Repository/
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

Here, `[*.cs]` matches C# files in that directory and its subdirectories. Omit `root = true` so the child inherits the parent configuration. Only explicitly overridden settings change; all others remain inherited.

## License

This project is licensed under the [MIT License](LICENSE).

A copy of the license is also included at the end of [`.editorconfig`](.editorconfig) so it accompanies the configuration when reused in other projects. When copying, modifying, or redistributing the file, retain the copyright and permission notices as required by the license.
