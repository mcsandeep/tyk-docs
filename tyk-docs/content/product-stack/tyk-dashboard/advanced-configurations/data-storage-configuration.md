---
title: "Data Storage Configuration"
tags: ["Database", "Options", "Data storage", "MongoDB", "SQL", "PostgreSQL", "Dashboard"]
description: "How to configure Tyk data storage layers"
---

Tyk stores a variety of data in 4 separate data storage layers. You can configure each layer separately to use one of our supported database platforms. Alternatively a single platform can be used for all layers. The 4 data storage layers are as follows:

1. **Main**: Stores configurations of: APIs, Policies, Users and User Groups.
2. **Aggregate Analytics**: Data used to display Dashboard charts and [analytics]({{< ref "tyk-dashboard-analytics" >}}).
3. **Logs**: When [detailed logging]({{< ref "tyk-stack/tyk-pump/useful-debug-modes#what-is-detailed-request-logging" >}}) is enabled, request and response data is logged to storage. These logs can previewed in the Dashboard [log browser]({{< ref "tyk-stack/tyk-manager/analytics/log-browser" >}}).
4. **Uptime**: Uptime test analytics.

Being extensible, Tyk supports storing this data across different databases (MongoDB, MySQL and PostgreSQL etc.). For example, Tyk can be configured to store analytics in PostgreSQL, logs in MongoDB and uptime data in MySQL.

As illustrated below it can be seen that Tyk Pump writes to one or more external data sources via a Redis store. Conversely, Tyk Dashboard reads this data from the external data sources.

{{< img src="/img/diagrams/diagram_docs_pump-open-source@2x.png"  alt="Tyk Dashboard Pump Architecture" >}}

The following details are required to manage this configuration:

- Data storage layer type
- Database engine
- Database connection string

The remainder of this document explains how to configure Tyk Dashboard and Tyk Pump to read and write from one or more data storage layers, respectively.

## How To Configure Tyk Dashboard To Read From A Data Storage Layer

Tyk Dashboard has configuration environment variables for each data storage layer in the following format:

```console
TYK_DB_STORAGE_<LAYER>_TYPE
TYK_DB_STORAGE_<LAYER>_CONNECTIONSTRING
```

where _LAYER_ can be _ANALYTICS_, _LOGS_ or _UPTIME_.

For example, to configure Tyk Dashboard to read logs from a mongo database, the following environment variables are required:

```console
TYK_DB_STORAGE_LOGS_TYPE=mongo
TYK_DB_STORAGE_LOGS_CONNECTIONSTRING=mongodb://db_host_name:27017/tyk_analytics
```

The full set of environment variables are listed below:

```console
TYK_DB_STORAGE_MAIN_TYPE
TYK_DB_STORAGE_MAIN_CONNECTIONSTRING
TYK_DB_STORAGE_LOGS_TYPE
TYK_DB_STORAGE_LOGS_CONNECTIONSTRING
TYK_DB_STORAGE_ANALYTICS_TYPE
TYK_DB_STORAGE_ANALYTICS_CONNECTIONSTRING
TYK_DB_STORAGE_UPTIME_TYPE
TYK_DB_STORAGE_UPTIME_CONNECTIONSTRING
```

It should be noted that Tyk will attempt to use the configuration for the _main_ data storage layer when no corresponding configuration is available for logs, uptime or analytics.

Please refer to the [storage configuration]({{< ref "tyk-dashboard/configuration#storage" >}}) section to explore the parameters for configuring Tyk Dashboard to read from different storage layers.

## How To Configure Tyk Pump To Write To Data Storage Layers?

Please consult the Pump configuration [guide]({{< ref "tyk-pump/tyk-pump-configuration/tyk-pump-dashboard-config#3-sql-uptime-pump" >}}) for an explanation of how to configure Tyk Pump to write to different storage layers.

The remainder of this section explains the _environment variables_ that can be used to configure Tyk Pump to write to the following data storage layers:

- Uptime
- Aggregated Analytics
- Logs

### How To Configure Tyk Pump To Write Uptime Data?

