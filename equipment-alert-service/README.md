# EquipmentAlertServiceApp

## Overview

Uses a Queue to guarantee delivery of critical medical equipment failure alerts.

Part of the [HealthSafe](../README.md) project — its alerting service.
Independent Maven module, no parent pom.

Mechanism: ActiveMQ Queue (guaranteed delivery)

Queue: `equipment-failure-queue`

- Producer: `ward-service` (`../ward-service`) — publishes when it detects an
  equipment failure on one of its wards.
- Consumer: this service.

Broker URL and queue name are shared via a common `co.wethinkcode.healthsafe.mq.MqConfig`
class (`BROKER_URL`, `QUEUE`). It's identical in every participating service's own
source tree — each service here is an independent Maven project with no shared
parent pom, so the common package is duplicated rather than imported from one place.

A queue (not a topic) is used deliberately: each failure alert must be delivered to
exactly one consumer and processed at least once, even if this service is briefly
down — unlike the broadcast `staffing-events-topic` in [`../common/`](../common),
where every subscriber gets every message.

## Project structure

```
equipment-alert-service/
├── pom.xml
├── README.md
└── src/main/java/co/wethinkcode/healthsafe/
    ├── EquipmentAlertServiceApp.java
    └── mq/
        ├── MqConfig.java
        └── EquipmentAlertConsumer.java
```

## Build

```
mvn package
```

## Run

```
java -jar target/equipment-alert-service.jar
```

Listens on port `7034`. Handled alerts are readable at `GET /alerts`.

## Test

No automated tests. Manually verify it's up and consuming:

```
curl http://localhost:7034/health   # -> OK
```

With the broker running (`../common && docker compose up -d`) and
`ward-service` up, report a failure and watch it arrive:

```
curl -X POST http://localhost:7031/wards/W-01/equipment-failures \
  -H 'Content-Type: application/json' \
  -d '{"equipment":"ventilator-3","failure":"alarm unresponsive"}'
curl http://localhost:7034/alerts
```
