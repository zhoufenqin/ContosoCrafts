# Configuration & Externalized Settings Inventory

ContosoCrafts.WebSite uses two configuration sources (a base `appsettings.json` and a Development override) with no externalized secret store, config server, feature flag framework, or environment variable–based configuration.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|--------|------|---------------|-------|
| appsettings.json | JSON config file | `src/appsettings.json` | Base configuration; applies to all environments |
| appsettings.Development.json | JSON config file | `src/appsettings.Development.json` | Development environment override; increases log verbosity |
| wwwroot/data/products.json | JSON data file | `src/wwwroot/data/products.json` | Not a configuration file; the application's flat-file data store (see `data-architecture.md`) |

No `web.config`, `launchSettings.json`, Spring Cloud Config, Azure App Configuration, Kubernetes ConfigMaps/Secrets, HashiCorp Vault, or `.env` files exist in the repository.

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---------|-----------|---------|--------------------------|
| Debug | Default for `dotnet run` and IDE | Development build; includes debug symbols, no optimizations | `Microsoft.NET.Sdk.Web` (implicit) |
| Release | Manual: `dotnet publish -c Release` | Production-optimized build; enables IL trimming and optimizations | `Microsoft.NET.Sdk.Web` (implicit) |

No custom MSBuild properties or conditional compilation symbols are defined in the project file beyond the default SDK configurations.

## Runtime Profiles

| Profile | Activation Method | Config Files Loaded | Key Overrides |
|---------|-------------------|---------------------|---------------|
| Development | `ASPNETCORE_ENVIRONMENT=Development` (default for `dotnet run`) | `appsettings.json` + `appsettings.Development.json` | Log levels: Default→Debug, System→Information, Microsoft→Information |
| Production | `ASPNETCORE_ENVIRONMENT=Production` (default in published/deployed scenarios) | `appsettings.json` only | Uses `app.UseExceptionHandler("/Error")` + HSTS instead of developer exception page |

No staging, testing, or cloud-specific runtime profiles exist.

## Properties Inventory

### ContosoCrafts.WebSite

| Property Key | Default Value | Profile Override | Source |
|-------------|---------------|------------------|--------|
| `Logging:LogLevel:Default` | `Information` | `Debug` (Development) | `appsettings.json` / `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft` | `Warning` | `Information` (Development) | `appsettings.json` / `appsettings.Development.json` |
| `Logging:LogLevel:System` | _(not set in base)_ | `Information` (Development) | `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.Hosting.Lifetime` | `Information` | _(no override)_ | `appsettings.json` |
| `AllowedHosts` | `*` | _(no override)_ | `appsettings.json` |

No database connection strings, API keys, external service URLs, or application-specific settings are declared in any configuration file.

## Startup Parameters & Resource Requirements

| Service | Runtime Options | Memory | Instance Count |
|---------|-----------------|--------|----------------|
| ContosoCrafts.WebSite | No JVM/CLR startup options configured; defaults apply | Not specified (no Docker, K8s, or cloud deployment manifest) | 1 (single instance; no scale-out configuration) |

No `-Xms`/`-Xmx` equivalents for .NET (`COMPlus_GCHeapHardLimit`, `DOTNET_GCHeapHardLimit`), Docker `mem_limit`, or Kubernetes resource requests/limits are configured.

## Startup Dependency Chain

ContosoCrafts.WebSite has no external service dependencies and therefore no startup ordering requirements. The application starts independently as a self-contained process:

1. **ContosoCrafts.WebSite** — starts standalone; no wait mechanisms, no health-check probes, no config server to connect to before startup.

There is no config server, service discovery registry, database server, or message broker to wait for.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage |
|-----------------|------|---------|
| _(None detected)_ | — | — |

No secrets, passwords, API keys, connection strings with credentials, or sensitive configuration entries are present in any configuration file. The application has no external service dependencies that would require credentials.

### Secrets Provisioning Workflow

No secrets provisioning workflow exists. The application does not connect to any external system requiring authentication (no database, no external API, no message broker). If external services are added during modernization (e.g., Azure SQL, Azure Service Bus), a secrets management strategy using Azure Key Vault with managed identity is recommended.

## Feature Flags

No feature flag framework is configured. There are no Spring Feature Flags, LaunchDarkly, Unleash, .NET `Microsoft.FeatureManagement`, `@ConditionalOnProperty`, or custom toggle patterns in the codebase.

| Flag Name | Default | Controlled By |
|-----------|---------|---------------|
| _(None detected)_ | — | — |

## Framework & Runtime Versions

| Component | Version | Source |
|-----------|---------|--------|
| Target Framework | .NET Core 3.1 (`netcoreapp3.1`) | `src/ContosoCrafts.WebSite.csproj` |
| ASP.NET Core | 3.1 (implicit via `Microsoft.NET.Sdk.Web`) | `src/ContosoCrafts.WebSite.csproj` |
| Blazor Server | 3.1 (implicit via `Microsoft.NET.Sdk.Web`) | `src/ContosoCrafts.WebSite.csproj` |
| Razor Pages | 3.1 (implicit via `Microsoft.NET.Sdk.Web`) | `src/ContosoCrafts.WebSite.csproj` |
| Bootstrap | 4.3.1 | `src/wwwroot/lib/bootstrap/dist/css/bootstrap.min.css` (inline comment) |
| jQuery | 3.3.1 | `src/wwwroot/lib/jquery/dist/jquery.js` (inline comment) |
| Font Awesome | 4.7.0 | `src/Components/ProductList.razor` (CDN URL) |
| Hosting Model | Out-of-process | `src/ContosoCrafts.WebSite.csproj` (`<AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>`) |
| Nullable Reference Types | Enabled | `src/ContosoCrafts.WebSite.csproj` (`<Nullable>enable</Nullable>`) |
