# AlertLevelServiceApp

## Overview

Tracks the hospital Emergency Status (0-8, 8 = full Code Blue).

Part of the [HealthSafe](../README.md) project. Independent Maven module, no
parent pom.

REST: called by `staffing-service` (`../staffing-service`) to read the current
status when computing on-call schedules — see [Integration contracts](../README.md#integration-contracts)
in the root README for the endpoint shapes.

## Project structure

```
alert-level-service/
├── pom.xml
├── README.md
└── src/
    ├── main/java/co/wethinkcode/healthsafe/
    │   ├── AlertLevelServiceApp.java
    │   └── AlertLevelTracker.java
    └── test/java/co/wethinkcode/healthsafe/
        └── AlertLevelTrackerTest.java
```

## Build

```
mvn package
```

## Run

```
java -jar target/alert-level-service.jar
```

Listens on port `7032`. `GET /alert-level` reads the current status;
`PUT`/`POST /alert-level` set it (400 for out-of-range or malformed input).

## Test

Unit tests cover the 0-8 range validation:

```
mvn test
```

Manually verify it's up:

```
curl http://localhost:7032/health   # -> OK
curl http://localhost:7032/alert-level   # -> { "level": 0 }
```
