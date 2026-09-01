# Manhunt Plugin

this is a minecraft plugin i built for me and my friends. minecraft manhunt is a game mode where
one or more players try to beat the game by defeating the ender dragon, while a separate team of players
try to stop them. 

the project serves as a straightforward version of manhunt. server operators assign runners and hunters, hunters 
receive a compass that tracks runners across dimensions, and "Runner's Luck" keeps progression moving 
with boosted enderman, blaze, and piglin drops.

## Features

- multiple runners and hunters
- hunter compass updated once per second by default
- right-click target cycling when multiple runners are online
- last known runner locations across the Overworld, Nether, and End
- tracker compass restored when a hunter dies/respawns
- live world regeneration with a random or specific seed
- configurable progression drop boosts for runners
- tab completed role management commands
- no database, arena setup, or required dependencies

## Requirements

- Paper 1.21.11
- Java 21 or newer

other Paper 1.21 releases may work but 1.21.11 is the version this project builds and tests against.

## Installation

1. download `manhunt-plugin-1.1.0.jar` from the latest GitHub release, or build it locally.
2. place the JAR in your Paper server's `plugins` directory.
3. restart the server.
4. use the commands below as an operator.

## Commands

| Command | Description                                  |
| --- |----------------------------------------------|
| `/manhunt r <player>` | set an online runner                         |
| `/manhunt h <player>` | set an online hunter and give them a tracker |
| `/manhunt status` | show the current roles                       |
| `/manhunt reset` | clear all roles and trackers                 |
| `/manhunt resetworld` | immediately generate a fresh random world |
| `/manhunt resetworld <seed>` | immediately generate a fresh world from a numeric seed |

all commands require `manhunt.admin`, which defaults to server operators.

world resets generate a new managed Overworld, Nether, and End, then move every online player to the new spawn.
manhunt roles are cleared during the reset. after the first reset, later resets unload and delete the previous managed
world set. Paper keeps its original bootstrap world loaded in the background because primary server worlds cannot be
unloaded safely through the public API.

## Configuration

paper creates `plugins/ManhuntPlugin/config.yml` on first launch:

```yaml
compass:
  display-name: "&cseb tracker :3"
  description: "&7right-click to cycle runners"
  update-interval-ticks: 20

runners-luck:
  enderman-extra-drop-chance: 0.80
  blaze-extra-drop-chance: 0.80
  piglin-pearl-chance: 0.20
```

the enderman and blaze values are an extra chance applied only when the normal
drop does not appear. with a roughly 50% vanilla drop rate, the defaults produce
about a 90% total success rate. chances are clamped between `0.0` and `1.0`.

## Building

the Maven Wrapper downloads the correct Maven version automatically:

```powershell
.\mvnw.cmd clean package
```

on macOS or Linux:

```bash
bash ./mvnw clean package
```

the resulting plugin is written to `target/manhunt-plugin-1.1.0.jar`. tests can be run
with `bash ./mvnw test` or `.\mvnw.cmd test`.

## Design notes

`CompassTracker` owns the Paper events and compass behavior. `ManhuntGame` contains
the role and target selection state without Bukkit dependencies, which keeps the core
rules straightforward to test. `RunnersLuck` handles the runner-only drop modifiers. `WorldResetManager` performs the
live world replacement and restores the previous folders when an intermediate step fails.

game state is intentionally stored in memory. roles reset when the server or plugin restarts; this plugin is designed
for manually organized sessions rather than persistent public server matches.

## License

released under the [MIT License](LICENSE).
