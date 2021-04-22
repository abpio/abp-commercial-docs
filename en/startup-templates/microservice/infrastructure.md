# Microservice Startup Template Infrastructure

> Abp Microservice Startup Template contains shared modules that are used in applications, gateways and microservices. There are also external services run on containers where some of them are mandatory for microservice template to run such as Redis and RabbitMQ.

![overall-applications](../../images/overall-solution.png)

Shared folder in microservice template solution contains **DbMigrator** project for centralized database migration and data seeding for microservices and shared modules used in applications, microservices and gateways. 

**Infrastructure services run on docker containers** as default and some of the services are **required** for microservice template to run properly while some of them are optional. [Docker compose](https://docs.docker.com/compose/) is used for running the infrastructure services. Two docker-compose.yml files are used for service configurations and they are located under *etc/docker* folder. While `docker-compose.infrastructure.yml` file contains `image`, `container_name`, `network` and `volume` data, `docker-compose.infrastructure.override.yml` file contains service `ports` and `environment` data. 

Docker-compose file defines

- Sql-Server (required as default)
- Redis (required)
- RabbitMQ (required)
- ElasticSearch
- Kibana
- Grafana
- Prometheus

services that run on a predefined **external docker-network**. Use `up.ps1` powershell command located in the same *etc/docker* folder to run the external infrastructure images that will execute the commands

```powershell
docker network create mycompanyname.myprojectname-network
docker-compose -f docker-compose.infrastructure.yml -f docker-compose.infrastructure.override.yml up -d
```

which creates the external docker network for infrastructure services and runs the docker-compose files in detached mode. To stop and remove the containers, use `down.ps1` powershell command. 

> You can use a single docker file by merging **docker-compose.infrastructure.yml** and **docker-compose.infrastructure.override.yml** files as you desire.

![overall-applications](../../images/running-infrastructure.png)

## Redis Integration & Configuration

[Redis cache](https://docs.abp.io/en/abp/latest/Redis-Cache) is a **required** service for the microservice solution template. It is used for caching permissions and such.

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

Default RabbitMQ-management port is **15672** and UI can be accessed with default `guest` username/password.

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

[ElasticSearch](https://www.elastic.co/elasticsearch/) is used for [Serilog](https://serilog.net/) integration for logging and [Kibana](https://www.elastic.co/kibana) is used as the visualization tool that integrated to ElasticSearch. Docker-compose service configuration for these services is defined as below

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

The ElasticSearch integration of Serilog configuration is done at **SerilogConfigurationHelper** located in *Shared.Hosting.AspNetCore* shared project. The *IndexFormat* is declared as `MyProjectName-log-{0:yyyy.MM}` under this configuration.

After running ElasticSearch in container, its endpoint should be exposed as below

![overall-applications](../../images/elastic-search.png)

Use the *IndexFormat* explained above to create preferred UI visualization data.

![kibana](../../images/kibana.png)

Configuration for each application and microservices is located under *ElasticSearch* section of appsettings

```json
"ElasticSearch": {
  "Url": "http://localhost:9200"
},
```

## Grafana & Prometheus Integration

[Grafana](https://grafana.com/) provides dashboards and data analysis from [Prometheus](https://prometheus.io/) metrics. Docker-compose service configuration for these services is defined as below

```yaml
grafana:
    container_name: grafana
    image: grafana/grafana
    volumes:
      - ../grafana/storage:/var/lib/grafana
    networks:
      - mycompanyname.myprojectname-network
    ports:
      - "3000:3000"
      
  prometheus:
    container_name: prometheus
    image: prom/prometheus
    volumes:
      - ../prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - ../prometheus/storage:/prometheus
    networks:
      - mycompanyname.myprojectname-network
    ports:
      - "9090:9090"  
```

Prometheus scrape configuration, *prometheus.yml* file, is mounted and located under *etc/prometheus* folder.

> Grafana storage directory is mounted from *etc/granfana/storage* folder and Prometheus storage directory is from *etc/prometheus/storage*. You can also use [docker volumes](https://docs.docker.com/storage/volumes/#use-a-volume-with-docker-compose) instead of mounting directories.

Navigate to Prometheus targets to check endpoint targets

![prometheus-targets](../../images/prometheus-targets.png)

> If you are not running the solution on docker, you may have to change the prometheus *static_config* targets such as `-targets: ['auth-server']` to `-targets: ['host.docker.internal:44322']`

Run Grafana and add Prometheus as *Data Source*![grafana-add-source](../../images/grafana-add-source.png)

> For first time entry, default user name and password is **admin/admin**

Add dashboard using prometheus data source

![grafana-dashboard](../../images/grafana-dashboard.png)

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

Shared modules are as the name implies; modules and configurations that are used in other applications, services and/or microservices to prevent code duplication and centralize basic configurations. There are 5 shared modules in Abp Microservice Template solution.

### Localization

The *Shared.Localization* project contains configuration for [Virtual File System](https://docs.abp.io/en/abp/latest/Virtual-File-System) and **solution-wide** localization. 

```csharp
Configure<AbpVirtualFileSystemOptions>(options =>
{
    options.FileSets.AddEmbedded<MyProjectNameSharedLocalizationModule>();
});

Configure<AbpLocalizationOptions>(options =>
{
    options.Resources
        .Add<MyProjectNameResource>("en")
        .AddBaseTypes(
            typeof(AbpValidationResource)
        ).AddVirtualJson("/Localization/MyProjectName");

    options.DefaultResourceType = typeof(MyProjectNameResource);
});
```

The **SharedLocalizationModule** is depended by all applications, gateways and microservices.

> Use provided localization files if you want your localization keys available in all applications, gateways and microservices.

### Hosting

*Shared.Hosting* project contains **database configurations** in SharedHostingModule service configuration which is used in all other hosting modules. 

Each module with database connection in abp has its own connection string so that it can be deployed to any database individually when needed. This is achieved by [Connection Strings Management](https://docs.abp.io/en/abp/latest/Connection-Strings). Since *infrastructural microservices* such as [AdministrationService](microservices.md#AdministrationService), [IdentityService](microservices.md#IdentityService) and [SaasService](microservices.md#SaasService) uses one or more modules to perform, each module database configuration must be added to related database explicitly so that the module can connect to it's database.

Instead of adding all module connection strings in microservice connection string configuration explicitly; related connection strings are mapped into a single connection string. 

```csharp
private void ConfigureDatabaseConnections()
{
    Configure<AbpDbConnectionOptions>(options =>
    {
        options.Databases.Configure("SaasService", database =>
        {
            database.MappedConnections.Add("Saas");
            database.IsUsedByTenants = false;
        });
        
        options.Databases.Configure("AdministrationService", database =>
        {
            database.MappedConnections.Add("AbpAuditLogging");
            database.MappedConnections.Add("AbpPermissionManagement");
            database.MappedConnections.Add("AbpSettingManagement");
            database.MappedConnections.Add("AbpFeatureManagement");
            database.MappedConnections.Add("AbpLanguageManagement");
            database.MappedConnections.Add("TextTemplateManagement");
            database.MappedConnections.Add("AbpBlobStoring");
        });
        
        options.Databases.Configure("IdentityService", database =>
        {
            database.MappedConnections.Add("AbpIdentity");
            database.MappedConnections.Add("AbpIdentityServer");
        });

        options.Databases.Configure("ProductService", database =>
        {
            database.MappedConnections.Add("ProductService");
        });
    });
}
```

### Hosting AspNetCore



### Hosting Microservices



### Hosting Gateways

### 
