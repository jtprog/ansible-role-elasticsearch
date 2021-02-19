# jtprogru.install_elasticsearch

![GitHub Workflow Status](https://img.shields.io/github/workflow/status/jtprogru/ansible-role-elasticsearch/CI?label=CI) ![GitHub Workflow Status](https://img.shields.io/github/workflow/status/jtprogru/ansible-role-elasticsearch/Release?label=Release) ![GitHub](https://img.shields.io/github/license/jtprogru/ansible-role-elasticsearch)

An Ansible Role that installs Elasticsearch on RedHat/CentOS or Debian/Ubuntu.

## Requirements

Requires at least Java 8. You can use the [`geerlingguy.java`](https://github.com/geerlingguy/ansible-role-java) to easilly install Java.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):
```yaml
elasticsearch_version: '7.x'
```
The major version to use when installing Elasticsearch.
```yaml
elasticsearch_package_state: present
```
The `elasticsearch` package state; set to `latest` to upgrade or change versions.
```yaml
elasticsearch_service_state: started
elasticsearch_service_enabled: true
```
Controls the Elasticsearch service options.
```yaml
elasticsearch_network_host: localhost
```
Network host to listen for incoming connections on. By default we only listen on the localhost interface. Change this to the IP address to listen on a specific interface, or `0.0.0.0` to listen on all interfaces.
```yaml
elasticsearch_http_port: 9200
```
The port to listen for HTTP connections on.
```yaml
elasticsearch_heap_size_min: 1g
```
The minimum jvm heap size.
```yaml
elasticsearch_heap_size_max: 2g
```
The maximum jvm heap size.
```yaml
elasticsearch_extra_options: ''
```
A placeholder for arbitrary configuration options not exposed by the role. This will be appended as-is to the end of the `elasticsearch.yml` file, as long as your variable preserves formatting with a `|`. For example:

```yaml
elasticsearch_extra_options: |  # Dont forget the pipe!
  some.option: true
  another.option: false
```

## Dependencies

Requires at least Java 8. You can use the [`geerlingguy.java`](https://github.com/geerlingguy/ansible-role-java) to easilly install Java.

## Example Playbook
```yaml
- name: INSTALL ELASTICSEARCH
  hosts: elks
  vars:
    elasticsearch_version: '6.x'
    elasticsearch_network_host: "{{ ansible_default_ipv4.address }}"
    elasticsearch_http_port: 9200

    elasticsearch_heap_size_min: 1g
    elasticsearch_heap_size_max: 1g

    elasticsearch_cluster_name: my-cluster

    elasticsearch_extra_options: |
      node.ml: false
      xpack.ml.enabled: true
      node.master: true
      node.data: true
      node.ingest: true
      discovery.zen.ping.unicast.hosts: [ {%for host in groups['elks']%}"{{ hostvars[host].ansible_eth0.ipv4.address }}"{% if not loop.last %},{% endif %}{% endfor %} ]
      discovery.zen.minimum_master_nodes: 2
      node.name: {{ inventory_hostname }}
      cluster.name: {{ elasticsearch_cluster_name }}

  roles:
    - geerlingguy.java
    - jtprogru.install_elasticsearch
```
## License

MIT / BSD

## Author Information

This role was created in 2014 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).

This role modified in 2021 by [Michel Savin](https://jtprog.ru).
