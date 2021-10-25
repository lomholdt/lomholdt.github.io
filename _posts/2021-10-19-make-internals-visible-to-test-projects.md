---
title: Make Internals Visible to Test Projects
categories:
  - Blog
tags:
  - csharp
---

# How to make internals visible to test projects

Put the following MSBuild task into your `Directory.Build.props` in your src folder.

```xml
<Project>

  <Target Name="InternalsVisibleToTask" BeforeTargets="GenerateAdditionalSources" Condition="@(InternalsVisibleTo) != ''">
    <ItemGroup>
      <AssemblyAttribute Include="System.Runtime.CompilerServices.InternalsVisibleTo">
        <_Parameter1>%(InternalsVisibleTo.Identity)</_Parameter1>
      </AssemblyAttribute>
    </ItemGroup>
  </Target>

</Project>
```

In the `.csproj` for the project that contains the internal class add the following:

```xml
<ItemGroup>
  <InternalsVisibleTo Include="Project.Name.UnitTest" />
</ItemGroup>
```

> Remember to specify the correct test project where the internals should be made visible
