# WardServiceApp

## Overview

Provides lists of wards and departments.

Part of the [HealthSafe](../README.md) project. Independent Maven module, no
parent pom.

MQ: this service subscribes to the ActiveMQ topic `staffing-events-topic` — see [`../common/`](../common) — and publishes to the ActiveMQ queue `equipment-failure-queue` when it detects an equipment failure on one of its wards, consumed by [`../equipment-alert-service`](../equipment-alert-service). Broker URL, topic name, and queue name come from the common `co.wethinkcode.healthsafe.mq.MqConfig` class alongside it in this module.

REST: called by `staffing-service` (`../staffing-service`) and `alert-level-service` (`../alert-level-service`) — see [Integration contracts](../README.md#integration-contracts) in the root README for the endpoint shapes.

## Project structure

```
ward-service/
├── pom.xml
├── README.md
└── src/main/java/co/wethinkcode/healthsafe/
    ├── WardServiceApp.java
    ├── Ward.java
    ├── WardCatalog.java
    └── mq/
        ├── MqConfig.java
        └── MqManager.java
```

## Build

```
mvn package
```

## Run

```
java -jar target/ward-service.jar
```

Listens on port `7031`. Bootstrap retries until ingestion-service is reachable,
so startup order doesn't matter.

## Test

No automated tests. Manually verify it's up:

```
curl http://localhost:7031/health   # -> OK
curl http://localhost:7031/wards    # -> wards cached from ingestion-service
```
