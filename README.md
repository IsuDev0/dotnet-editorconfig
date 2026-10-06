# ⚙️ C# / .NET EditorConfig

![Version](https://img.shields.io/badge/version-1.0.0-555555?style=flat-square&labelColor=333333)&nbsp;
![.NET](https://img.shields.io/badge/.NET-10-555555?style=flat-square&labelColor=333333)&nbsp;
![C#](https://img.shields.io/badge/C%23-14-555555?style=flat-square&labelColor=333333)&nbsp;
[![EditorConfig license](https://img.shields.io/badge/.editorconfig%20license-MIT-555555?style=flat-square&labelColor=333333)](#license)

A shared, opinionated coding standard for C#/.NET projects, defined in a reusable `.editorconfig` file.
It brings together formatting, naming, code style, and code quality rules to support consistency and maintainability across different codebases.
The configuration includes .NET analyzer settings and scoped exceptions for common scenarios,
and can be adapted to the requirements of individual projects and frameworks.

## Environment

The configuration is designed for the following environment:

- **Language and framework:** C# 14 / .NET 10
- **Target IDE:** Visual Studio 2026+
- **Tested SDK:** .NET SDK 10.0.401 (CLI)

The SDK version identifies the toolchain used for verification. The `.editorconfig` file itself does not select or install an SDK.

**Analyzer dependencies**

- [Microsoft.CodeAnalysis.NetAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.NetAnalyzers) (included in the .NET SDK)
- [Microsoft.CodeAnalysis.CSharp.CodeStyle](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle) (included in the .NET SDK)

Formatting settings are applied by an EditorConfig-compatible editor or formatter.
[Analyzer diagnostics](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/overview) are reported in the IDE and, for supported rules, during builds with the setup in [Quick Start](#quick-start).

Visual Studio is not required for command-line analysis.
Other IDEs may differ in their support for individual settings and diagnostics, and SDK updates can change analyzer behavior.

## Quick Start

The standard is easiest to adopt in new projects, where its conventions can be followed from the start.
Existing projects can also use it, but may require code changes or adjustments to the configuration.
The following steps cover installation, build-time analysis, and a quick check that the configuration is active.

### 1. Add the configuration

Place the [`.editorconfig`](.editorconfig) file in the repository root, above the projects that should inherit the standard.
If an `.editorconfig` already exists, merge the settings to preserve project-specific configuration.

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

The baseline sets `root = true`, which stops inheritance from parent directories.
Child `.editorconfig` files can still override its settings.

Visual Studio normally detects EditorConfig settings automatically.
If settings are not being applied, open **Tools > Options**, search for
[**Follow project coding conventions**](https://learn.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options#troubleshoot-editorconfig-settings),
and make sure it is enabled. Reopen the affected source file to load the updated settings.

### 2. Enable build-time analysis

To run the SDK analyzers during builds, add the settings below to a project's `.csproj` file.
To share them across projects, use a `Directory.Build.props` file in the repository root instead.
In an existing file, merge the entries into its `<Project>` element.

```xml
<Project>
  <PropertyGroup>
    <EnableNETAnalyzers>true</EnableNETAnalyzers>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
    <AnalysisLevel>10.0</AnalysisLevel>
  </PropertyGroup>

  <ItemGroup>
    <CompilerVisibleProperty Include="RootNamespace" />
    <CompilerVisibleProperty Include="ProjectDir" />
  </ItemGroup>
</Project>
```

`EnableNETAnalyzers` enables SDK code quality analysis, while `EnforceCodeStyleInBuild` adds supported code style checks to builds.
`AnalysisLevel` selects the .NET 10 analysis level without pinning the SDK version.

The two `CompilerVisibleProperty` entries provide the project information needed by
[`IDE0130`](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/style-rules/ide0130) to check whether namespaces match the folder structure during command-line builds.

### 3. Confirm the configuration is active

For a quick check, temporarily remove `internal` from an existing top-level class in the project, leaving its namespace and body unchanged.
For example, change:

```csharp
internal class ConfigurationCheck { }
```

to:

```csharp
class ConfigurationCheck { }
```

The editor should report **IDE0040 — Add accessibility modifiers** as an error.
With the build settings above, the same diagnostic should appear when running this command from the repository root:

```sh
dotnet build Example.slnx --no-incremental
```

Replace `Example.slnx` with the actual solution or project path.
The missing modifier deliberately triggers a build error for this check.
Restore `internal` afterwards and confirm that `IDE0040` disappears.

This confirms that one configured rule is active. Further verification and troubleshooting are covered in [Configuration](docs/configuration.md).

## Shared Exceptions

The baseline includes predefined exceptions that adjust selected rules for common kinds of code.
Most exceptions are scoped by directory name and apply to C# files in those directories and their subdirectories:

- `Tests`, `tests`, and `*.Tests` — automated tests.
- `Fakes` and `fakes` — test doubles used to replace dependencies.
- `DTOs`, `Dtos`, and `dtos` — data transfer objects.
- `Migrations` and `migrations` — EF Core migrations.

Dependency injection exceptions apply to files named `DependencyInjection.cs`.

The selectors and rule overrides are defined in the `Shared Exceptions` section of [`.editorconfig`](.editorconfig).
Adjust the selectors if the repository uses different directory or file names.

## Customization

Use scoped overrides to adapt formatting, naming, code style, or analyzer settings to a project.
Overrides can adjust both the general rules and the [shared exceptions](#shared-exceptions).
Include only the settings that need to differ from the baseline.

The examples below show two ways to apply the same change.

### Root configuration

Use the `Project-Specific Overrides` section at the end of the root `.editorconfig` to keep project adjustments in one place.
Start each group with a file selector that defines its scope.

This example lowers `CA1707`, which checks for underscores in identifiers, to a warning for C# files in `src/Example` and its subdirectories:

```ini
[src/Example/**.cs]
dotnet_diagnostic.CA1707.severity = warning
```

Paths are relative to the `.editorconfig` file.
Within that file, later matching sections override earlier values for the same setting.

### Child configuration

Use a child `.editorconfig` to keep overrides alongside the project or directory they affect:

```text
Repository/
├── .editorconfig              ← Shared baseline
└── src/
    └── Example/
        ├── .editorconfig      ← Local overrides
        └── Example.csproj
```

With the child file in `src/Example`, the equivalent override is:

```ini
[*.cs]
dotnet_diagnostic.CA1707.severity = warning
```

Selectors are relative to the child file, so `[*.cs]` covers C# files in that directory and all its subdirectories.

Omit `root = true` so the child continues to inherit the baseline.
For matching files, its settings override inherited values. All other settings continue to be inherited.

## Documentation

For more information about the rules included in this standard, why they were chosen, and how to adapt them to specific projects, see:

- [Coding conventions](docs/conventions.md) — formatting, naming, and code style choices and their rationale.
- [Code analysis](docs/analysis.md) — analyzer rules, severity levels, and API scopes.
- [Configuration](docs/configuration.md) — scoped exceptions, inheritance, and project-specific overrides.

## License

The [`.editorconfig`](.editorconfig) file is licensed under the [MIT License](https://opensource.org/license/mit).
The full license text is embedded at the end of the file so it stays with the configuration when copied into another repository.
Retain the copyright and license notice when copying or redistributing the file, including modified versions.

This license applies to `.editorconfig` only. It does not cover the repository's documentation or code examples.