Tyk Pump can be configured to write uptime data to SQL (Postgres and SQL Lite) and Mongo. The default behavior is to write to Mongo.

#### How To Configure Tyk Pump To Write Uptime Data To A PostgreSQL Database?

Tyk Pump can be configured to write to a PostgreSQL database, using the following environment variables:

- _TYK_PMP_UPTIMEPUMPCONFIG_UPTIMETYPE_: Set to _sql_ to configure Pump to store logs in a SQL based database.
- _TYK_PMP_UPTIMEPUMPCONFIG_TYPE_: Set to _postgres_ to configure Pump to use a PostgreSQL database for uptime data.
- _TYK_PMP_UPTIMEPUMPCONFIG_CONNECTIONSTRING_: Set the connection string for the PostgreSQL database.

An example configuration is shown below:

```console
TYK_PMP_UPTIMEPUMPCONFIG_UPTIMETYPE=sql
TYK_PMP_UPTIMEPUMPCONFIG_TYPE=postgres
TYK_PMP_UPTIMEPUMPCONFIG_CONNECTIONSTRING=user=postgres password=topsecretpassword host=tyk-postgres port=5432 database=tyk_analytics
```

Further details for configuring an uptime SQL database are available [here]({{< ref "tyk-pump/tyk-pump-configuration/tyk-pump-environment-variables#sql-uptime-pump" >}})

#### How To Configure Tyk Pump To Write Uptime Data To A Mongo Database?

Tyk Pump can be configured to write to a Mongo database, using the following environment variables:

- _TYK_PMP_UPTIMEPUMPCONFIG_UPTIMETYPE_: Set to _mongo_ to configure Pump to store logs in a Mongo database.
- _TYK_PMP_UPTIMEPUMPCONFIG_MONGOURL_: Set to Mongo database connection string.
- _TYK_PMP_UPTIMEPUMPCONFIG_COLLECTIONNAME_: Set to the name of the collection used to store uptime analytics.

```console
TYK_PMP_UPTIMEPUMPCONFIG_UPTIMETYPE=mongo
TYK_PMP_UPTIMEPUMPCONFIG_MONGOURL=mongodb://db_host_name:27017/tyk_uptime_db
TYK_PMP_UPTIMEPUMPCONFIG_COLLECTIONNAME=umptime_analytics
```

Further details for configuring a Tyk Mongo Pump are available [here]({{< ref "tyk-pump/tyk-pump-configuration/tyk-pump-environment-variables#mongo-uptime-pump" >}}>)

### How to Configure Tyk Pump To Write Logs?

Tyk Pump can be configured to write logs to Mongo or SQL based databases.

#### How To Configure Tyk Pump To Write Logs To A Mongo Database?

Tyk Pump can be configured to write to a Mongo database by setting the following environment variables:

- _TYK_PMP_PUMPS_LOGS_TYPE_: Set to _mongo_ to configure Pump to store logs in a Mongo database.
- _TYK_PMP_PUMPS_LOGS_META_MONGOURL_: Set the connection string for the Mongo database.
- _TYK_PMP_PUMPS_LOGS_META_COLLECTION_NAME_: Set the name of the collection that will store logs in the Mongo database.

An example is listed below:

```console
TYK_PMP_PUMPS_LOGS_TYPE=mongo
TYK_PMP_PUMPS_LOGS_META_MONGOURL=mongodb://tyk-mongo:27017/tyk_analytics
TYK_PMP_PUMPS_LOGS_META_COLLECTIONNAME=tyk_logs
```

#### How To Configure Tyk Pump To Write Logs To A SQL Database?

Tyk Pump can be configured to write logs to SQL based databases. This section provides examples for how to configure Tyk Pump to write to Postgres or MySQL databases.

##### How To Configure Tyk Pump To Write Logs To A PostgreSQL Database?

Tyk Pump can be configured to write to a PostgreSQL database by setting the following environment variables:

