---
id: server-versions-and-dependencies
title: Versions and dependencies
sidebar_label: Versions & dependencies
---

## Overview

This page details some of the version specific requirements and dependencies needed to build and run an instance of Temporal.

All versions of Temporal require that [Go v1.15+](https://golang.org/dl/) is installed in the environment.

## Dependencies

Temporal offers official support for, and is tested against, dependencies with the exact versions described below.

### Go Packages

| Temporal version | Go packages |
|------------------|-------------|
| [1.3.2](https://github.com/temporalio/temporal/tree/v1.3.2) | [go.mod](https://github.com/temporalio/temporal/blob/v1.3.2/go.mod) |
| [1.3.1](https://github.com/temporalio/temporal/tree/v1.3.1) | [go.mod](https://github.com/temporalio/temporal/blob/v1.3.1/go.mod) |
| [1.3.0](https://github.com/temporalio/temporal/tree/v1.3.0) | [go.mod](https://github.com/temporalio/temporal/blob/v1.3.0/go.mod) |
| [1.2.2](https://github.com/temporalio/temporal/tree/v1.2.2) | [go.mod](https://github.com/temporalio/temporal/blob/v1.2.2/go.mod) |
| [1.2.1](https://github.com/temporalio/temporal/tree/v1.2.1) | [go.mod](https://github.com/temporalio/temporal/blob/v1.2.1/go.mod) |
| [1.1.1](https://github.com/temporalio/temporal/tree/v1.1.1) | [go.mod](https://github.com/temporalio/temporal/blob/v1.1.1/go.mod) |
| [1.1.0](https://github.com/temporalio/temporal/tree/v1.1.0) | [go.mod](https://github.com/temporalio/temporal/blob/v1.1.0/go.mod) |
| [1.0.0](https://github.com/temporalio/temporal/tree/v1.0.0) | [go.mod](https://github.com/temporalio/temporal/blob/v1.0.0/go.mod) |

### Persistence

The only required dependency is a database, and there are multiple types of databases that are supported.

- **Cassandra v3.11**
- **MySQL v5.7**
- **PostgreSQL v9.6** (supported since Temporal v1.2.1)

### Workflow search

The feature that enables you to search for Workflows is optional. Currently only ElasticSearch is supported. The use of ElasticSearch also requires the use of event streaming software and currently only Kafka + Zookeeper is supported. See the note on event streaming dependency below.

- **ElasticSearch v6.8**
- **Kafka v2.1.1 & Zookeeper v3.4.6**

### Monitoring & observation

Temporal emits metrics by default in a format that is supported by Prometheus. Monitoring and observing those metrics is optional. Any software that can pull metrics that supports the same format could be used, but we only ensure it works with Prometheus and Grafana versions.

- **Prometheus >= v2.0**
- **Grafana >= v2.5**

### Multi-cluster replication

This is an experimental feature, most Temporal users do not need this. Requires the use of event streaming software. See the note on event streaming dependency below.

- **Kafka v2.1.1 & Zookeeper v3.4.6**

:::note Note on event streaming dependency

Event streaming software as a dependency is only required when ElasticSearch is being used or when Temporal is deployed across multiple data centers. However, in future releases of Temporal, third party event streaming software will likely cease to be needed as dependency for both.

:::

## Upgrade your version of Temporal

If there is a newer version of Temporal available, a notification will appear in the Temporal Web UI.

To use a more recent version of Temporal, first [check to see](https://github.com/temporalio/temporal/releases) if an upgrade to the database schema is required. Newer binaries can not run with older database schemas. Some releases require changes to the schema, and some do not. If you are using a version that is older than 1.0.0, reach out to us at [community.temporal.io](http://community.temporal.io) to ask how to upgrade.

We ensure that any consecutive versions are compatible in terms of database schema upgrades, features, and system behavior, however there is no guarantee that there is compatibility between *any* 2 non-consecutive versions. Please reach out to us or check the forums at [community.temporal.io](http://community.temporal.io) for more information.

### Upgrade Cassandra schema

You can use the `temporal-cassandra-tool` to upgrade both the default and visibility schemas for your Cassandra DB:

**Example default schema upgrade:**

```bash
temporal_v1.2.1 $ temporal-cassandra-tool \
   --tls \
   --tls-ca-file <...> \
   --user <cassandra-user> \
   --password <cassandra-password> \
   --endpoint <cassandra.example.com> \
   --keyspace temporal \
   --timeout 120 \
   update \
   --schema-dir ./schema/cassandra/temporal/versioned

```

**Example visibility schema upgrade:**

```bash
temporal_v1.2.1 $ temporal-cassandra-tool \
   --tls \
   --tls-ca-file <...> \
   --user <cassandra-user> \
   --password <cassandra-password> \
   --endpoint <cassandra.example.com> \
   --keyspace temporal_visibility \
   --timeout 120 \
   update \
   --schema-dir ./schema/cassandra/visibility/versioned

```

### Upgrade MySQL / PostgreSQL schema

Use the `temporal-sql-tool`, which works similarly to the `temporal-cassandra-tool`.

Refer to this [Makefile](https://github.com/temporalio/temporal/blob/v1.3.2/Makefile#L367-L383) for context.

#### PostgreSQL

**Example default schema upgrade:***

```bash
./temporal-sql-tool \
	--tls \
	--tls-enable-host-verification \
	--tls-cert-file <path to your client cert> \
	--tls-key-file <path to your client key> \
	--tls-ca-file <path to your CA> \
	--ep localhost -p 5432 -u temporal -pw temporal --pl postgres --db temporal update-schema -d ./schema/postgresql/v96/temporal/versioned
```

**Example visibility schema upgrade:**

```bash
./temporal-sql-tool \
	--tls \
	--tls-enable-host-verification \
	--tls-cert-file <path to your client cert> \
	--tls-key-file <path to your client key> \
	--tls-ca-file <path to your CA> \
	--ep localhost -p 5432 -u temporal -pw temporal --pl postgres --db temporal_visibility update-schema -d ./schema/postgresql/v96/visibility/versioned
```

#### MySQL

**Example default schema upgrade:**

```bash
./temporal-sql-tool \
	--tls \
	--tls-enable-host-verification \
	--tls-cert-file <path to your client cert> \
	--tls-key-file <path to your client key> \
	--tls-ca-file <path to your CA> \
	--ep localhost -p 3036 -u root -pw root --pl mysql --db temporal update-schema -d ./schema/mysql/v57/temporal/versioned/
```

**Example visibility schema upgrade:**

```bash
./temporal-sql-tool \
	--tls \
	--tls-enable-host-verification \
	--tls-cert-file <path to your client cert> \
	--tls-key-file <path to your client key> \
	--tls-ca-file <path to your CA> \
	--ep localhost -p 3036 -u root -pw root --pl mysql --db temporal_visibility update-schema -d ./schema/mysql/v57/visibility/versioned/
```

### Manage cluster

We recommend preparing a staging cluster and then do the following to verify the upgrade is successful:

1. Create some simulation load on the staging cluster.
2. Upgrade the database schema in the staging cluster.
3. Wait and observe for few minutes to verify that there is no unstable behavior from both the server and the simulation load logic.
4. Upgrade the server.
5. Now do the same to the live environment cluster.
