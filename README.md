# Lanbuddy Game Scripts

This repository contains game start and server start scripts for games that can be installed through Lanbuddy. The files in this repository are named using the following format: `<IGDBID>-<GAMESLUG>.yaml`.

## Table of Contents

- [Lanbuddy Game Scripts](#lanbuddy-game-scripts)
  - [Table of Contents](#table-of-contents)
  - [Usage](#usage)
  - [Contributing](#contributing)
    - [Pull Requests](#pull-requests)
    - [Issues](#issues)
  - [Lanbuddy Scripts (.yaml files)](#lanbuddy-scripts-yaml-files)
    - [Setup Section](#setup-section)
      - [The `file` type](#the-file-type)
      - [The `execute` type](#the-execute-type)
      - [The `registry` type](#the-registry-type)
    - [Game Section](#game-section)
    - [Server Section](#server-section)
    - [Available Variables](#available-variables)
  - [License](#license)


## Usage

This scripts will be used by [lanbuddy-cli](https://github.com/lanbuddy/lanbuddy-cli) or [lanbuddy-server](https://github.com/lanbuddy/lanbuddy-server) to simplify game package creation. If a game matching the igdb id is found, the scripts are automatically downloaded and used - so there is no manual usage neccessary.

## Contributing

### Pull Requests

If you would like to add some scripts for a game to this repository you are more than welcome to do so. A good pull request should look like this:

- A meaningful title stating what you added/changed and the game you changed something for with its igdb id for example:
  - `Add SuperTux (51231)`
  - `Added dedicated server script to SuperTuxCart (10763) as it was missing`
- Good quality scripts with no spelling mistakes and written in english.
- No hazardous behaviour of the scripts.

### Issues

Issues can be used for a couple of things in this repository:
- You have a problem or found a bug using the scripts for an existing game.
- You want to have scripts for a game added and want to ask if someone could do it for you.
- You found a basic issue with the Lanbuddy Script "Language" and want to report it.

## Lanbuddy Scripts (.yaml files)

The game scripts are pretty easy to create and use. Their syntax looks like this:

```yaml
---
version: 1

setup:
  - type: execute
    path: "{game_dir}/dependency.exe"
  - type: file
    path: "{user_dir}/Documents/Game/settings.ini"
    content: |
      [game]
      enable_splash_screen=0
      [user]
      name={user_name}
  - type: registry
    subtype: dword
    path: "HKEY_CURRENT_USER\\SOFTWARE\\Game Company\Game"
    key: "Installed"
    content: 1
  - type: execute
    path: "{game_dir}/language.exe"

game:
  path: "{game_dir}/game.exe"
  arguments: "--user-name={user_name}"
  firewall:
    - "{game_dir}/game.exe"
    - "{game_dir}/data/binaries/Win64/game-win64.exe"

server:
  path: "{game_dir}/game-server.exe"
  arguments: "--server-config=config.xml --lan-server={user_name}_server --network-console"
  firewall:
    - "{game_dir}/game-server.exe"
```

### Setup Section

The setup section is used to create neccessary files, registry entries or run executables containing dependencies. The setup section takes a list of instructions how to set up the game and walks through it top to bottom.

Possible step types are:

#### The `file` type


Creates a file.

 Parameter  | Type           | Description
------------|----------------|--------------------------
 path       | string         | The path to the file.
 content    | string         | The content of the file.

Usage:

```yaml
setup:
  - type: file
    path: "{user_dir}/Documents/Game/Game.ini"
    content: |
      [game]
      key=value
```

#### The `execute` type

Runs an executable.

 Parameter  | Type           | Description
------------|----------------|-----------------------------
 path       | string         | The path to the executable.

Usage:

```yaml
setup:
  - type: execute
    path: "{game_dir}/dependency.exe"
```

#### The `registry` type

Creates a registry key.

 Parameter  | Type           | Description
------------|----------------|-----------------------------
 path       | string         | The path to the executable.
 subtype    | string         | The registry key type. (DWORD, SZ, MULTI_SZ, BINARY)
 key        | string         | The registry key name.
 content    | string, int    | The content of the registry key.

```yaml
setup:
  - type: registry
    subtype: DWORD
    path: "HKEY_CURRENT_USER\SOFTWARE\Game Company\Game"
    key: "Installed"
    content: 1
```

### Game Section

This section defines how a game can be started.

 Parameter  | Type           | Description
------------|----------------|-----------------------------
 path       | string         | The path to the executable.
 arguments  | string         | The arguments to use with the executable.
 firewall   | list[string]   | A list of executables to open the firewall for.

### Server Section

This section defines how a server can be started.

 Parameter  | Type           | Description
------------|----------------|-----------------------------
 path       | string         | The path to the executable.
 arguments  | string         | The arguments to use with the executable.
 firewall   | list[string]   | A list of executables to open the firewall for.

### Available Variables

To ensure the best user experience script creators can and should use variables in their game scripts. Lanbuddy is exposing the following templateable variables by default:

 Variable Name   | Description                       | Example
-----------------|-----------------------------------|-----------------------------
 `{user_name}`   | The users name.                   | `lanbuddy`
 `{game_dir}`    | The gamefiles directory.          | `D:\games\game\data\`
 `{user_dir}`    | The current user directory.       | `C:\Users\lanbuddy\`
 `{appdata_dir}` | The appdata directory.            | `C:\Users\lanbuddy\AppData\`
 `{lang}`        | The language.                     | `german`
 `{lang_cap}`    | The language but capitalized.     | `German`
 `{lang_iso_1}`  | The langauge in iso set 1 format. | `de`
 `{lang_iso_2}`  | The langauge in iso set 2 format. | `deu`
 `{lang_local}`  | The language in its language.     | `deutsch`
 `{locale}`      | The locale code.                  | `de_DE`

## License

This repository is licensed under the [MIT License](./LICENSE).