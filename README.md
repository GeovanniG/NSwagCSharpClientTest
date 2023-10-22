# An NSwag C# Client Test Project

The objective of this project was to use NSwag to auto-generate a C# Client based off of an Open API document on build. 

To acheive this outcome, we relied on the [NSwag.MSBuild](https://www.nuget.org/packages/NSwag.MSBuild) NuGet pacakage. This package allows a project to generate either a TypeScript or C# client from an Open API document on build.

NSwag.MSBuild needs details to on how to generate the C# client. These details can be configured using a [NSwag Configuration Document](https://github.com/RicoSuter/NSwag/wiki/NSwag-Configuration-Document). The NSwag Configuration Document is created using [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). For example, our document for this project can be found in NSwagCSharpClientTest/Client/redocly.nswag. This document generates a C# Client using the specified `namespace` and outputs the client to the desired `output` path.

With the NSwag Configuration Document defined, NSwag.MSBuild needs the path of the file along with the .NET version used by our project. These details can be added to the csproj file. 

Adding these details to our project file, we end up with the following:

```
 <PropertyGroup>
	<RunPostBuildEvent>OnBuildSuccess</RunPostBuildEvent>
  </PropertyGroup>

  <Target Name="NSwag" AfterTargets="PostBuildEvent" Condition=" '$(Configuration)' == 'Debug' ">
    <Exec WorkingDirectory="$(ProjectDir)" EnvironmentVariables="ASPNETCORE_ENVIRONMENT=Development" Command="$(NSwagExe_Net60) run Client/redocly.nswag /variables:Configuration=$(Configuration)">
      <Output TaskParameter="ExitCode" PropertyName="NSwagExitCode" />
      <Output TaskParameter="ConsoleOutput" PropertyName="NSwagOutput" />
    </Exec>

    <Message Text="$(NSwagOutput)" Condition="'$(NSwagExitCode)' == '0'" Importance="low" />
    <Error Text="$(NSwagOutput)" Condition="'$(NSwagExitCode)' != '0'" />
  </Target>
```

Notice how we specified the auto-generation of our C# client as a post build event. We also only generate the C# client when building in Debug mode. Lastly, if the auto-generation of the C# client fails, we fail the build.


References:
* https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild
* https://github.com/RicoSuter/NSwag/blob/master/docs/tutorials/GenerateProxyClientWithCLI/generate-proxy-client.md
* https://github.com/jasontaylordev/CleanArchitecture/tree/main


Tools:
* The Open API specification we used can be found at https://redocly.github.io/redoc/
* NSwagStudio can be dowloaded from https://github.com/RicoSuter/NSwag/wiki/NSwagStudio