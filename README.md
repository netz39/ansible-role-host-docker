<!--
SPDX-FileCopyrightText: 2024 Netz39 Administrators <admin@netz39.de>
SPDX-License-Identifier: CC-BY-4.0
-->

# Host Docker

[![REUSE status](https://api.reuse.software/badge/github.com/netz39/ansible-role-host-docker)](https://api.reuse.software/info/github.com/netz39/ansible-role-host-docker)
![license MIT](https://img.shields.io/badge/license-MIT-informational)

Ansible Role for installing the Docker runtime environment on your (Debian) host.

## Table of Contents

- [Requirements](#requirements)
- [Install](#install)
- [Role Variables](#role-variables)
- [Dependencies](#dependencies)
- [Example Playbook](#example-playbook)
- [Contributing](#contributing)
- [License](#license)
- [Author Information](#author-information)

## Requirements

The role has been tested on Debian and Ubuntu 24.04 (LTS) target hosts.
Other distribution have not been tested.

If your target machine has Python 3.12 or later, you need ansible 2.16
or later, otherwise downloading the apt key fails (see
[#17](https://github.com/netz39/ansible-role-host-docker/issues/17)).

## Install

This role can be installed through your *requirements.yml*.

Please note that `docker-compose` is now a part of the Docker client and
available through the `docker compose` command.

Example:

```yaml
---
roles:
  - name: netz39.host_docker
    src: git+https://github.com/netz39/ansible-role-host-docker.git
    version: v0.2.1
```

## Role Variables

You can go with just the defaults.

### Optional Variables

* `docker_apt_key_fpr`:
    * Default: *none*
      If this variable is not set, **no** OpenPGP key fingerprint is checked!
    * Description: Set the fingerprint of the debian repo signing key
      here to notice signing key changes.
* `docker_apt_key_url`:
    * Default: https://download.docker.com/linux/debian/gpg
    * Description: Address to download the OpenPGP key, which is used to
      sign the third party apt repo.
* `docker_apt_keyrings_dir`:
    * Default: */etc/apt/keyrings*
    * Description: Directory to put the downloaded OpenPGP file into.
      Default is Debian standard path for such keys.
      Can be left on default in almost all cases.
* `docker_apt_uri`:
    * Default: https://download.docker.com/linux/debian
    * Description: Address of the third party apt repo to download
      Debian packages from.
      You might want to set this to the address of a local apt proxy
      like *approx*, *apt-cacher* or the like.
* `docker_image_prune`:
    * Default: `false`
    * Description: Enables a systemd timer to automatically prune Docker
      images without container references. You can check the timer with
      `sudo systemctl list-timers --all` and the results with
      `sudo journalctl -xeu docker-image-prune`.
* `docker_data_root`:
    * Default: */var/lib/docker*
    * Description: Persistent data directory where docker puts
      containers, images, volumes, etc.
      See [Daemon data directory](https://docs.docker.com/engine/daemon/#daemon-data-directory)
      in docker documentation for details.
      Often set to a separate volume which is not the root volume of
      the machine docker is installed to.
* `docker_storage_driver`:
    * Default: overlay2
    * Description: See [Select a storage driver](https://docs.docker.com/engine/storage/drivers/select-storage-driver/)
      in docker documentation for detailed description.
* `docker_v6_cidr`:
    * Default: *empty*
    * Description: Enable IPv6 for the docker default network and set
      the given CIDR.
* `docker_log_max_size`:
    * Default: *100m*
    * Description: Set the maximum size of a single log file.
      See [Configure logging drivers](https://docs.docker.com/config/containers/logging/configure/)
      in docker documentation for details.

## Dependencies

No external dependencies.
Tested with ansible 2.14.18 on Debian GNU/Linux 12 (bookworm).

## Example Playbook

### Minimal Example

```yaml
---
- hosts: docker_host
  become: true

  roles:
    - role: netz39.host_docker
```

### Example with Common Options

```yaml
---
- hosts: miraculix
  become: true

  roles:
    - role: netz39.host_docker
      vars:
        docker_apt_uri: "http://deb.example.internal:9999/docker"
        docker_apt_key_url: "{{ docker_apt_uri }}/gpg"
        docker_apt_key_fpr: "9DC858229FC7DD38854AE2D88D81803C0EBFCD88"
        docker_data_root: "/srv/docker"
        docker_cron_image_prune: true
        docker_v6_cidr: "2001:db8:1::/64"
```

## Contributing

Pull requests accepted.

## License

This project is licensed unter the [MIT License](LICENSES/MIT.txt)
unless noted differently.

© 2024 [Netz39 Administrators](http://www.netz39.de/) and contributors.

## Author Information

Notable amount of contributions by (in alphabetic order):

- Alexander Dahl ([@LeSpocky](https://github.com/LeSpocky/))
- David ([@24367dfa](https://github.com/24367dfa))
- Stefan Haun ([@penguineer](https://github.com/penguineer))
