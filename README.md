# Ansible Role: Counter-Strike: Global Offensive

An Ansible role that installs and configures a Counter-Strike: Global Offensive dedicated server.

The game server is downloaded through Steam and exposed as a systemd service for easier management.
Using this role it is possible to publish a minimalist server with custom maps.
For more advanced configuration it is necessary to install [Metamod:Source](https://github.com/tleguern/ansible-role-metamod-source) and [SourceMod](https://github.com/tleguern/ansible-role-sourcemod).

## Requirements

An ansible role dedicated to the installation of SteamCMD such as [tleguern.steamcmd](https://github.com/tleguern/ansible-steamcmd).
This role should provide the `steam_home` variable, pointing to such a folder as `/home/steam/Steam` or `/home/steam/.steam` depending on your operating system.

## Role Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `steamcmd_user` | User name for steamcmd | `steam` |
| `steamcmd_bin` | Path to the steamcmd executable | `/usr/games/steamcmd` |
| `csgo_motd` | HTML text for the server's MOTD | See below |
| `csgo_mapcycle` | List of maps for the rotation | See below |
| `csgo_custom_config_path` | Base Path for Server and Custom Configs | See below |
| `csgo_custom_cfg` | Gamemode configuration | See below |
| `csgo_gamemodes_server_txt` | gamemodes_server.txt file | See below |
| `csgo_port` | Network port | `27015` |
| `csgo_extra_mapcycles` | Configuration of extra mapcycle | See below |
| `csgo_extra_maps_directory` | Directory containing extra bsp and nav files | `""` |

### `csgo_motd`

The MOTD is a welcome text displayed by the server during the first connexion of a player.
It is formatted in HTML.

Default value:

```html
  <html>
  <head>
    <title>Counter-Strike: Global Offensive</title>
  </head>
  <body scroll="no">
    <pre>MOTD</pre>
  </body>
  </html>
```

### `csgo_mapcycle`

The mapcycle file is a simple list of maps loaded on the server.

There is no default value.

Example:

```
cs_italy
de_dust2
de_aztec
cs_office
de_piranesi
```

### `csgo_custom_config_path`

The base directory for csgo_server_cfg, csgo_custom_cfg, and csgo_gamemodes_server_txt. These files are all necessary for performing complex and useful configurations for custom game modes and for configuring servers with plugins enabled.

The contents of these files will ultimately be loaded into j2 templates and copied to your server, where they will be named appropriately.  See Valve's CSGO Dedicated Server documentation for context regarding how these files interact with the server.

It is assumed you will have a server.cfg, custom.cfg and gamemodes_server.txt included in this folder, however these files can be named anything arbitrary for your convenience.


### `csgo_server_cfg`

The server.cfg file is the main configuration file for the server.
It holds the server name, the administrator password as well as various convars.

There is no default value.

Example:

```
hostname "My Counter-Strike Server"
rcon_password mypassword
sv_cheats 1
```

### `csgo_custom_cfg`

The custom.cfg file is the main configuration file for your gamemode.
It holds the various rules for your gamemode. This file should be referenced directly in your gamemodes_server.txt file.

There is no default value.

Example:

```
mp_friendlyfire 1
mp_allowNPCs 0
mp_autoteambalance 1
mp_autokick 0
```

### `csgo_gamemodes_server_txt`

The gamemodes_server.txt file defines the final layers of execution for your gamemode rules and settings.

See gamemodes_server.txt.example in /csgo/.


### `csgo_gslt`

Your Steam Game Server Logon Token.  This is required for servers you wish to access using a public IP.  This should be treated as sensitive information.

https://steamcommunity.com/dev/managegameservers


### `csgo_extra_mapcycles`

A list of hashes containing two keys: `name` and `content`.
The first will be used as a prefix to form a new mapcycle file named `mapcycle_{{ name }}.txt` and containing `{{ content }}`.

Example:

```
csgo_extra_mapcycles:
  - name: deathrun
    content: |
      deathrun_temple
      deathrun_goldfever
  - name: dustonly
    content: |
      de_dust
      de_dust2
```

These files can later be used with rcon, using the `mapcyclefile` CVAR.

## Example Playbook

```yaml
- hosts: game
  vars:
    csgo_extra_maps_directory: "files/csgo/maps"
    csgo_server_cfg: |
      hostname "My Server"
      sv_password password
      rcon_password rconpassword
    csgo_mapcycle: |
      de_dust2
      de_ancient
      cs_office
  roles:
    - role: tleguern.steamcmd
    - role: cjpf.csgo
```

## License

ISC

## Contributing

Please send a Gitlab Pull Request to contribute to this project.  
https://gitlab.com/csgo-services/ansible-role-csgo-dedicated

## Author Information


CJ Pfenninger <cjpf@charliejuliet.net>

Thanks to https://github.com/tleguern for his work here: https://github.com/tleguern/ansible-role-cstrike-source, for giving me a foundation with which to start creating this role.
