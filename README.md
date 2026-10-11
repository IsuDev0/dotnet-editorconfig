# ⚙️ C# / .NET EditorConfig

![Version](https://img.shields.io/badge/version-1.0.0-555555?style=flat-square&labelColor=333333)&nbsp;
![.NET](https://img.shields.io/badge/.NET-10-555555?style=flat-square&labelColor=333333)&nbsp;
![C#](https://img.shields.io/badge/C%23-14-555555?style=flat-square&labelColor=333333)&nbsp;
[![License](https://img.shields.io/badge/license-MIT-555555?style=flat-square&labelColor=333333)](#license)

A shared, opinionated coding standard for C#/.NET projects, defined in a reusable `.editorconfig` file.
It brings together formatting, naming, code style, and code quality rules to support consistency and maintainability across different codebases.
The configuration includes .NET analyzer settings and scoped exceptions for common scenarios,
and can be adapted to the requirements of individual projects and frameworks.


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

If the file is missing, confirm that it is named `.editorconfig`, is located above the project directory, and that the command targets the intended project.

This command checks file discovery without building the project or modifying source code. The settings applied to each source file depend on matching sections and local overrides.

> [!IMPORTANT]
> If the file is found but Visual Studio does not apply its settings, open **Tools > Options** and make sure
> [**Follow project coding conventions**](https://learn.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options#troubleshoot-editorconfig-settings)
> is enabled. Then reopen the affected source file.


## Usage

Once `.editorconfig` is in place, compatible IDEs should detect it automatically and use its supported settings. Continue writing code as usual, following the formatting preferences and analyzer diagnostics shown in the editor.

Use the IDE's formatting command to apply formatting to existing code. Adding the file does not automatically rewrite existing source files.

With build-time analysis enabled, supported analyzer diagnostics are also reported when building the project.


## License

This repository is licensed under the [MIT License](LICENSE).

A copy of the license is included at the end of [`.editorconfig`](.editorconfig) so it remains available when the configuration is copied into another project. Retain the copyright and license notice when copying or redistributing the file, including modified versions.
