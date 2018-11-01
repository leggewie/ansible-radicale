# ansible-radicale

[![Build Status](https://travis-ci.com/maxlareo/ansible-radicale.svg?branch=master)](https://travis-ci.com/maxlareo/ansible-radicale)

Provisions [Radicale](http://radicale.org/) - a simple calendar and contact server

## Role Variables

Variable                   | Description                               | Type   | Default
---                        | ---                                       | ---    | ---
`radicale_version`         | Radicale version to install               | String | ` 2.1.10`
`radicale_install_from`    | Install Radicale from `pip` or `source`   | String | ` pip`
`radicale_git_url`         | Radicale Git repository url               | String | `https://github.com/Kozea/Radicale.git`
`radicale_download_dir`    | Locale path to fetch Radicale source code | String | ` "/opt/radicale"`
`radicale_user`            | System user which start Radicale          | String | ` radicale`
`radicale_group`           | System group which start Radicale         | String | ` "{{ radicale_user }}"`
`radicale_collections_dir` | Directory path to store collections data  | String | ` "/data/radicale/collections"`
`radicale_log_dir`         | Director path to store log files          | String | ` "/var/log/radicale"`
`radicale_default_config`  | Dict to set Radicale config file          | Dict   | [see](#radicale_default_config)
`radicale_default_logging` | Dict to set Radicale logging file         | Dict   | [see](#radicale_default_logging)

### radicale_default_config

This variable is the default value and is not mean to be overridden, for that
purpose please use the variable `radicale_config`, only the keys used in this
dict will replace the default ones as the 2 variable will be combined as follow:
```
- set_fact: >
    _radicale_config="{{
      radicale_default_config |
      combine(radicale_config |
      default({}),
      recursive=True)
    }}"
```

Default value:
```yml
radicale_default_config:
  server:
    hosts: "0.0.0.0:5232"
  logging:
    config: /etc/radicale/logging
  storage:
    filesystem_folder: "{{ radicale_collections_dir }}"
```

### radicale_default_logging

This variable is the default value and is not mean to be overridden, for that
purpose please use the variable `radicale_logging`, only the keys used in this
dict will replace the default ones as the 2 variable will be combined as follow:
```yml
- set_fact: >
    _radicale_logging="{{
      radicale_default_logging |
      combine(radicale_logging |
      default({}),
      recursive=True)
    }}"
```

Default values:
```yml
radicale_default_logging:
  loggers:
    keys: root
  handlers:
    keys: file
  formatters:
    keys: full
  logger_root:
    level: INFO
    handlers: file
  handler_file:
    class: "handlers.RotatingFileHandler"
    args: "('/var/log/radicale/log', 'a', 100000, 10)"
    formatter: full
  formatter_full:
    format: '%(asctime)s - %(levelname)s: %(message)s'
```

## Dependencies

None

## Example Playbook

```yml
- hosts: davserver
  roles:
    - role: radicale
  vars:
    radicale_install_from: source
    radicale_download_dir: /srv/radicale
    radicale_config:
      web:
        type: none
      auth:
        type: http_x_remote_user
      server:
        hosts: 127.0.0.1:5232
```
