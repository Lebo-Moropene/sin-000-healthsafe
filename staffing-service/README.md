# StaffingServiceApp

## Overview

Provides on-call schedules for doctors based on ward and status.

Part of the [HealthSafe](../README.md) project. Independent Maven module, no
parent pom.

MQ: this service publishes to the ActiveMQ topic `staffing-events-topic` — see [`../common/`](../common). Broker URL and topic name come from the common `co.wethinkcode.healthsafe.mq.MqConfig` class alongside it in this module.

REST: calls `ward-service` (`../ward-service`) to validate the ward and
`alert-level-service` (`../alert-level-service`) to read the current Emergency
Status before computing a schedule — see [Integration contracts](../README.md#integration-contracts)
in the root README for the endpoint shapes.

## Project structure

```
staffing-service/
├── pom.xml
├── README.md
└── src/
    ├── main/java/co/wethinkcode/healthsafe/
    │   ├── StaffingServiceApp.java
    │   ├── Ward.java
    │   ├── WardClient.java
    │   ├── AlertLevelClient.java
    │   ├── Schedule.java
    │   ├── ScheduleComputer.java
    │   └── mq/
    │       ├── MqConfig.java
    │       └── TopicPublisher.java
    └── test/java/co/wethinkcode/healthsafe/
        └── ScheduleComputerTest.java
```

## Build

```
mvn package
```

## Run

```
java -jar target/staffing-service.jar
```

Listens on port `7033`. `GET /schedule/{wardId}` returns the on-call schedule,
or 404/502/504 when a downstream service is missing or unreachable.

## Test

Unit tests cover the scheduling rules (staffing scales with Emergency Status,
critical departments start with two doctors, levels are clamped):

```
mvn test
```

Manually verify it's up:

```
curl http://localhost:7033/health   # -> OK
```
