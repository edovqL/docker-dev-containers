# Docker Development Containers

A comprehensive Docker Compose setup providing multiple development databases and services for local development environments.

## Services Included

This setup provides the following services:

- **MariaDB** (MySQL-compatible) - Port 32768
- **MongoDB** - Port 32769
- **Redis** - Port 32770
- **PostgreSQL** - Port 32771
- **Elasticsearch** - Ports 32774 (HTTP), 32773 (Transport)
- **Mosquitto MQTT Broker** - Ports 1883 (MQTT), 9001 (WebSocket)
- **Google Cloud Pub/Sub Emulator** - Port 8888
- **RabbitMQ** - Ports 5672 (AMQP), 15672 (Management UI)
- **Apache Kafka** with **Zookeeper** - Ports 9092 (Kafka), 2181 (Zookeeper)

## Prerequisites

- Docker
- Docker Compose

## Quick Start

### Option 1: Using Helper Scripts

Start all services:

```bash
./bin/up
```

Stop all services:

```bash
./bin/down
```

### Option 2: Using Docker Compose Directly

Start all services in detached mode:

```bash
docker-compose up -d
```

Stop all services:

```bash
docker-compose down
```

View logs:

```bash
docker-compose logs -f
```

## Service Details & Connection Information

### MariaDB (MySQL-compatible)

- **Port**: 32768
- **Username**: root
- **Password**: (empty)
- **Connection**: `mysql -h localhost -P 32768 -u root`

### MongoDB

- **Port**: 32769
- **Username**: root
- **Password**: root
- **Connection**: `mongodb://root:root@localhost:32769`

### Redis

- **Port**: 32770
- **Connection**: `redis-cli -h localhost -p 32770`

### PostgreSQL

- **Port**: 32771
- **Username**: postgres
- **Password**: postgres
- **Connection**: `psql -h localhost -p 32771 -U postgres`

### Elasticsearch

- **HTTP Port**: 32774
- **Transport Port**: 32773
- **URL**: http://localhost:32774
- **Health Check**: `curl http://localhost:32774/_cluster/health`

### Mosquitto MQTT Broker

- **MQTT Port**: 1883
- **WebSocket Port**: 9001
- **Test**: `mosquitto_pub -h localhost -t test -m "Hello World"`

### Google Cloud Pub/Sub Emulator

- **Port**: 8888
- **Project ID**: REYSTAGING
- **Endpoint**: http://localhost:8888

### RabbitMQ

- **AMQP Port**: 5672
- **Management UI**: http://localhost:15672
- **Username**: rabbit
- **Password**: rabbit

### Apache Kafka

- **Port**: 9092
- **Zookeeper Port**: 2181
- **Bootstrap Servers**: localhost:9092

## Data Persistence

The following services have persistent data volumes:

- MariaDB: `sql-data`
- MongoDB: `mongo-data`
- PostgreSQL: `pg-data`
- Elasticsearch: `el-data`
- RabbitMQ: `mq-data`

To remove all data volumes:

```bash
docker-compose down -v
```

## Configuration

### Mosquitto MQTT

Custom configuration is mounted from `./config/mosquitto.conf`. The current configuration:

- Allows anonymous connections
- Listens on all interfaces (0.0.0.0:1883)
- Enables persistence

## Troubleshooting

### Check service status

```bash
docker-compose ps
```

### Check service health

All services include health checks for automatic monitoring and recovery:

```bash
# Check health status of all services
docker-compose ps --format "table {{.Name}}	{{.Status}}	{{.State}}"

# Check specific service health
docker inspect <container-name> --format "{{.State.Health.Status}}"

# Example: Check MariaDB health
docker inspect mariadb-dev --format "{{.State.Health.Status}}"
```

### Health Check Commands by Service

#### Database Services

```bash
# MariaDB
docker exec mariadb-dev mysqladmin ping -h localhost

# MongoDB
docker exec mongo-dev mongosh --eval "db.adminCommand('ping')"

# PostgreSQL
docker exec postgres-dev pg_isready -U postgres

# Redis
docker exec redis-dev redis-cli ping
```

#### Search & Analytics

```bash
# Elasticsearch
curl http://localhost:32774/_cluster/health
```

#### Message Brokers

```bash
# RabbitMQ
docker exec rabbitmq-dev rabbitmq-diagnostics ping

# Mosquitto MQTT
docker exec mosquitto-dev mosquitto_pub -h localhost -t health -m ping

# Kafka
docker exec kafka kafka-broker-api-versions --bootstrap-server localhost:9092

# Zookeeper
docker exec zookeeper sh -c "echo ruok | nc localhost 2181"
```

#### Cloud Emulators

```bash
# Pub/Sub Emulator
curl -f http://localhost:8085/v1/projects/STAGING
```

### Health Status Meanings

- **healthy** - Service is running and responding correctly
- **unhealthy** - Service failed health checks (will restart automatically)
- **starting** - Service is initializing, health checks not yet passed
- **none** - No health check configured

### View logs for a specific service

### Restart a specific service

```bash
docker-compose restart <service-name>
```

### Check if ports are available

```bash
# Check if any of the ports are already in use
lsof -i :32768,32769,32770,32771,32774,32773,1883,9001,8888,5672,15672,9092,2181
```

### Free up disk space

Remove unused containers, networks, and images:

```bash
docker system prune -a
```

## Common Use Cases

### Database Development

- Use MariaDB for MySQL-compatible applications
- Use PostgreSQL for applications requiring advanced SQL features
- Use MongoDB for document-based applications

### Message Queue Testing

- Use RabbitMQ for AMQP-based messaging
- Use Kafka for high-throughput event streaming
- Use Mosquitto for IoT/MQTT applications

### Search and Analytics

- Use Elasticsearch for full-text search and analytics

### Caching

- Use Redis for caching and session storage

### Cloud Development

- Use Pub/Sub emulator for Google Cloud development

## Security Note

⚠️ **Warning**: This setup is intended for local development only. The default configurations use weak or no passwords and should never be used in production environments.
