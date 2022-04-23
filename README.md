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
| `csgo_custom_config_path` | Base Path for Server and Custom Configs | See below |
| `__csgo_server_cfg` | Main Configuration File | See below |
| `__csgo_custom_cfg` | Gamemode configuration | See below |
| `__csgo_gamemodes_server_txt` | gamemodes_server.txt file | See below |
| `csgo_gslt` | Valve Game Server Logon Token | See below |
| `csgo_port` | Network port | `27015` |
| `csgo_extra_maps_directory` | Directory containing extra bsp and nav files | `""` |

### `csgo_motd`

The MOTD is a welcome text displayed by the server during the first connection of a player.
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

### `csgo_custom_config_path`

The base directory for csgo_server_cfg, csgo_custom_cfg, and csgo_gamemodes_server_txt. These files are all necessary for performing complex and useful configurations for custom game modes and for configuring servers with plugins enabled.

The contents of these files will ultimately be loaded into j2 templates and copied to your server, where they will be named appropriately.  See Valve's CSGO Dedicated Server documentation for context regarding how these files interact with the server.

It is assumed you will have a server.cfg, custom.cfg and gamemodes_server.txt included in this folder. See below for what these files should contain.

Here is an example of how you might organize your files locally to support this role;

Default value is `""` implying the files are in the root of your project.

```
project
│   README.md
│   main.yml  
│
└───sourcemod_plugins
│   │   ...
│   
└───configs
    │___practice
    |   | server.cfg
    |   | custom.cfg
    |   | gamemodes_server.txt
    |
    │___1v1
        | server.cfg
        | custom.cfg
        | gamemodes_server.txt 
```


### `__csgo_server_cfg`

The server.cfg file is the main configuration file for the server.
It holds the server name, the administrator password as well as various convars.

Default value is `{{ csgo_custom_config_path }}/server.cfg`

Example:

```
hostname "My Counter-Strike Server"
rcon_password mypassword
sv_cheats 1
```

### `__csgo_custom_cfg`

The custom.cfg file is the main configuration file for your gamemode.
It holds the various rules for your gamemode. This file should be referenced directly in your gamemodes_server.txt file.

Default value is `{{ csgo_custom_config_path }}/custom.cfg`

Example:

```
mp_friendlyfire 1
mp_allowNPCs 0
mp_autoteambalance 1
mp_autokick 0
```


### `csgo_gamemodes_server_txt`

The gamemodes_server.txt file defines the final layers of execution for your gamemode rules and settings.
This file is where you will point to your "custom.cfg" file for the server to load the settings you intend to use.
This file also includes your map group definitions.  

See gamemodes_server.txt.example in /csgo/.

https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive_Dedicated_Servers#gamemodes_server.txt
https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive_Dedicated_Servers#Maps

Default value is `{{ csgo_custom_config_path }}/gamemodes_server.txt`


### `csgo_gslt`

Your Steam Game Server Logon Token.  This is required for servers you wish to access using a public IP.  This should be treated as sensitive information.

https://steamcommunity.com/dev/managegameservers


## Example Playbook

```yaml
- hosts: game
  vars:
    csgo_extra_maps_directory: "files/csgo/maps"
    csgo_custom_config_path: "configs/practice"
    csgo_gslt: "123456"
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
