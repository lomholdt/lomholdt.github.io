---
title: Convert to file scoped namespaces in the entire solution using .editorconfig
categories:
  - Blog
tags:
  - csharp
  - editorconfig
  - file scoped namespaces
---

File scoped namespaces is a new feature in C# 10, allowing a simpler format for defining namespaces.

Old way:

```csharp
namespace MyNamespace
{
    public class MyClass {}
}
```

New way:

```csharp
namespace MyNamespace;

public class MyClass {}
```

In VS2022 you are able to convert a file to file scoped namespaces using intellisense, but if you want to convert a full project/solution theres still not direct support for that (yet!).

However if you are using `.editorconfig` and `dotnet format` you can add the following line:

```editorconfig
csharp_style_namespace_declarations = file_scoped:warning
```

Now when you run `dotnet format` it will format the entire solution to file scoped namespaces!

> ProTip: The `dotnet format` command is built into the .NET SDK as of .NET 6
