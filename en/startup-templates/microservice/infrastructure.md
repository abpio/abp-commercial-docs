# Microservice Startup Template Infrastructure

> Abp Microservice Startup Template contains shared modules that are used in applications, gateways and microservices. There are also external services run on containers where some of them are mandatory for microservice template to run such as Redis and RabbitMQ.

![overall-applications](../../images/microservice-template-main-diagram.png)

Shared folder in microservice template solution contains **DbMigrator** project for centralized database migration and data seeding for microservices and shared modules used in applications, microservices and gateways. 

Infrastructure services are located under *etc/docker* folder using **docker-compose.infrastructure.yml** file to run on containers. Some of the services are **required** for microservice template to run properly and some of them are optional. Defined services in docker-compose are

- Sql-Server (required as default)
- Redis (required)
- RabbitMQ (required)
- ElasticSearch
- Kibana
- Grafana
- Prometheus

that runs on a predefined **external docker-network**. Use `up.ps1` powershell command located in the same *etc/docker* folder to run the external infrastructure images that will execute the commands

```powershell
docker network create mycompanyname.myprojectname-network
docker-compose -f docker-compose.infrastructure.yml -f docker-compose.infrastructure.override.yml up -d
```

which creates the external docker network for infrastructure services and runs the docker-compose files in detached mode. To stop and remove the containers, use `down.ps1` powershell command. 

> While **docker-compose.infrastructure.yml** file contains image, container name and volume related data; **docker-compose.infrastructure.override.yml** file contains port and environment data of the external services.

![overall-applications](../../images/running-infrastructure.png)

## Redis Integration & Configuration

[Redis cache](https://docs.abp.io/en/abp/latest/Redis-Cache) is a **required** service used in microservice solution template. It is used for caching permissions and such.

Redis runs on container with the main configurations are located in *docker-compose.infrastructure.yml* and port information in *docker-compose.infrastructure.override.yml* file. Redis docker-compose service configuration is as below

```yaml
redis:
    container_name: redis
    image: redis:6.0.10-alpine
    networks:
      - mycompanyname.myprojectname-network
    ports:
      - "6379:6379"  
```

All the microservices uses `AbpCachingStackExchangeRedisModule` dependency over *SharedHostingMicroservicesModule* and applications (AuthServer, Web, PublicWeb) references explicitly to *Volo.Abp.Caching.StackExchangeRedis* package.

Microservices and applications have their redis configuration in appsettings *Redis* section

```json
"Redis": {
  "Configuration": "localhost:6379"
}
```

> **Redis 6.0.10-alpine** image is used as default. You can change the image tag in *docker-compose.infrastructure.yml* file and default exposed **6379** port in *docker-compose.infrastructure.override.yml* file.

## RabbitMQ Integration & Configuration

[Distributed Event Bus](https://docs.abp.io/en/abp/latest/Distributed-Event-Bus) is the tool for async communication in microservice systems and abp microservice template solution uses [RabbitMQ integration](https://docs.abp.io/en/abp/latest/Distributed-Event-Bus-RabbitMQ-Integration) for this capability. It is a **required** service in the solution template used for on-the-fly database migration of the microservices. RabbitMQ docker-compose service configuration is as below

```yaml
rabbitmq:
    container_name: rabbitmq
    image: rabbitmq:3.8.11-management-alpine
    networks:
      - mycompanyname.myprojectname-network
    ports:
      - "15672:15672"
      - "5672:5672"  
```

Default RabbitMQ-management port is **15672** and UI can be accwith default `guest` username/password.

![overall-applications](../../images/rabbitmq-exchanges.png)

All the microservices uses `AbpEventBusRabbitMqModule` dependency over *SharedHostingMicroservicesModule* and applications (AuthServer, Web, PublicWeb) references explicitly to *Volo.Abp.EventBus.RabbitMQ* package.

Each application and microservice has it's configuration under appsettings *RabbitMQ* section.

```json
"RabbitMQ": {
  "Connections": {
    "Default": {
      "HostName": "localhost"
    }
  },
  "EventBus": {
    "ClientName": "MyProjectName_AuthServer",
    "ExchangeName": "MyProjectName"
  }
}
```

They all share the same **ExchangeName** and individual queues will be created after services start running.

![overall-applications](../../images/rabbitmq-queues.png)

> Minimal configurations are used to connect RabbitMQ. Check [RabbitMQ Integration docs](https://docs.abp.io/en/abp/latest/Distributed-Event-Bus-RabbitMQ-Integration#configuration) for more configuration settings.

## ElasticSearch & Kibana Integration

[ElasticSearch](https://www.elastic.co/elasticsearch/) is used for [Serilog](https://serilog.net/) integration for logging. ElasticSearch docker-compose service configuration is as below

```yaml
elasticsearch:
    container_name: elasticsearch
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.2
    volumes:
      - esdata:/usr/share/elasticsearch/data
    networks:
      - mycompanyname.myprojectname-network
    environment:
      - xpack.monitoring.enabled=true
      - xpack.watcher.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - discovery.type=single-node
    ports:
      - "9200:9200"  
```

After running ElasticSearch in container, its endpoint should be exposed as below

![overall-applications](../../images/elastic-search.png)

[Kibana](https://www.elastic.co/kibana) is used as the visualization tool integrated to Elasticsearch and configured in docker-compose service as below

```yaml
  kibana:
    container_name: kibana
    image: docker.elastic.co/kibana/kibana:7.10.2
    depends_on:
      - elasticsearch
    networks:
      - mycompanyname.myprojectname-network
    environment:
      - ELASTICSEARCH_URL=http://host.docker.internal:9200
    ports: 
      - "5601:5601"  
```

![kibana](../../images/kibana.png)

## Prometheus & Grafana Integration

## Database Migrator

> Infrastructure services must run before running Database Migrator since default connection string for microservices use sql-server running on container.

**DbMigrator**, under shared folder in the microservice template solution, is a console application that runs the `MigrateAsync` method of *DbMigrationService*. This service migrates *Host* and *Tenant* databases and seeds data.

Host migration migrates each microservices

```csharp
private async Task MigrateHostAsync(CancellationToken cancellationToken)
{
    _logger.LogInformation("Migrating Host side...");
    await MigrateAllDatabasesAsync(null, cancellationToken);
    await SeedDataAsync();
}

private async Task MigrateAllDatabasesAsync(
    Guid? tenantId,
    CancellationToken cancellationToken)
{
    using (var uow = _unitOfWorkManager.Begin(requiresNew: true, isTransactional: false))
    {
        if (tenantId == null)
        {
            /* SaaS schema should only be available in the host side */
            await MigrateDatabaseAsync<SaasServiceDbContext>(cancellationToken);
        }

        await MigrateDatabaseAsync<AdministrationServiceDbContext>(cancellationToken);
        await MigrateDatabaseAsync<IdentityServiceDbContext>(cancellationToken);
        await MigrateDatabaseAsync<ProductServiceDbContext>(cancellationToken);

        await uow.CompleteAsync(cancellationToken);
    }

    _logger.LogInformation(
        $"All databases have been successfully migrated ({(tenantId.HasValue ? $"tenantId: {tenantId}" : "HOST")}).");
}
```

## Shared Modules

## Tye Integration