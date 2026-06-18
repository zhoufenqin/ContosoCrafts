# Configuration & Externalized Settings Inventory

The ContosoCrafts application has a small configuration footprint centered on two `appsettings` files and standard ASP.NET Core environment selection. No external configuration service, secrets manager, or feature-flag system is declared in the repository.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| `appsettings.json` | Base runtime configuration | `src/appsettings.json` | Provides default logging levels and `AllowedHosts` |
| `appsettings.Development.json` | Development runtime override | `src/appsettings.Development.json` | Overrides logging levels when `ASPNETCORE_ENVIRONMENT=Development` |
| `ContosoCrafts.WebSite.csproj` | Build configuration | `src/ContosoCrafts.WebSite.csproj` | Declares target framework, web SDK, and nullable setting |
| `ASPNETCORE_ENVIRONMENT` | Environment variable | Process environment | Controls which environment-specific `appsettings` file is loaded by the ASP.NET Core host |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| `Debug` | Standard .NET build configuration | Local development and debugging | Uses the default .NET SDK toolchain; no profile-specific plugins declared |
| `Release` | Standard .NET build configuration | Optimized deployment build | Uses the default .NET SDK toolchain; no profile-specific plugins declared |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| `Development` | `ASPNETCORE_ENVIRONMENT=Development` | `appsettings.json`, `appsettings.Development.json` | Raises logging verbosity and adds a `System` log level entry |
| Non-Development environments | Any other `ASPNETCORE_ENVIRONMENT` value or unset | `appsettings.json` | Uses base logging configuration and `AllowedHosts` only |

## Properties Inventory

### ContosoCrafts.WebSite

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `Logging:LogLevel:Default` | `Information` | Overridden to `Debug` in `Development` | `appsettings.json`, `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft` | `Warning` | Overridden to `Information` in `Development` | `appsettings.json`, `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.Hosting.Lifetime` | `Information` | Base only | `appsettings.json` |
| `Logging:LogLevel:System` | Not set in base file | Set to `Information` in `Development` | `appsettings.Development.json` |
| `AllowedHosts` | `*` | Base only | `appsettings.json` |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| `ContosoCrafts.WebSite` | No runtime switches or CLI options declared in repository | Not specified | Not specified; single local web process implied |

## Startup Dependency Chain

1. `ContosoCrafts.WebSite` → waits for → no declared upstream service dependency; the app can start independently because it only needs local file access to `wwwroot/data/products.json`.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| None detected | N/A | N/A |

### Secrets Provisioning Workflow

No secret provisioning workflow is defined in the repository. The checked-in configuration files do not reference API keys, passwords, Key Vault URIs, or other external secret stores.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| None detected | N/A | N/A |

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET target framework | `netcoreapp3.1` | `src/ContosoCrafts.WebSite.csproj` |
| Project SDK | `Microsoft.NET.Sdk.Web` | `src/ContosoCrafts.WebSite.csproj` |
| Nullable reference types | `enable` | `src/ContosoCrafts.WebSite.csproj` |
| Installed SDK used for baseline build | `10.0.300` | `dotnet build` output during assessment |
