**Publishing Packages**

- Prereqs: Install .NET 9 SDK (and .NET 10 RC if packing v10), have a NuGet.org API key.
- SDK selection: repo uses `global.json` with `rollForward: latestMajor` and `allowPrerelease: true` so .NET 10 RC works if installed.

Steps

- Update version: edit `NugetProjects/Directory.Build.props` and adjust `<BaseVersion>` if needed.
- Restore/build: `dotnet build -c Release`
- Pack all: `dotnet pack .\NugetProjects\**\*.csproj -c Release -o .\nupkgs`
  - Projects:
    - v6/v6.* (net6/EF Core 6)
    - v7/v7.* (net6/EF Core 7)
    - v8/v8.* (net8/EF Core 8)
    - v9/v9.* (net9/EF Core 9)
    - v10/v10.* (net10/EF Core 10 RC)
- Push single package: `dotnet nuget push .\nupkgs\EfCore.BulkExtensions.Open*.nupkg -k %NUGET_API_KEY% -s https://api.nuget.org/v3/index.json`
- Push all: `dir .\nupkgs\*.nupkg | %% { dotnet nuget push $_.FullName -k %NUGET_API_KEY% -s https://api.nuget.org/v3/index.json }`

Notes

- Package IDs: `EfCore.BulkExtensions.Open` and provider variants: `.SqlServer`, `.PostgreSql`, `.MySQL`, `.SQLite`.
- Readme/license/icon included from repo root per `NugetProjects/Directory.Build.props`.
- Validate locally: `dotnet build /t:pack -c Release` for a specific project to inspect generated `.nupkg`.
- You can pack without the solution: reference the `.csproj` files directly as above.
