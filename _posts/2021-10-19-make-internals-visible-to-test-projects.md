---
title: Make Internals Visible to Test Projects
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
  <InternalsVisibleTo Include="../../test/Project.Name.UnitTest" />
</ItemGroup>
```

> Remember to specify the correct path to your test project where the internals should be made visible
