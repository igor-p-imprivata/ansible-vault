# Vault

[![Build Status](https://travis-ci.org/brianshumate/ansible-vault.svg?branch=master)](https://travis-ci.org/brianshumate/ansible-vault)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-brianshumate.vault-blue.svg)](https://galaxy.ansible.com/brianshumate/vault/)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/brianshumate/ansible-vault.svg)](http://isitmaintained.com/project/brianshumate/ansible-vault "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/brianshumate/ansible-vault.svg)](http://isitmaintained.com/project/brianshumate/ansible-vault "Percentage of issues still open")

This Ansible role performs a basic [Vault](https://vaultproject.io/)
installation, including filesystem structure and example configuration.

It can also bootstrap a minimal development or evaluation server or HA
Consul-backed cluster in a Vagrant and VirtualBox based environment. See
[README_VAGRANT.md](https://github.com/brianshumate/ansible-vault/blob/master/examples/README_VAGRANT.md) and the associated [Vagrantfile](https://github.com/brianshumate/ansible-vault/blob/master/examples/Vagrantfile) for more details about the developer mode setup.

## Requirements

This role requires FreeBSD, or a Debian or RHEL based Linux distribution. It
might work with other software versions, but does work with the following
specific software and versions:

* Ansible: 2.2.2.0
* Vault: 0.7.0
* Debian: 8
* FreeBSD 11
* Ubuntu 16.04

Sorry, there is no planned support at the moment for Windows.

## Role Variables

The role defines variables in `defaults/main.yml`:


### `vault_version`

- version to install - can be overridden with `VAULT_VERSION` environment variable
- Default value: *0.7.0*

### `vault_pkg`

- package filename
- Default value: `"vault_{{ vault_version }}_linux_amd64.zip"`

### `vault_zip_url`

- Package download URL
- Default value: `"https://releases.hashicorp.com/vault/{{ vault_version }}/vault_{{ vault_version }}_linux_amd64.zip"`

### `vault_checksum_file_url`

- SHA summaries URL
- Default value: `"https://releases.hashicorp.com/vault/{{ vault_version }}/vault_{{ vault_version}}_SHA256SUMS"`

### `vault_bin_path`

- Binary installation path
- Default value: `/usr/local/bin`

### `vault_config_path`

- Configuration file path
- Default value: `/etc/vault.d`

### `vault_data_path`

- Data path
- Default value: `/var/vault`

### `vault_log_path`

- Log path - (not yet implemented)
- Default value: `/var/log/vault`

### `vault_run_path`

- PID file location
- Default value: `/var/run/vault`

### `vault_user`

- OS user name
- Default value: *vault*

### `vault_group`

- OS group name
- Default value: *bin*

### `vault_group_name`

- Inventory group name
- Default value: `cluster_nodes`

### `vault_cluster_name`

- Cluster name label
- Default value: *dc1*

### `vault_datacenter`

- Datacenter label
- Default value:  *dc1*

### `vault_consul`

- host:port value for connecting to Consul HA backend
- Default value: *127.0.0.1:8500*

### `vault_consul_path`

- Name of Vault's Consul K/V root path
- Default value: *vault*

### `vault_log_level`

- [Log level](https://www.consul.io/docs/agent/options.html#_log_level)
  - Supported values: trace, debug, info, warn, err
- Default value: *info*

### `vault_syslog_enable`

- Log to syslog (not yet impemented)
- Default value: *true*

### `vault_iface`

- Network interface can be overridden with `VAULT_IFACE` environment variable
- Default value: `eth1`

### `vault_address`

- Primary network interface address to use
- Default value: `"{{ hostvars[inventory_hostname]['ansible_eth1']['ipv4']['address'] }}"`

### `vault_redirect_addr`

- [HA Client Redirect address](https://www.vaultproject.io/docs/concepts/ha.html#client-redirection)
- Default value: `"{{ hostvars[inventory_hostname]['ansible_'+vault_iface]['ipv4']['address'] }}"`

### `vault_port`

- TCP port number to on which to listen
- Default value: *8200*

### `vault_node_name`

- Short node name
- Default value: `"{{ inventory_hostname_short }}"`

### `vault_main_config`

- Main configuration file name (full path)
- Default value: `"{{ vault_config_path }}/vault_main.hcl"`

### `vault_primary_node`

- Active node FQDN
- Default value: `"{{hostvars[groups['primary'][0]]['ansible_fqdn']}}"`

### `vault_backend`

- Backend template filename
- Default value: `backend_consul.j2`

### `vault_cluster_address`

- Address for intra-cluster communication
- Default value: `"{{ hostvars[inventory_hostname]['ansible_'+vault_iface]['ipv4']['address'] }}"`

### `vault_cluster_disable`

- Disable HA clustering
- Default value: *false*

### `vault_tls_config_path`

- Path to TLS certificate and key
- Default value `/etc/vault/tls`

### `vault_tls_disable`

- [Disable TLS](https://www.vaultproject.io/docs/configuration/listener/tcp.html#tls_disable)
- Default value: *1*

### `vault_tls_cert_file`

- [Vault TLS certificate file path](https://www.vaultproject.io/docs/configuration/listener/tcp.html#tls_cert_file)
- Default value: None

### `vault_tls_cert_file_dest`

- Vault TLS certificate destination (full path)
- Default value: `"{{ vault_config_path }}/vault.crt" # /etc/pki/tls/certs/vault.crt`

### `vault_tls_key_file`

- [Vault TLS key file path](https://www.vaultproject.io/docs/configuration/listener/tcp.html#tls_key_file)
- Default value: None

### `vault_tls_key_file_dest`

- Vault TLS key destination (full path)
- Default value: `"{{ vault_config_path }}/vault.key"`

### `vault_tls_min_version`

- [Minimum acceptable TLS version](https://www.vaultproject.io/docs/configuration/listener/tcp.html#tls_min_version) can be overridden with `VAULT_TLS_MIN_VERSION` environment variable
- Default value: *tls12*

### `vault_tls_cipher_suites`

- [Comma-separated list of supported ciphersuites](https://www.vaultproject.io/docs/configuration/listener/tcp.html#tls_cipher_suites)
- Default value: "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA"

### `vault_tls_prefer_server_cipher_suites`

- [Prefer server's cipher suite over client cipher suite](https://www.vaultproject.io/docs/configuration/listener/tcp.html#tls_prefer_server_cipher_suites) can be overridden by `VAULT_TLS_PREFER_SERVER_CIPHER_SUITES` environment variable
- Default value: *false*

## OS Distribution Variables

The `consul` binary works on most Linux platforms and is not distribution
specific. However, some distributions require installation of specific OS
packages with different naming, so this role was built with support for
popular Linux distributions and defines these variables to deal with the
differences across distributions:


### `vault_pkg`

- Vault package filename
- Default value: `{{ vault_version }}_linux_amd64.zip`

### `vault_centos_url`

- Vault package download URL
- Default value: `{{ vault_zip_url }}`

### `vault_sha256`

- Vault download SHA256 summary
- Default value: SHA256 SUM

### `vault_centos_os_packages`

- List of OS packages to install
- Default value: list

### `vault_pkg`

- Vault package filename
- Default value: `"{{ vault_version }}_linux_amd64.zip"`

### `vault_debian_url`

- Vault package download URL
- Default value: `"{{ vault_zip_url }}"`

### `vault_sha256`

- Vault download SHA256 summary
- Default value: SHA256 SUM

### `vault_debian_os_packages`

- List of OS packages to install
- Default value: list

### `vault_pkg`

- Vault package filename
- Default value: `"{{ vault_version }}_linux_amd64.zip"`

### `vault_redhat_url`

- Vault package download URL
- Default value: `"{{ vault_zip_url }}"`

### `vault_sha256`

- Vault package SHA256 summary
- Default value: SHA256 SUM

### `vault_redhat_os_packages`

- List of OS packages to install
- Default value: list

### `vault_pkg`

- Vault package filename
- Default value: `"{{ vault_version }}_linux_amd64.zip"`

### `vault_ubuntu_url`

- Vault package download URL
- Default value: `"{{ vault_zip_url }}"`

### `vault_sha256`

- Vault package SHA256 summary
- Default value: SHA256 SUM

### `vault_ubuntu_os_packages`

- List of OS packages to install
- Default value: list

## Dependencies

Ansible requires GNU tar and this role performs some local use of the
unarchive module, so ensure that your system has `gtar` installed.

## Example Playbook

Basic installation is possible using the included `site.yml` playbook:

```
ansible-playbook -i hosts site.yml
```

You can also pass variables in using the `--extra-vars` option to the
`ansible-playbook` command:

```
ansible-playbook -i hosts site.yml --extra-vars "vault_datacenter=maui"
```

Specify a template file with a different backend definition
(see `templates/backend_consul.j2`):

```
ansible-playbook -i hosts site.yml --extra-vars "vault_backed=backend_file.j2"
```

You need to make sure that the template file `backend_file.j2` is in the
role directory for this to work.

### Vagrant and VirtualBox

See `examples/README_VAGRANT.md` for details on quick Vagrant deployments
under VirtualBox for testing, etc.

## License

BSD

## Author Information

[Brian Shumate](http://brianshumate.com)

## Contributors

Special thanks to the folks listed in [CONTRIBUTORS.md](https://github.com/brianshumate/ansible-vault/blob/master/CONTRIBUTORS.md) for their
contributions to this project.
