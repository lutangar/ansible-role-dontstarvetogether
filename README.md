Ansible role Don't Starve Together dedicated server
===================================================

Setup and install one or more Don't Starve Together dedicated server.
> caves included :mushroom:

Requirements
------------

The Steam CMD client must be present on the targeted host.
You may use this role in combination with this [role for Steam cmd](https://github.com/lutangar/ansible-role-steamcmd) to ease things up.

In any case you can simply point to your existing installation with the help of these variables:
```
steamcmd_user: steam
steamcmd_user_home: /home/{{ steamcmd_user }}/
steamcmd_directory: /home/{{ steamcmd_user }}/cmd/
```

Role Variables
--------------
```
# steamcmd configuration
steamcmd_user: steam
steamcmd_user_home: /home/{{ steamcmd_user }}/
steamcmd_directory: /home/{{ steamcmd_user }}/cmd/
steamcmd_steam:
  username: anonymous
  password:

# app wide configuration
dst_app_id: 343050
dst_install_dir: '{{ steamcmd_user_home }}apps/dontstarvetogether/'
dst_server_token: ~ # proves ownership of Don’t Starve Together

# world
dst_world_preset: SURVIVAL_TOGETHER

# network
dst_server_name: "[Host]'s World"
dst_server_description: Once uppon a time in a quite hostile world.
dst_server_port: 10999
dst_server_password: password
dst_server_save_slot: ~  # which save slot should the dedicated server load
dst_server_intention: cooperative
dst_max_players: 6 # 1..64
dst_pvp: false # true | false
dst_game_mode: survival # endless | survival | wilderness
dst_enable_autosaver: true # creates a save at the start of each day, which will be resumed from when the server restarts
dst_enable_snapshots: false # allow GSAs the ability to fully roll back a server to an older state
dst_tick_rate: 30 # quality of the server, higher tick rate consumes more bandwidth and CPU
dst_connection_timeout: 8000 # time in milliseconds that the server should wait before dropping a non responsive client
dst_enable_vote_kick: false # vote kick allows users to vote to kick players that are disrupting the game
dst_pause_when_empty: true # pause the simulation when the dedicated server is empty
dst_offline_server: false

# steam
dst_disable_cloud: true

# misc
dst_console_enabled: true
dst_autocompiler_enabled: true

# shard
dst_shard_enable: false # enable different server to work together
dst_shard_is_master: false # define the master server
dst_shard_name: overworld # define the shard name
dst_shard_cluster_key: change_this_if_you_set_bind_ip
dst_shard_master_ip: ~ # set the master server IP
dst_shard_id: "{{ dst_shard_name | to_uuid }}"
```

Example Playbook
----------------

Setup a classical survival Don't Starve Together server:
```
- hosts: myhostname
  vars:
    dst_server_token: myUniqueServerToken
  roles:
    - lutangar.dontstarvetogether
```

Setup a classical **overworld server** plus a **caves server**, and open the
```
- hosts: myhostname
  vars:
    dst_server_token: myUniqueServerToken
  roles:
    - { role: lutangar.dontstarvetogether, dst_shard_enable: true, dst_shard_is_master: true }
    - { role: lutangar.dontstarvetogether, dst_shard_enable: true, dst_world_preset: DST_CAVES, dst_server_port: 11000, dst_shard_name: caves, master_ip: 127.0.0.1 }
```

> Remember there's only one master server, and other servers must set the master's IP and a different port number.

This setup would creates 2 system services named after the shard name, allowing to `stop`, `start` and `restart` servers individually:
```
service dstoverworld restart
service dstcaves restart
```

> Don't forget to open the host ports to access the servers from the outside.

License
-------

MIT

Resources
---------

<http://dont-starve-game.wikia.com/wiki/Guides/Don’t_Starve_Together_Dedicated_Servers>
