> For reference only. Taking a three-node deployment with RocksDB backend under Raft mode as an example, validation was performed under this [commit](https://github.com/apache/incubator-hugegraph/commit/bce6d058f5d1f108b4c19e18eb7a65ab0604a526).

After referring to the [HugeGraph-Server Quick Start](https://hugegraph.apache.org/docs/quickstart/hugegraph-server/) and building HugeGraph from the source code, you can find the folder `hugegraph-server/apache-hugegraph-incubating-*` in the project path. Copy this folder into three replicas `A, B, C`, and configure them sequentially (keep the default values for unspecified configuration items).

# TL;DR

- Set `raft.mode` to true in `hugegraph.properties`.
- Set the REST ports to `8081, 8082, 8083` sequentially.
- Set the Gremlin ports to `8181, 8182, 8183` sequentially.
- Ensure no conflicts for Arthas ports on each node.
- Set the RPC ports to `8091, 8092, 8093` sequentially; these ports are essential for communication between Raft nodes.
- Download the corresponding configuration files at this [location](https://github.com/hugegraph/hugegraph/releases/tag/tmp).

# A

## `hugegraph.properties`

```properties
backend=rocksdb
serializer=binary

store=hugegraph

raft.mode=true
```

## `gremlin-server.yaml`

```yaml
host: 127.0.0.1
port: 8181
```

## `rest-server.properties`

```properties
restserver.url=http://127.0.0.1:8081
gremlinserver.url=http://127.0.0.1:8181

arthas.telnet_port=8562
arthas.http_port=8561

rpc.server_host=127.0.0.1
rpc.server_port=8091
rpc.remote_url=127.0.0.1:8091,127.0.0.1:8092,127.0.0.1:8093

raft.group_peers=127.0.0.1:8091,127.0.0.1:8092,127.0.0.1:8093

server.id=server-1
server.role=master
```

# B

## `hugegraph.properties`

```properties
backend=rocksdb
serializer=binary

store=hugegraph

raft.mode=true
```

## `gremlin-server.yaml`

```yaml
host: 127.0.0.1
port: 8182
```

## `rest-server.properties`

```properties
restserver.url=http://127.0.0.1:8082
gremlinserver.url=http://127.0.0.1:8182

arthas.telnet_port=8572
arthas.http_port=8571

rpc.server_host=127.0.0.1
rpc.server_port=8092
rpc.remote_url=127.0.0.1:8091,127.0.0.1:8092,127.0.0.1:8093

raft.group_peers=127.0.0.1:8091,127.0.0.1:8092,127.0.0.1:8093

server.id=server-2
server.role=worker
```

# C

## `hugegraph.properties`

```properties
backend=rocksdb
serializer=binary

store=hugegraph

raft.mode=true
```

## `gremlin-server.yaml`

```yaml
host: 127.0.0.1
port: 8183
```

## `rest-server.properties`

```properties
restserver.url=http://127.0.0.1:8083
gremlinserver.url=http://127.0.0.1:8183

arthas.telnet_port=8582
arthas.http_port=8581

rpc.server_host=127.0.0.1
rpc.server_port=8093
rpc.remote_url=127.0.0.1:8091,127.0.0.1:8092,127.0.0.1:8093

raft.group_peers=127.0.0.1:8091,127.0.0.1:8092,127.0.0.1:8093

server.id=server-3
server.role=worker
```

# start servers

```bash
cd path-to-A
bin/init-store.sh
bin/start-hugegraph.sh

cd path-to-B
bin/init-store.sh
bin/start-hugegraph.sh

cd path-to-C
bin/init-store.sh
bin/start-hugegraph.sh
```

# stop servers

```bash
cd path-to-A
bin/stop-hugegraph.sh

cd path-to-B
bin/stop-hugegraph.sh

cd path-to-C
bin/stop-hugegraph.sh
```
