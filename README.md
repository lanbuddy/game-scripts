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
      - [The `fileTemplate` type](#the-filetemplate-type)
    - [Game and Server Section](#game-and-server-section)
    - [Available Variables](#available-variables)
  - [License](#license)


## Usage

This scripts will be used by [lanbuddy-cli](https://github.com/lanbuddy/lanbuddy-cli) or [lanbuddy-server](https://github.com/lanbuddy/lanbuddy-server) to simplify game package creation. If a game matching the igdb id is found, the scripts are automatically downloaded and used - so there is no manual usage neccessary.

## Contributing

### Pull Requests

If you would like to add some scripts for a game to this repository you are more than welcome to do so. A good pull request should look like this:

- A meaningful title stating what you added/changed and the game you changed something for with its igdb id for example:
  - `Add SuperTux (51231)`
  - `Add server to SuperTuxCart (10763) as it is missing`
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
  windows:
    - type: execute
      command: "{{gameDir}}/dependency.exe"
      arguments: "-q"
      workingDirectory: "{{gameDir}}"
      runAsAdmin: true
      allowedExitCodes:
        - 0
    - type: file
      path: "{{userDir}}/Documents/Game/settings.ini"
      content: |
        [game]
        enable_splash_screen=0
        [user]
        name={{userName}}
    - type: registry
      path: "HKEY_CURRENT_USER\\SOFTWARE\\Game Company\\Game"
      key: "Installed"
      keyType: 'DWORD'
      value: 1
    - type: fileTemplate
      templateFile: "{{gameDir}}/server_settings.ini.template"
      path: "{{gameDir}}/server_settings.ini"

game:
  windows:
    command: "{{gameDir}}/game.exe"
    workingDirectory: "{{gameDir}}"
    arguments: "--user-name={{userName}}"
    firewallRules:
      - action: "allow"
        direction: "in"
        executable: "{{gameDir}}/game.exe"
        name: "allow_incoming_for_gamename"

server:
  windows:
    command: "{{gameDir}}/server.exe"
    workingDirectory: "{{gameDir}}"
    arguments: "--dedicated --max-users=10"
    firewallRules:
      - action: "allow"
        direction: "in"
        executable: "{{gameDir}}/game.exe"
        name: "allow_incoming_for_gamename"
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
  windows:
    - type: file
      path: "{{userDir}}/Documents/Game/Game.ini"
      content: |
        [game]
        key=value
```

#### The `execute` type

Runs an executable.

 Parameter        | Type           | Description
------------------|----------------|-----------------------------
 command          | string         | The path to the executable.
 arguments        | string         | The arguments in string format.
 workingDirectory | string         | The working directory.
 runAsAdmin       | boolean        | Runs the executable as admin.
 allowedExitCodes | array[number]  | A list of allowed exit codes.

**Usage:**
```yaml
setup:
  windows:
    - type: execute
      command: "{{gameDir}}/dependency.exe"
      arguments: "-q"
      workingDirectory: "{{gameDir}}"
      runAsAdmin: true
      allowedExitCodes:
        - 0
```

#### The `registry` type

Creates a registry key.

 Parameter  | Type           | Description
------------|----------------|-----------------------------
 path       | string         | The path to the executable.
 keyType    | string         | The registry key type. (REG_DWORD, REG_SZ, REG_MULTI_SZ, REG_BINARY)
 key        | string         | The registry key name.
 value      | string, int    | The content of the registry key.

```yaml
setup:
  windows:
    - type: registry
      path: "HKEY_CURRENT_USER\\SOFTWARE\\Game Company\\Game"
      key: "Installed"
      keyType: 'DWORD'
      value: 1
```

#### The `fileTemplate` type

Takes a template file, fills in the variables and saves it to a location.

 Parameter    | Type   | Description
--------------|--------|--------------------------------
 path         | string | The path to the target file.
 templateFile | string | The path to the template file.

```yaml
setup:
  windows:
    - type: fileTemplate
      templateFile: "{{gameDir}}/server_settings.ini.template"
      path: "{{gameDir}}/server_settings.ini"
```

### Game and Server Section

This sections define how a game is started.

 Parameter        | Type           | Description
------------------|----------------|-----------------------------
 command          | string               | The path to the executable.
 arguments        | string               | The arguments in string format.
 workingDirectory | string               | The working directory.
 runAsAdmin       | boolean              | Runs the executable as admin.
 firewallRules    | array[FirewallRules] | A list of firewall rules. (Windows only)

```yaml
game:
  windows:
    command: "{{gameDir}}/game.exe"
    workingDirectory: "{{gameDir}}"
    arguments: "--user-name={{userName}}"
    firewallRules:
      - action: "allow"
        direction: "in"
        executable: "{{gameDir}}/game.exe"
        name: "allow_incoming_for_gamename"

server:
  windows:
    command: "{{gameDir}}/server.exe"
    workingDirectory: "{{gameDir}}"
    arguments: "--dedicated --max-users=10"
    firewallRules:
      - action: "allow"
        direction: "in"
        executable: "{{gameDir}}/game.exe"
        name: "allow_incoming_for_gamename"
```

### Available Variables

To ensure the best user experience script creators can and should use variables in their game scripts. Lanbuddy is exposing the following templateable variables by default:

 Variable Name     | Description                       | Example
-------------------|-----------------------------------|-----------------------------
 `{{userName}}`    | The users name.                   | `lanbuddy`
 `{{gameDir}}`     | The gamefiles directory.          | `D:\lanbuddy\installed/game`
 `{{userDir}}`     | The current user directory.       | `C:\Users\lanbuddy\`
 `{{appdataDir}}`  | The appdata directory.            | `C:\Users\lanbuddy\AppData\`
 `{{lang}}`        | The language.                     | `german`
 `{{langCap}}`     | The language but capitalized.     | `German`
 `{{langIso1}}`    | The langauge in iso set 1 format. | `de`
 `{{langIso2}}`    | The langauge in iso set 2 format. | `deu`
 `{{langLocal}}`   | The language in its language.     | `deutsch`
 `{{locale}}`      | The locale code.                  | `de_DE`

## License

This repository is licensed under the [MIT License](./LICENSE).