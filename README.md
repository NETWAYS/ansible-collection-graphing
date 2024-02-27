
# ansible-collection-graphing

Ansible collection to provide a graphing backend for Icinga and other systems

[![CI](https://github.com/NETWAYS/ansible-collection-graphing/workflows/Molecule%20Test/badge.svg?event=push)](https://github.com/NETWAYS/ansible-collection-graphing/workflows/Molecule%20Test/badge.svg)

## Roles ##

### InfluxDB ###
 role `influxdb` in this collection to install InfluxDB 2 and start the service.

#### InfluxDB default variables ####

| *Variable*                                   | *Description*                                                                                       |
|----------------------------------------------|-----------------------------------------------------------------------------------------------------|
| `influxdb_influxql_query: true`              | To mapping bucket (InfluxDB 1.x) in databases (InfluxDB 2.X), to use InfluxQL to query bucket data. |
| `influxdb_org: admin`                        | InfluxDB root organisation.                                                                         |
| `influxdb_user: admin`                       | InfluxDB user for webinterface.                                                                      |
| `influxdb_password: strongpass`              | InfluxDB password for webinterface.                                                                 |
| `influxdb_bucket: mybucket`                  | InfluxDB bucket name.                                                                               |
| `influxdb_host: localhost`                   | InfluxDB host address.                                                                               |
| `authentication_config_name: administration` | Authentication config name to create connection configuration.                                      |
| `retention: 365d`                            | Describes how long InfluxDB keeps data, will be used if you use InfluxQL query.                                       
| `influxdb_token_path: /root/`                | The path where token should be stored.                                                              |
| `influxdb_backup_path: /tmp/`                | The path where InfluxDB backups should be stored.                                                   |


### Grafana ###

Install basic Grafana

#### Grafana default variables ####

| *Variable*         | *Description*                          |
|--------------------|----------------------------------------|
| `grafana_user: admin`     | Grafana user for Webeninterface login  |
| `grafana_password: strongpass` | Grafana password for webinterface login |

## Prerequisites ##

#### Packages ####

Some of the used repositories need the packages `apt-transport-https`, `software-properties-common` and `wget` on Debian systems available.

#### Firewall ####

You need to open Firewall ports yourself.

Defaults:

* Grafana: `3000`

#### Grafan defaults variables to setup InfluxDB datasources #####

| *Variable*                                    | *Description*                                                                              |
|-----------------------------------------------|--------------------------------------------------------------------------------------------|
| `influxdb_host: localhost`                    | InfluxDB host address                                                                      |
| `grafana_influxdb_url: http://localhost:8086` | InfluxDB url address.                                                                      |
| `grafana_influxdb_organization: admin`        | InfluxDB organisation, the same organization name which you defined in InfluxDB defaults.  |
| `grafana_influxdb_bucket_name: mybucket`      | InfluxDB bucket name, the same bucket name which you defined in InfluxDB defaults.         |

## Usage ##

You have to run the InfluxDB role before the Grafana role in your playbook. You have to also set the boolen Variable `influxdb_grafana_stack` true in your and add the influxdb variables that are defined in grafana `defaults` to configure the InfluxDB bucket or database as a datasource in Grafana. In following example your see how should your playbook look like.  

```

- hosts: localhost

  roles:

  - influxdb

  - grafana

#Datasource InfluxDB variable
influxdb_grafana_stack: true
influxdb_host: localhost
grafana_influxdb_url: http://localhost:8086
grafana_influxdb_organization: admin
grafana_influxdb_bucket_name: mybucket

#Datasource Konfiguration
grafana_datasources: >

  - name: InfluxDB_v2_Flux
    type: influxdb
    access: proxy
    url: {{ grafana_influxdb_url }}
    secureJsonData:
      token: {{ influxdb_graphing_token }}
    jsonData:
      version: Flux
      organization: {{ grafana_influxdb_organization }}
      defaultBucket: {{ grafana_influxdb_bucket_name }}
      tlsSkipVerify: true
  - name: InfluxDB_v1_influxql
    type: influxdb
    access: proxy
    url: {{ grafana_influxdb_url }}
    database: {{ grafana_influxdb_bucket_name }}
    jsonData:
      httpMode: GET
      httpHeaderName1: 'Authorization'
    secureJsonData:
      httpHeaderValue1: 'Token {{ influxdb_graphing_token }}'
```                                                             

