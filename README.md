# Confluent Platform Docker Setup

This repository contains Docker Compose configurations to run **Kafka and Confluent Platform components** for development and testing.

## Repository Structure

```
cp-8.0/
├── docker-compose.yml
├── cp.yml
├── c3.yml
├── plugins/
└── vol/
```

### File Descriptions

| File                   | Purpose                                                                                          |
| ---------------------- | ------------------------------------------------------------------------------------------------ |
| **docker-compose.yml** | Runs a **single-node Kafka broker setup**                                                        |
| **cp.yml**             | Runs **Confluent Platform components** such as Kafka brokers, Schema Registry, and Kafka Connect |
| **c3.yml**             | Runs **Control Center, Prometheus, and Alertmanager**                                            |
| **plugins/**           | Directory for **Kafka Connect custom plugins/connectors**                                        |
| **vol/**               | Configuration files used by Control Center and monitoring components                             |

---

# Running the Environment

## 1. Start Single Node Kafka

Use this for simple local testing.

```bash
docker compose -f docker-compose.yml up -d
```

Stop it with:

```bash
docker compose -f docker-compose.yml down
```

---

# 2. Start Confluent Platform Components

This starts the Kafka cluster and core components:

* Kafka Brokers
* Schema Registry
* Kafka Connect

```bash
docker compose -f cp.yml up -d
```

Check running containers:

```bash
docker compose ps
```

Stop services:

```bash
docker compose -f cp.yml down
```

---

# 3. Start Control Center and Monitoring

This stack includes:

* Control Center
* Prometheus
* Alertmanager

Start services:

```bash
docker compose -f c3.yml up -d
```

Access Control Center:

```
http://localhost:9021
```

Stop services:

```bash
docker compose -f c3.yml down
```

---

# Kafka Connect Plugins

Custom Kafka Connect connectors should be placed in the **plugins directory**.

Example structure:

```
plugins/
 ├── jdbc/
 │   └── kafka-connect-jdbc.jar
 ├── debezium-mysql/
 │   └── debezium-connector-mysql.jar
```

Each connector should be placed in its **own folder**.

---

# Restart Kafka Connect After Adding Plugins

After adding new connectors, restart the Connect container so it reloads plugins.

```bash
docker compose restart connect
```

Verify connectors:

```bash
curl localhost:8083/connector-plugins
```

---

# Useful Commands

### Check running containers

```bash
docker compose ps
```

### View logs

```bash
docker logs -f connect
```

### List Kafka topics

```bash
docker exec -it broker-1 kafka-topics --bootstrap-server broker-1:29092 --list
```

---

# Notes

* Ensure Docker and Docker Compose are installed.
* Kafka Connect plugins must be placed inside the `plugins` directory before restarting the Connect container.
* Control Center and monitoring services rely on the Kafka cluster defined in `cp.yml`.

---
