# Procon Plugins — Procon v2 Plugin Collection

## Overview

A collection of [Procon v2](https://github.com/AdKats/Procon-1) plugins for Battlefield game server administration. Each plugin is an independent git repository included here as a submodule.

- **Runtime:** [Procon v2](https://github.com/AdKats/Procon-1) (.NET 8)
- **Plugin SDK:** [pluginsdk/README.md](https://github.com/AdKats/Procon-1/blob/master/pluginsdk/README.md)
- **Plugin API:** [src/PRoCon.Core/Plugin/](https://github.com/AdKats/Procon-1/tree/master/src/PRoCon.Core/Plugin)
- **Maintainer:** Prophet731

## Plugins

| Plugin | Description | Games | License |
|--------|-------------|-------|---------|
| [AdKats](AdKats/) | Admin toolset — 100+ commands, RBAC, cross-server bans, infraction tracking | BF3, BF4, BFHL, BFBC2 | GPLv3 |
| [AdKats-LRT](AdKats-LRT/) | On-spawn loadout enforcer | BF4, BF3, BFHL | GPLv3 |
| [AdaptiveServerSize](AdaptiveServerSize/) | Dynamic server slot management | BF3, BF4, BFHL | GPLv3 |
| [bf4db](bf4db/) | BF4DB.com ban list integration | BF4 | CC BY-ND 4.0 |
| [ChatGUIDStatsAndMapstatsLogger](ChatGUIDStatsAndMapstatsLogger/) | Player stats, map stats, and chat logger | BF3, BF4, BFBC2, MOH, MOHW | GPLv3 |
| [EventLogger](EventLogger/) | Server event logging to MySQL | BF3, BF4, BFHL, BFBC2 | GPLv3 |
| [fail-log](fail-log/) | Failure event logging and diagnostics | BF3 | GPLv3 |
| [insane-limits](insane-limits/) | Custom rule engine with runtime C# compilation | BF3, BF4, BFHL | GPLv3 |
| [LatencyManager](LatencyManager/) | Player latency monitoring and enforcement | BF3, BF4, BFHL | GPLv3 |
| [multi-balancer](multi-balancer/) | Advanced skill-based team balancer | BF3, BF4, BFHL | GPLv3 |
| [ProconRulz](ProconRulz/) | Rule-based server management | BF3, BF4, BFHL, BFBC2 | GPLv3 |
| [ProconRulzNL](ProconRulzNL/) | Deprecated — merged into ProconRulz | — | GPLv3 |
| [PunkbusterScreenshotEnforcer](PunkbusterScreenshotEnforcer/) | PunkBuster screenshot enforcement | BF3, BF4 | GPLv3 |
| [TrueBalancer](TrueBalancer/) | Team balancer with skill-based scrambler | BF3, BF4 | GPLv3 |
| [UltimateMapManager](UltimateMapManager/) | Map rotation and voting manager | BF3, BF4, BFHL, MOHW | GPLv3 |
| [vip-slot-manager](vip-slot-manager/) | VIP reserved slot management with MySQL | BF3, BF4, BFHL, BFBC2 | GPLv3 |
| [voteban](voteban/) | Player-initiated vote-to-kick system | BF3, BF4 | GPLv3 |
| [xVotemap](xVotemap/) | In-game map vote system | BF3, BF4 | GPLv3 |

## Code Style

Style is enforced by `.editorconfig` and checked via `dotnet format` in each plugin's CI pipeline.

**Conventions:**
- **Use `String`, `Int32`, `Boolean`, `Double`** — not `string`, `int`, `bool`, `double`
- **Allman brace style** — opening brace on its own line
- **4 spaces**, LF line endings, UTF-8 (no BOM)
- **Block-scoped namespaces**, `using` directives outside namespace, System usings first
- **Namespace:** `PRoConEvents` for all plugin classes

```bash
# Run style checks locally (from any plugin directory)
dotnet restore
dotnet format whitespace --verify-no-changes
dotnet format style --verify-no-changes --severity warn --exclude-diagnostics IDE1007
```

## Plugin Structure

```
PluginName/
+-- .editorconfig              # Code style rules
+-- PluginName.csproj           # CI-only (dotnet format), NOT a build file
+-- CLAUDE.md                   # Plugin-specific development reference
+-- README.md                   # User-facing documentation
+-- CHANGELOG.md                # Version history
+-- LICENSE
+-- .github/workflows/
|   +-- ci.yml                  # Code style checks on push/PR
|   +-- release.yml             # Tag-triggered release packaging
+-- src/
    +-- PluginName.cs           # Main entry point
    +-- PluginName/             # Partial class files (plugins >3000 lines)
        +-- Settings.cs
        +-- Events.cs
        +-- ...
```

Plugins over ~3000 lines are split into partial classes using the **subfolder layout**. Each plugin's `CLAUDE.md` documents its specific architecture.

## Libraries

| Purpose | Library |
|---------|---------|
| Database | [MySqlConnector](https://mysqlconnector.net/) + [Dapper](https://github.com/DapperLib/Dapper) |
| HTTP | [Flurl.Http](https://flurl.dev/) |
| JSON | [Newtonsoft.Json](https://www.newtonsoft.com/json) |

The `.csproj` in each plugin exists only for `dotnet format` CI checks — plugins are compiled at runtime by Procon's Roslyn-based compiler, not by MSBuild.

## Procon v2 API

Plugins inherit from `PRoConPluginAPI` and implement `IPRoConPluginInterface`. Key patterns:

- Call `RegisterEvents()` in `OnPluginLoaded()` to receive event callbacks
- 59-second execution timeout per invocation
- Plugins are compiled in isolated `AssemblyLoadContext` instances

### Removed from .NET 8

| Removed | Replacement |
|---------|-------------|
| `MySql.Data.MySqlClient` | `MySqlConnector` |
| `System.Windows.Forms` | Plugin console output |
| `PRoCon.Core.HttpServer` | SignalR layer |
| `System.Runtime.Remoting` | Removed (no AppDomain sandboxing) |

See the [Plugin SDK](https://github.com/AdKats/Procon-1/blob/master/pluginsdk/README.md) for the complete API reference.

## Cross-Plugin Dependencies

- **AdKats** depends on **ChatGUIDStatsAndMapstatsLogger** database tables
- **AdKats-LRT** optionally integrates with **AdKats** at runtime
- **bf4db** optionally integrates with **AdKats** at runtime

## Branch Strategy

- `master` — Procon v2 development
- `legacy` — archived pre-refactor code
