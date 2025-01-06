<!-- @format -->

<p><img src="https://code.benco.io/icon-collection/logos/ansible.svg" alt="ansible logo" title="ansible" align="left" height="60" /></p>

# Basic-Service
[![Galaxy Role](https://img.shields.io/ansible/role/d/0x0i/basic_service
)](https://galaxy.ansible.com/ui/standalone/roles/0x0i/basic_service/)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/0x0I/basic-service?color=yellow)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

Configure and operate a basic cloud-native service: running anything from cypto blockchain clients to the immense app store of open-source ([Apache](https://projects.apache.org/projects.html), [CNCF](https://landscape.cncf.io/card-mode?project=hosted&grouping=no) and beyond) services.

## Requirements

Systemd, [Docker SDK](https://docker-py.readthedocs.io/en/stable/) for Python (for Python 2.6 support, use the deprecated `docker-py` library instead), installation of the `docker` engine or a Kubernetes cluster.

## Role Variables

### Common

|       var       |                        description                         |     default      |
| :-------------: | :--------------------------------------------------------: | :--------------: |
|   _setup_mode_   |  infrastructure provisioning setup mode (`container, k8s, systemd`)  |   `undefined`    |
|     _name_      |                 name of service to deploy                  |    **required**    |
|     _command_     |             Command and arguments to execute on startup              |    **required**    |
|     _user_     |             service user to setup              |    `root`    |
|    _config_     |  configuration files associated with the service to mount  |       `{}`       |
|   _config_env_   |  environment variables to set within the service runtime   |       `{}`       |
|     _ports_     |          listening port information for a service          |       `{}`       |
|    _data_dir_    |  directory mappings to store service runtime/operational data |      `/tmp`      |
|  _host_data_dir_  |   host directory for general deployment operations    |    `/var/tmp`    |
|     _cpus_      |  CPU resources each deployed service can use (either percentage for systemd or cores for containers)   |      `100`       |
|    _memory_     | available memory resources each deployed service can use |       `1G`       |
| _restart_policy_ |                  service restart policy                  | `unless-stopped` |
|   _uninstall_   |    whether to remove installed service and artifacts    |     `false`      |

### Container

|       var       |                        description                         |     default      |
| :-------------: | :--------------------------------------------------------: | :--------------: |
|     _image_     |             service container image to deploy              |    ` `    |
|     _network_mode_     |             container network to attach ([more info](https://docs.ansible.com/ansible/latest/collections/community/docker/docker_container_module.html#parameter-network_mode))              |    `bridge `    |

### Systemd

|       var       |                        description                         |     default      |
| :-------------: | :--------------------------------------------------------: | :--------------: |
|     _binary_url_     |             URL of the binary file to download              |    ` `    |
|     _binary_file_name_override_     |             Override the binary file name after moving it to the destination directory              |    ` `    |
|    _binary_strip_components_     | Strip NUMBER leading components from file names on extraction | `0` |
|     _destination_directory_     |             directory where the binary file will be placed after downloading/extracting              |    `/usr/local/bin`    |
|   _systemd_   |    custom service type & unit, service and install properties    |     `{}`      |

### Kubernetes (k8s)

To authorize access to the target Kubernetes cluster, set the following environment variables:
```
export KUBECONFIG=<path-to-the-kubeconfig-file>
export KUBE_CONTEXT=<context-within-the-kubeconfig-to-use>
```

|       var       |                        description                         |     default      |
| :-------------: | :--------------------------------------------------------: | :--------------: |
|     _helm_chart_path_     |             path to Helm chart to use for the service deployment/release              |    `../../helm`    |
|     _helm_namespace_      |  Kubernetes namespace to deploy to   |      `default`       |
|    _helm_values_path_     | file to load Helm chart values (see [here](./helm/README.md) for available values) |       `values.yml`       |

## Containerized Apps
- [O1 Containers](https://github.com/O1ahmad/containers)
- [Dockerhub](https://hub.docker.com/search?q=)
- [Quay.io](https://quay.io/search)

## Dependencies

```
roles:
- name: ansible-role-systemd
  url: git+https://github.com/O1ahmad/ansible-role-systemd.git

# ansible-galaxy install git+https://github.com/O1ahmad/ansible-role-systemd.git

collections:
- name: community.docker
```

## Example Playbook

- Launch a Wireguard client which establishes a secure peer tunnel connection:

```
- name: Configure WireGuard VPN
  hosts: VPNServers
  remote_user: devops
  become: true
  roles:
    - role: basic-service
      vars:
        setup_mode: systemd
        name: wireguard
        binary_url: https://git.zx2c4.com/wireguard-tools/snapshot/wireguard-tools-1.0.20210424.tar.xz
        binary_file_name_override: wireguard
        command: >
          /usr/local/bin/wg-quick up wg0
        cpus: 50
        memory: 1G
        config:
          wg0.conf:
            destinationPath: /etc/wireguard/wg0.conf
            data: |
              [Interface]
              PrivateKey = <Your-Private-Key>
              Address = 10.0.0.1/24
              ListenPort = 51820

              [Peer]
              PublicKey = <Peer-Public-Key>
              Endpoint = <Peer-Public-IP>:51820
              AllowedIPs = 10.0.0.2/32
        ports:
          wireguard:
            ingressPort: 51820
            servicePort: 51820
        systemd:
          service_properties:
            User: wireguard
            ExecStart: /usr/local/bin/wg-quick up wg0
            ExecStop: /usr/local/bin/wg-quick down wg0
            Restart: on-failure
```

## License

MIT

## Author Information

This Ansible role was created in 2023 by O1.IO.

🏆 **always happy to help & donations are always welcome** 💸

- **ETH (Ethereum):** 0x652eD9d222eeA1Ad843efec01E60C29bF2CF6E4c

- **BTC (Bitcoin):** 3E8gMxwEnfAAWbvjoPVqSz6DvPfwQ1q8Jn

- **ATOM (Cosmos):** cosmos19vmcf5t68w6ug45mrwjyauh4ey99u9htrgqv09
