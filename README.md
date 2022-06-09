# ansible-collection-graphing
Ansible collection to provide a graphing backend for Icinga and other systems

[![CI](https://github.com/NETWAYS/ansible-collection-graphing/workflows/Molecule%20Test/badge.svg?event=push)](https://github.com/NETWAYS/ansible-collection-graphing/workflows/Molecule%20Test/badge.svg)

## Roles ##

### InfluxDB ###

Use role `influxdb` in this collection to install InfluxDB 2 and start the service.

### Grafana ###

Install basic Grafana

## Prerequisites ##

Some of the used repositories need the packages `apt-transport-https`, `software-properties-common` and `wget` on Debian systems available.
