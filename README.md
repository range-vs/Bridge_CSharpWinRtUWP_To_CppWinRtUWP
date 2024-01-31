# Consuming C# WinRT UWP components from C++ WinRT UWP

Based on [this](https://github.com/asklar/WinRTComponent) repository

This project provides a sample for how to call a C# WinRT UWP component from a C++/WinRT UWP app.

The default out-of-the-box solution in Visual Studio is faulty in that it builds, but then fails at runtime with a cryptic exception:

```
FUSION_E_REF_DEF_MISMATCH
0x80131040  The located assembly's manifest definition does not match the assembly reference
```

Here's how to get it to work:
1. Right click on the VCXProj file ➡ Manage NuGet Packages.
2. Search for **Microsoft.Net.Native.Compiler**, and install it
3. Then add the following properties to the VCXProj
```
  <PropertyGroup>
    <UseDotNetNativeToolchain>true</UseDotNetNativeToolchain>
    <DotNetNativeVersion>2.2.12-rel-31116-00</DotNetNativeVersion>
    <NugetPath>$(ProgramFiles)\Microsoft SDKs\UWPNuGetPackages</NugetPath>
  </PropertyGroup>
```
(Replace the `2.2.12-rel-31116-00` version above with the version from the .net native compiler NuGet Package. Used path: $(ProgramFiles)\Microsoft SDKs\UWPNuGetPackages\microsoft.net.native.compiler\your_version\)

P.S.: 
Rebuild may cause an error:
```
Error   MSB3816   Loading assembly "C:\Program Files (x86)\Microsoft SDKs\UWPNuGetPackages\runtime.win10-x86-aot.microsoft.netcore.universalwindowsplatform\6.2.14\runtimes\win10-x86-aot\lib\uap10.0.15138\System.Diagnostics.DiagnosticSource.dll" failed. System.IO.FileNotFoundException: Could not load file or assembly 'System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The system cannot find the file specified.
File name: 'System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'
   at System.ModuleHandle.ResolveType(RuntimeModule module, Int32 typeToken, IntPtr* typeInstArgs, Int32 typeInstCount, IntPtr* methodInstArgs, Int32 methodInstCount, ObjectHandleOnStack type)
   at System.ModuleHandle.ResolveTypeHandleInternal(RuntimeModule module, Int32 typeToken, RuntimeTypeHandle[] typeInstantiationContext, RuntimeTypeHandle[] methodInstantiationContext)
   at System.Reflection.RuntimeModule.ResolveType(Int32 metadataToken, Type[] genericTypeArguments, Type[] genericMethodArguments)
   at System.Reflection.CustomAttribute.FilterCustomAttributeRecord(CustomAttributeRecord caRecord, MetadataImport scope, Assembly& lastAptcaOkAssembly, RuntimeModule decoratedModule, MetadataToken decoratedToken, RuntimeType attributeFilterType, Boolean mustBeInheritable, Object[] attributes, IList derivedAttributes, RuntimeType& attributeType, IRuntimeMethodInfo& ctor, Boolean& ctorHasParameters, Boolean& isVarArg)
   at System.Reflection.CustomAttribute.GetCustomAttributes(RuntimeModule decoratedModule, Int32 decoratedMetadataToken, Int32 pcaCount, RuntimeType attributeFilterType, Boolean mustBeInheritable, IList derivedAttributes, Boolean isDecoratedTargetSecurityTransparent)
   at System.Reflection.CustomAttribute.GetCustomAttributes(RuntimeAssembly assembly, RuntimeType caType)
   at System.Attribute.GetCustomAttributes(Assembly element, Type attributeType, Boolean inherit)
   at System.Attribute.GetCustomAttribute(Assembly element, Type attributeType, Boolean inherit)
   at System.Reflection.CustomAttributeExtensions.GetCustomAttribute[T](Assembly element)
   at Microsoft.Build.Tasks.ProcessResourceFiles.ReadAssemblyResources(String name, String outFileOrDir)

WRN: Assembly binding logging is turned OFF.
To enable assembly bind failure logging, set the registry value [HKLM\Software\Microsoft\Fusion!EnableLog] (DWORD) to 1.
Note: There is some performance penalty associated with assembly bind failure logging.
To turn this feature off, remove the registry value [HKLM\Software\Microsoft\Fusion!EnableLog].
   CoreApp8   C:\Program Files\Microsoft Visual Studio\2022\Community\MSBuild\Microsoft\VisualStudio\v17.0\AppxPackage\Microsoft.AppXPackage.Targets   1437      

```
Use the Build command and the problem will go away. The reason for this behavior has not been established.