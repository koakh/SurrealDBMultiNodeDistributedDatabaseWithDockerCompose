# NOTES

## Forked Project

- <https://github.com/pingcap/tidb-docker-compose/fork>

forked to <https://github.com/koakh/SurrealDBMultiNodeDistributedDatabaseWithDockerCompose.git>

## Change forked Project

```shell
# clone project
$ git clone https://github.com/koakh/SurrealDBMultiNodeDistributedDatabaseWithDockerCompose.git
# enter path
$ cd SurrealDBMultiNodeDistributedDatabaseWithDockerCompose
# edit file
$ nano docker-compose.yml
```

## Change docker-compose.yml

add to bottom of file

```yaml
  surrealdb:
    container_name: surrealdb
    image: surrealdb/surrealdb
    restart: always
    depends_on:
      - tikv0
    ports:
      - 8000:8000
    command: start --log trace --user root --pass root tikv://pd0:2379
```

> note for `--pass root`, if wanna change password change it here ex `--pass mysecretpass`

comment port `- "4000:4000"` on `tidb` service

else we get bellow error

```shell
ERROR: for tidb  Cannot start service tidb: driver failed programming external connectivity on endpoint surrealdbmultinodedistributeddatabasewithdockercompose_tidb_1 (e5847e3a8463695244b183a8c68af34600990fb67a19d3a4f3c26799adebf821): Error starting userland proxy: listen tcp4 0.0.0.0:4000: bind: address already in use
```

## Spin Up Docker Stack FULL

```shell
$ cd SurrealDBMultiNodeDistributedDatabaseWithDockerCompose
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

> note for `tikv://pd0:2379`

this will launch some services:
  
### Pingcap services

- pd0:pingcap/pd
  - port: 2379

- pd1:pingcap/pd
  - port: 2379

- pd2:pingcap/pd
  - port: 2379

- tikv0:pingcap/tikv
- tikv1:pingcap/tikv
- tikv2:pingcap/tikv

- tidb:pingcap/tidb
  - port: 4000
  - port: 10080    

- tispark-master:pingcap/tispark
  - port: 7077
  - port: 8080

- tispark-slave0:pingcap/tispark
  - port: 38081

- tidb-vision:pingcap/tidb-vision
  - port: 8010

### Monitor services

- pushgateway:prom/pushgateway

- prometheus:prom/prometheus
  - port: 9090

- grafana:grafana/grafana
  - port: 3000

### Database services

- surrealdb:surrealdb/surrealdb
  - port: 8000

### Monitor Services

- Access monitor at http://localhost:3000 (login with admin/admin if you want to modify grafana)

- Access [tidb-vision](https://github.com/pingcap/tidb-vision) at http://localhost:8010

- Access Spark Web UI at http://localhost:8080
  and access [TiSpark](https://github.com/pingcap/tispark) through spark://127.0.0.1:7077

## Launch some Test Queries and test Persistence

### Insert some Data

```shell
$ DATA="CREATE account SET
	name = \"ACME Inc\",
	created_at = time::now();"
$ curl -s --compressed POST \
	--header 'Content-Type: application/json' \
  --header 'NS: test' \
  --header 'DB: test' \
  --user "root:root" \
	--data "${DATA}" \
	http://localhost:8000/sql | jq
# outcome
[
  {
    "time": "14.686326ms",
    "status": "OK",
    "result": [
      {
        "created_at": "2022-08-02T22:39:25.933515372Z",
        "id": "account:nqc749lhk7ldmxgu35vr",
        "name": "ACME Inc"
      }
    ]
  }
]
```

### Select Some Data

```shell
$ DATA="SELECT * FROM account;"
$ curl -s --compressed POST \
	--header 'Content-Type: application/json' \
  --header 'NS: test' \
  --header 'DB: test' \
  --user "root:root" \
	--data "${DATA}" \
	http://localhost:8000/sql | jq
# outcome
[
  {
    "time": "8.580884ms",
    "status": "OK",
    "result": [
      {
        "created_at": "2022-08-02T22:33:51.128142608Z",
        "id": "account:cd6nfwiavtt8ch2fump2",
        "name": "ACME Inc"
      }
    ]
  }
]
```

### Check DB Info

```shell
$ DATA="INFO FOR DB;"
$ curl -s --compressed  POST \
	--header 'Content-Type: application/json' \
  --header 'NS: test' \
  --header 'DB: test' \
  --user "root:root" \
	--data "${DATA}" \
	http://localhost:8000/sql | jq
# outcome
[
  {
    "time": "6.508192ms",
    "status": "OK",
    "result": {
      "dl": {},
      "dt": {},
      "sc": {},
      "tb": {
        "account": "DEFINE TABLE account SCHEMALESS PERMISSIONS NONE"
      }
    }
  }
]
```

## Stop Stack

```shell
$ cd SurrealDBMultiNodeDistributedDatabaseWithDockerCompose
$ docker-compose down
```

## Start Stack again to test Persisted Storage

```shell
$ cd SurrealDBMultiNodeDistributedDatabaseWithDockerCompose
$ docker-compose up -d
```

### Select Some Data AGAIN

this is a double check to prove that surrealdb data is persisted

```shell
$ DATA="SELECT * FROM account;"
$ curl -s --compressed POST \
	--header 'Content-Type: application/json' \
  --header 'NS: test' \
  --header 'DB: test' \
  --user "root:root" \
	--data "${DATA}" \
	http://localhost:8000/sql | jq
# outcome
[
  {
    "time": "8.580884ms",
    "status": "OK",
    "result": [
      {
        "created_at": "2022-08-02T22:33:51.128142608Z",
        "id": "account:cd6nfwiavtt8ch2fump2",
        "name": "ACME Inc"
      }
    ]
  }
]
```

if you see some data, everything is worked as expected

## TearDown

clean up everything, warning all data will be lost

```shell
$ ./tearDown.sh
```

## Spin Up Docker Stack MINIMAL

this docker stack as a lot of service, we can slim it usign the file `docker-compose-minimal.yml`

```shell
$ cd SurrealDBMultiNodeDistributedDatabaseWithDockerCompose
# don't forget to tearDown
$ ./tearDown.sh
$ docker-compose -f docker-compose-minimal.yml up -d
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

now we have only the minimal service components `pd0`, `tikv0`, `tidb` and obvious our awesome `surrealdb`

in the end tearDown

```shell
$ ./tearDown.sh
```