- _TYK_PMP_PUMPS_LOGS_TYPE_: Set to _SQL_ to configure Pump to store logs in a SQL based database.
- _TYK_PMP_PUMPS_LOGS_META_TYPE_: Set to _postgres_ to configure Pump to store logs in a PostgreSQL database.
- _TYK_PMP_PUMPS_LOGS_META_CONNECTIONSTRING_: Set the name of the connection string for the PostgreSQL database.

```console
TYK_PMP_PUMPS_LOGS_TYPE=SQL
TYK_PMP_PUMPS_LOGS_META_TYPE=postgres
TYK_PMP_PUMPS_LOGS_META_CONNECTIONSTRING=user=postgres password=topsecretpassword host=tyk-postgres port=5432 database=tyk_analytics
```

##### How To Configure Tyk Pump To Write Logs To A MySQL Database?

Tyk Pump can be configured to write to a MySQL database by setting the following environment variables:

- _TYK_PMP_PUMPS_LOGS_TYPE_: Set to _SQL_ to configure Pump to store logs in a SQL based database.
- _TYK_PMP_PUMPS_LOGS_META_TYPE_: Set to _mysql_ to configure Pump to store logs in a MySQL database.
- _TYK_PMP_PUMPS_LOGS_META_CONNECTIONSTRING_: Set the name of the connection string for the MySQL database.

```console
TYK_PMP_PUMPS_LOGS_TYPE=SQL
TYK_PMP_PUMPS_LOGS_META_TYPE=mysql
TYK_PMP_PUMPS_LOGS_META_CONNECTIONSTRING=mysql://db_host_name:3306/tyk_logs_db
```

### How To Configure Tyk Pump To Write Aggregated Analytics Data?

Aggregated analytics corresponds to data that is used for the display of charts and graphs in [dashboard]({{< ref "tyk-dashboard-analytics" >}}). Tyk Pump can be configured to write aggregated analytics data to SQL based databases or MongoDB.

#### How To Configure Tyk Pump To Write Aggregated Analytics To A SQL Database?

Storage of aggregated analytics data has been tested with PostgreSQL and SqlLite databases. The following environment variables can be used to manage this configuration:

- _TYK_PMP_PUMPS_SQLAGGREGATE_TYPE_: Set to _sql_aggregate_ to configure Pump to store aggregated analytics data for charts and graphs in dashboard to a SQL based database.
- _TYK_PMP_PUMPS_SQLAGGREGATE_META_TYPE_: The database engine used to store aggregate analytics. Tested values are _postgres_ or _sqlite_.
- _TYK_PMP_PUMPS_SQLAGGREGATE_META_CONNECTIONSTRING_: The connection string for the database that will store the aggregated analytics.

The example below demonstrates how to configure Tyk Pump to write aggregated to a PostgreSQL database:

```console
TYK_PMP_PUMPS_SQLAGGREGATE_TYPE=SQL
TYK_PMP_PUMPS_SQLAGGREGATE_META_TYPE=postgres
TYK_PMP_PUMPS_SQLAGGREGATE_META_CONNECTIONSTRING=user=postgres password=topsecretpassword host=tyk-postgres port=5432 database=tyk_aggregated_analytics
```

#### How To Configure Tyk Pump To Write Aggregated Analytics To A Mongo Database?

Tyk Pump can be configured to write aggregated analytics data to MongoDB. Aggregated analytics are written to a collection named _z*tyk_analyticz_aggregate*{ORG ID}_, where _ORG_ID_ corresponds to the ID of your organization assigned by Tyk.

The following environment variables can be used as a minimum to manage this configuration:

- _TYK_PMP_PUMPS_MONGOAGGREGATE_TYPE_: Set to _mongo-pump-aggregate_ to configure Pump to store aggregated analytics data in a MongoDB database.
- _TYK_PMP_PUMPS_MONGOAGGREGATE_META_MONGOURL_: Mongo database connection URL.

An example is given below:

```console
- TYK_PMP_PUMPS_MONGOAGGREGATE_TYPE=mongo-pump-aggregate
- TYK_PMP_PUMPS_MONGOAGGREGATE_META_MONGOURL=mongodb://db_host_name:27017/tyk_aggregated_analytics_db
```
