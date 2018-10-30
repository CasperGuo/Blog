---
layout: post
title: C# Blank Form Problem
date: 2018-10-30 08:38:38
categories: Program
tags:
 - Program
 - C#
 - winForm 
---

##Visual Studio Design View - form is blank

Your project file has become invalid.

A valid project entry for a form looks like this:
```xml
<Compile Include="Form1.cs">
  <SubType>Form</SubType>
</Compile>
<Compile Include="Form1.Designer.cs">
  <DependentUpon>Form1.cs</DependentUpon>
</Compile>
```

Yours though is missing the DependentUpon line - which is why the code and designer files appear separately in the project, instead of being connected:

```xml
<Compile Include="mainForm.cs">
  <SubType>Form</SubType>
</Compile>
<Compile Include="mainForm.Designer.cs" />
```

If you add in the missing line, the form displays correctly in design mode:

```xml
<Compile Include="mainForm.cs">
  <SubType>Form</SubType>
</Compile>
<Compile Include="mainForm.Designer.cs">
  <DependentUpon>mainForm.cs</DependentUpon>
</Compile>
```

And to tidy up the resource file:

```xml
<EmbeddedResource Include="mainForm.resx">
  <DependentUpon>mainform.cs</DependentUpon>
</EmbeddedResource>
```

In order to fix this, you can just edit the csproj in an editor, or to do it in Visual Studio:

Back up the files  
Right-click the project and choose "Unload Project"  
Right-click the unloaded project and choose "Edit [ProjectName].csproj"  
Make your changes, and close the file.  
Right-click the unloaded project and choose "Reload Project"  

