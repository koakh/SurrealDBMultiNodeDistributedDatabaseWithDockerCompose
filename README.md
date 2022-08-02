# SurrealDB TiDB docker-compose

[![Build Status](https://travis-ci.org/pingcap/tidb-docker-compose.svg?branch=master)](https://travis-ci.org/pingcap/tidb-docker-compose)

**WARNING: This is for testing only, DO NOT USE IN PRODUCTION!**

## Always read

- SurrealDB Docker Compose [NOTES](NOTES.md), with some test queries, test persistence, paly with stack, tearDown, minimal stack version and other notes about fork changes to work with **SurrealDB**

## Requirements

* Docker >= 17.03
* Docker Compose >= 1.6.0

> **Note:** [Legacy Docker Toolbox](https://docs.docker.com/toolbox/toolbox_install_mac/) users must migrate to [Docker for Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac), since it is tested that tidb-docker-compose cannot be started on Docker Toolbox and Docker Machine.
> **Note:** It is recommended to disable SELinux.

## Quick start

```shell
# clone project
$ git clone https://github.com/koakh/SurrealDBMultiNodeDistributedDatabaseWithDockerCompose.git
# enter path
$ cd SurrealDBMultiNodeDistributedDatabaseWithDockerCompose
# spin up stack
$ docker-compose up -d
# view logs optional
$ docker-compose logs -f
# view surrealdb logs
$ docker-compose logs -f surrealdb
# outcome
surrealdb         | 
surrealdb         |  .d8888b.                                             888 8888888b.  888888b.
surrealdb         | d88P  Y88b                                            888 888  'Y88b 888  '88b
surrealdb         | Y88b.                                                 888 888    888 888  .88P
surrealdb         |  'Y888b.   888  888 888d888 888d888  .d88b.   8888b.  888 888    888 8888888K.
surrealdb         |     'Y88b. 888  888 888P'   888P'   d8P  Y8b     '88b 888 888    888 888  'Y88b
surrealdb         |       '888 888  888 888     888     88888888 .d888888 888 888    888 888    888
surrealdb         | Y88b  d88P Y88b 888 888     888     Y8b.     888  888 888 888  .d88P 888   d88P
surrealdb         |  'Y8888P'   'Y88888 888     888      'Y8888  'Y888888 888 8888888P'  8888888P'
surrealdb         | 
surrealdb         | 
surrealdb         | [2022-08-02 21:48:21] INFO  surrealdb::iam Root authentication is enabled
surrealdb         | [2022-08-02 21:48:21] INFO  surrealdb::dbs Database strict mode is disabled
surrealdb         | [2022-08-02 21:48:21] INFO  surrealdb::kvs Connecting to kvs store at tikv://pd0:2379
surrealdb         | [2022-08-02 21:48:21] INFO  surrealdb::kvs Connected to kvs store at tikv://pd0:2379
surrealdb         | [2022-08-02 21:48:21] INFO  surrealdb::net Starting web server on 0.0.0.0:8000
surrealdb         | [2022-08-02 21:48:21] INFO  surrealdb::net Started web server on 0.0.0.0:8000
```

* Access monitor at http://localhost:3000 (login with admin/admin if you want to modify grafana)

* Access [tidb-vision](https://github.com/pingcap/tidb-vision) at http://localhost:8010

* Access Spark Web UI at http://localhost:8080
  and access [TiSpark](https://github.com/pingcap/tispark) through spark://127.0.0.1:7077

## Other Notes

- Original [README_FROM_FORK](README_FROM_FORK.md)

