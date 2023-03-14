# Docker Deployment using Docker Compose

````json
//[doc-params]
{
    "UI": ["MVC", "Blazor", "BlazorServer", "NG"],
    "DB": ["EF", "Mongo"],
    "Tiered": ["Yes", "No"]
}
````

> This document assumes that you prefer to use **{{ UI_Value }}** as the UI framework and **{{ DB_Value }}** as the database provider. For other options, please change the preference on top of this document.

This guide will be guide you through how to build docker images for your application and run on localhost using `docker compose`. You will learn the provided build scripts and docker compose files in detail and how to modify for production environment.

## Building Docker Images

Each application contains a dockerfile called `Dockerfile.local` for building the docker image. As naming implies, these dockerfiles are not multi-stage dockerfiles and requires project to be built in `Release` mode to create the image. Currently, if you are building your images using CI&CD pipeline, you either need to include the SDK to your pipeline before building the images or add your own [multi-stage dockerfiles](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/docker/building-net-docker-images?view=aspnetcore-7.0).

Since they are not multi-staged dockerfiles, if you want to build the images individually, you can navigate to the related to-be-hosted application folder and run 

``````powershell
dotnet publish -c Release
``````

to populate the **Release** folder first which will be used to build the docker images. Afterwards,  you can run 

```powershell
docker build -f Dockerfile.local -t mycompanyname/myappname:version .
```

to manually build your application image.

To ease the process, application templates provide a build script to build all the images with a single script under `etc/build` folder named as `build-images-locally.ps1`. Based on your application name, ui and type; build image script will be generated. 

{{ if UI == "MVC"}}

```powershell
param ($version='latest')

$currentFolder = $PSScriptRoot
$slnFolder = Join-Path $currentFolder "../../"

Write-Host "********* BUILDING DbMigrator *********" -ForegroundColor Green
$dbMigratorFolder = Join-Path $slnFolder "src/Acme.BookStore.DbMigrator"
Set-Location $dbMigratorFolder
dotnet publish -c Release
docker build -f Dockerfile.local -t acme/bookstore-db-migrator:$version .

Write-Host "********* BUILDING Web Application *********" -ForegroundColor Green
$webFolder = Join-Path $slnFolder "src/Acme.BookStore.Web"
Set-Location $webFolder
dotnet publish -c Release
docker build -f Dockerfile.local -t acme/bookstore-web:$version .

	{{ if Tiered == "Yes"}}
Write-Host "********* BUILDING Api.Host Application *********" -ForegroundColor Green
$hostFolder = Join-Path $slnFolder "src/Acme.BookStore.HttpApi.Host"
Set-Location $hostFolder
dotnet publish -c Release
docker build -f Dockerfile.local -t acme/bookstore-api:$version .

Write-Host "********* BUILDING AuthServer Application *********" -ForegroundColor Green
$authServerAppFolder = Join-Path $slnFolder "src/Acme.BookStore.AuthServer"
Set-Location $authServerAppFolder
dotnet publish -c Release
docker build -f Dockerfile.local -t acme/bookstore-authserver:$version .
	{{ end }}

### ALL COMPLETED
Write-Host "COMPLETED" -ForegroundColor Green
Set-Location $currentFolder
```

{{ end }}

{{ if UI == "NG"}}

```powershell
param ($version='latest')

$currentFolder = $PSScriptRoot
$slnFolder = Join-Path $currentFolder "../../"

Write-Host "********* BUILDING DbMigrator *********" -ForegroundColor Green
$dbMigratorFolder = Join-Path $slnFolder "src/Acme.BookStore.DbMigrator"
Set-Location $dbMigratorFolder
dotnet publish -c Release
docker build -f Dockerfile.local -t acme/bookstore-db-migrator:$version .

Write-Host "********* BUILDING Angular Application *********" -ForegroundColor Green
$angularAppFolder = Join-Path $slnFolder "../angular"
Set-Location $angularAppFolder
yarn
npm run build:prod
docker build -f Dockerfile.local -t acme/bookstore-angular:$version .

Write-Host "********* BUILDING Api.Host Application *********" -ForegroundColor Green
$hostFolder = Join-Path $slnFolder "src/Acme.BookStore.HttpApi.Host"
Set-Location $hostFolder
dotnet publish -c Release
docker build -f Dockerfile.local -t acme/bookstore-api:$version .

	{{ if Tiered == "Yes"}}
$authServerAppFolder = Join-Path $slnFolder "src/Acme.BookStore.AuthServer"
Set-Location $authServerAppFolder
dotnet publish -c Release
docker build -f Dockerfile.local -t acme/bookstore-authserver:$version .
	{{ end }}

### ALL COMPLETED
Write-Host "COMPLETED" -ForegroundColor Green
Set-Location $currentFolder
```

{{ end }}

{{ if UI == "Blazor" }}

{{ end }}

{{ if UI == "BlazorServer" }}

{{ end }}

The **image tag** is set to `latest` by default. You can update the `param $version` at the first line as you want to set as tag for your images. 

You can examine all the provided dockerfiles required to publish your application below;

### DBMigrator

DbMigrator is a console application that is used to migrate the database of your application and seed the initial important data to run your application. Such as pre-defined langauges, admin user and role, OpenIddict applications and scopes.

`Dockerfile.local` is provided under this project as below;

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:7.0
COPY bin/Release/net7.0/publish/ app/
WORKDIR /app
ENTRYPOINT ["dotnet", "BookStore.DbMigrator.dll"]
```

If you don't want to use the `build-images-locally.ps1` to build the images or to build this image individually and manually, navigate to **DbMigrator** folder and run:

```powershell
dotnet publish -c Release #Builds the projects in Release mode
docker build -f Dockerfile.local -t acme/bookstore-db-migrator:latest . #Builds the image with "latest" tag
```

{{ if UI == "NG" }}

### Angular

The angular application uses [nginx:alpine-slim](https://hub.docker.com/layers/library/nginx/alpine-slim/images/sha256-0f859db466fda2c52f62b48d0602fb26867d98edbd62c26ae21414b3dea8d8f4?context=explore) base image to host the angular application. You can modify the base image based on your preference int the `Dockerfile.local` which provided under the angular folder of your solution as below;

```dockerfile
FROM nginx:alpine-slim
WORKDIR /app
COPY dist/BookStore /usr/share/nginx/html
COPY dynamic-env.json /usr/share/nginx/html
COPY /nginx.conf  /etc/nginx/conf.d/default.conf
```

You can notice that, other than built angular application, there are two more files are copied into application image.

The `dynamic-env.json` file is an empty json file representing angular application's environment variables. This file will be overridden by environment variable on image runtime. If you examine the `environment.prod.ts` file under the `angular/src/environments` folder, there is a remote environment configuration:

```json
remoteEnv: {
    url: '/getEnvConfig',
    mergeStrategy: 'deepmerge'
  }
```

[This configuration is used to get the environment variables from a remote service](https://docs.abp.io/en/abp/latest/UI/Angular/Environment#remoteenvironment). This configuration is used to **override environment variables without rebuilding the image.** The url `/getEnvConfig` is defined in the `nginx.conf` file:

```nginx
server {
    listen       80;
    listen  [::]:80;
    server_name  _;
    
    #listen              443 ssl;
    #server_name         www.myapp.com;
    #ssl_certificate     www.myapp.com.crt;
    #ssl_certificate_key www.myapp.com.key;
    
	location / {
        root   /usr/share/nginx/html;        
        index  index.html index.htm;
        try_files $uri $uri/ /index.html =404;		
	}
	
	location /getEnvConfig {
		default_type 'application/json';
        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS' always;
        add_header 'Content-Type' 'application/json';
		try_files $uri /dynamic-env.json;
    }
    
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

This configuration allows returning the `dynamic-env.json` file as a static file in which ABP Angular application uses for environment variables in one of the first initial requests when rendering the page. **The `dynamic-env.json` file you need to override is located under `aspnet-core/etc/docker`** folder.

​	{{ if Tiered == "No" }}

```json
{
  "production": true,
  "application": {
    "baseUrl":"http://localhost:4200",		// https://myapp.com
    "name": "BookStore",
    "logoUrl": ""
  },
  "oAuthConfig": {
    "issuer": "https://localhost:44354/", 	// https://myapi.com/
    "redirectUri": "http://localhost:4200", // https://myapp.com
    "clientId": "BookStore_App",
    "responseType": "code",
    "scope": "offline_access openid profile email phone BookStore"
  },
  "apis": {
    "default": {
      "url": "https://localhost:44354",		// https://myapi.com
      "rootNamespace": "BookStore"
    },
    "AbpAccountPublic": {
      "url": "https://localhost:44354",		// https://myapi.com
      "rootNamespace": "AbpAccountPublic"
    }
  }
}
```

​	{{ end }}

​	{{ if Tiered == "Yes" }}

```json
{
  "production": true,
  "application": {
    "baseUrl":"http://localhost:4200",		// https://myapp.com
    "name": "BookStore",
    "logoUrl": ""
  },
  "oAuthConfig": {
    "issuer": "https://localhost:44334/",	// https://myauthserver.com/
    "redirectUri": "http://localhost:4200",	// https://myapp.com
    "clientId": "BookStore_App",
    "responseType": "code",
    "scope": "offline_access openid profile email phone BookStore"
  },
  "apis": {
    "default": {
      "url": "https://localhost:44354",		// https://myapi.com
      "rootNamespace": "BookStore"
    },
    "AbpAccountPublic": {
      "url": "https://localhost:44334",		// https://myauthserver.com
      "rootNamespace": "AbpAccountPublic"
    }
  }
}
```

​	{{ end }}

If you don't want to use the `build-images-locally.ps1` to build the images or to build this image individually and manually, navigate to **angular** folder and run:

```powershell
yarn 				#Restores the project
npm run build:prod	#Builds on production environment. You can also use "ng build  --prod" if you have the angular CLI
docker build -f Dockerfile.local -t acme/bookstore-angular:latest . #Builds the image with "latest" tag
```

{{ end }}

{{ if UI == "MVC" }}

{{ end }}



{{ if UI == "BlazorServer" }}

{{ end }}



{{ if Tiered == "No"  }}

​	{{ if UI == "NG" || if UI == "Blazor"  }}

### Http.Api.Host

This is the backend application that contains the authserver functionality as well. The `dockerfile.local` is located under the `Http.Api.Host` project as below;

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
COPY bin/Release/net7.0/publish/ app/
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
RUN dotnet dev-certs https -v -ep authserver.pfx -p 2D7AA457-5D33-48D6-936F-C48E5EF468ED

FROM base AS final
WORKDIR /app
COPY --from=build /src .

ENTRYPOINT ["dotnet", "Acme.BookStore.HttpApi.Host.dll"]
```

Since it contains authserver within, it also uses multi-stages to generate `authserver.pfx` file which is **used by OpenIddict as signing and encryption certificate**. This configuration is found under the `PreConfigureServices` method of the **HttpApiHostModule**:

```csharp
if (!hostingEnvironment.IsDevelopment())
{
    PreConfigure<AbpOpenIddictAspNetCoreOptions>(options =>
    {
        options.AddDevelopmentEncryptionAndSigningCertificate = false;
    });

    PreConfigure<OpenIddictServerBuilder>(builder =>
    {
        builder.AddSigningCertificate(GetSigningCertificate(hostingEnvironment, configuration));
        builder.AddEncryptionCertificate(GetSigningCertificate(hostingEnvironment, configuration));
        builder.SetIssuer(new Uri(configuration["AuthServer:Authority"]));
    });
}
```

This configuration disables the *DevelopmentEncryptionAndSigningCertificate* and uses a self-signed certificate called `authserver.pfx`. for **signing and encrypting the tokens**. This certificate is being created when the docker image is build using the `dotnet dev-certs` tooling . It is a sample generated certificate and it is **recommended** to update it for production environment. You can check the [OpenIddict Encryption and signing credentials documentation](https://documentation.openiddict.com/configuration/encryption-and-signing-credentials.html) for different options and customization.

The `GetSigningCertificate` method is a private method located under the same **HttpApiHostModule**:

```csharp
private X509Certificate2 GetSigningCertificate(IWebHostEnvironment hostingEnv, IConfiguration configuration)
{
    var fileName = "authserver.pfx";
    var passPhrase = "2D7AA457-5D33-48D6-936F-C48E5EF468ED";
    var file = Path.Combine(hostingEnv.ContentRootPath, fileName);

    if (!File.Exists(file))
    {
        throw new FileNotFoundException($"Signing Certificate couldn't found: {file}");
    }

    return new X509Certificate2(file, passPhrase);
}
```

> You can always create any self-signed certificate using any other tooling outside of the dockerfile. You need to keep on mind to set them as **embedded resource** since the `GetSigningCertificate` method will be checking this file physically. 

If you don't want to use the `build-images-locally.ps1` to build the images or to build this image individually and manually, navigate to **Http.Api.Host** folder and run:

```powershell
dotnet publish -c Release #Builds the projects in Release mode
docker build -f Dockerfile.local -t acme/bookstore-api:latest . #Builds the image with "latest" tag
```

​	{{ end }}

{{ end }}

{{ if Tiered == "Yes"  }}

### AuthServer

This is the authentication server which should be individually hosted compared to non-tiered application templates. The `dockerfile.local` is located under the `AuthServer` project as below;

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
COPY bin/Release/net7.0/publish/ app/
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
RUN dotnet dev-certs https -v -ep authserver.pfx -p 2D7AA457-5D33-48D6-936F-C48E5EF468ED

FROM base AS final
WORKDIR /app
COPY --from=build /src .

ENTRYPOINT ["dotnet", "Acme.BookStore.AuthServer.dll"]
```

The AuthServer docker image building process contains multi-stages to generate `authserver.pfx` file which is **used by OpenIddict as signing and encryption certificate**. This configuration is found under the `PreConfigureServices` method of the **AuthServerModule**:

```csharp
if (!hostingEnvironment.IsDevelopment())
{
    PreConfigure<AbpOpenIddictAspNetCoreOptions>(options =>
    {
        options.AddDevelopmentEncryptionAndSigningCertificate = false;
    });

    PreConfigure<OpenIddictServerBuilder>(builder =>
    {
        builder.AddSigningCertificate(GetSigningCertificate(hostingEnvironment, configuration));
        builder.AddEncryptionCertificate(GetSigningCertificate(hostingEnvironment, configuration));
        builder.SetIssuer(new Uri(configuration["AuthServer:Authority"]));
    });
}
```

This configuration disables the *DevelopmentEncryptionAndSigningCertificate* and uses a self-signed certificate called `authserver.pfx`. for **signing and encrypting the tokens**. This certificate is being created when the docker image is build using the `dotnet dev-certs` tooling . It is a sample generated certificate and it is **recommended** to update it for production environment. You can check the [OpenIddict Encryption and signing credentials documentation](https://documentation.openiddict.com/configuration/encryption-and-signing-credentials.html) for different options and customization.

The `GetSigningCertificate` method is a private method located under the same **AuthServerModule**:

```csharp
private X509Certificate2 GetSigningCertificate(IWebHostEnvironment hostingEnv, IConfiguration configuration)
{
    var fileName = "authserver.pfx";
    var passPhrase = "2D7AA457-5D33-48D6-936F-C48E5EF468ED";
    var file = Path.Combine(hostingEnv.ContentRootPath, fileName);

    if (!File.Exists(file))
    {
        throw new FileNotFoundException($"Signing Certificate couldn't found: {file}");
    }

    return new X509Certificate2(file, passPhrase);
}
```

> You can always create any self-signed certificate using any other tooling outside of the dockerfile. You need to keep on mind to set them as **embedded resource** since the `GetSigningCertificate` method will be checking this file physically. 

If you don't want to use the `build-images-locally.ps1` to build the images or to build this image individually and manually, navigate to **AuthServer** folder and run:

```powershell
dotnet publish -c Release #Builds the projects in Release mode
docker build -f Dockerfile.local -t acme/bookstore-authserver:latest . #Builds the image with "latest" tag
```

### Http.Api.Host

This is the backend application that exposes the endpoints and swagger UI. It is not a multi-stage dockerfile hence you need to have already built this application in **Release mode** in order to use this dockerfile. The `dockerfile.local` is located under the `Http.Api.Host` project as below;

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:7.0
COPY bin/Release/net7.0/publish/ app/
WORKDIR /app
ENTRYPOINT ["dotnet", "Acme.BookStore.HttpApi.Host.dll"]
```

If you don't want to use the `build-images-locally.ps1` to build the images or to build this image individually and manually, navigate to **Http.Api.Host** folder and run:

```powershell
dotnet publish -c Release #Builds the projects in Release mode
docker build -f Dockerfile.local -t acme/bookstore-api:latest . #Builds the image with "latest" tag
```

{{ end }} //End of tiered

## Running Docker-Compose on Localhost

Under the `etc/docker` folder, you can find the `docker-compose.yml` to run your application. To ease the running process, the template provides `run-docker.ps1` (and `run-docker.sh`) scripts that handles the HTTPS certificate creation which is used in environment variables;

```powershell
$currentFolder = $PSScriptRoot

$slnFolder = Join-Path $currentFolder "../"
$certsFolder = Join-Path $currentFolder "certs"

If(!(Test-Path -Path $certsFolder))
{
    New-Item -ItemType Directory -Force -Path $certsFolder
    if(!(Test-Path -Path (Join-Path $certsFolder "localhost.pfx") -PathType Leaf)){
        Set-Location $certsFolder
        dotnet dev-certs https -v -ep localhost.pfx -p 91f91912-5ab0-49df-8166-23377efaf3cc -t        
    }
}

Set-Location $currentFolder
docker-compose up -d
```

`run-docker.ps1` (or `run-docker.sh`) script will be checking if there is an existing dev-cert already under the `etc/certs` folder and generates a `localhost.pfx` file if doesn't exist. **This file will be used by Kestrel as HTTPS certificate**.

Now lets break down each docker compose service under the `docker-compose.yml` file:

{{ if UI == "NG" }}

**bookstore-angular:**

```yaml
services:
  bookstore-angular:
    image: acme/bookstore-angular:latest
    container_name: bookstore-angular
    build:
      context: ../../../
      dockerfile: angular/Dockerfile.local
    ports:
      - "4200:80"
    depends_on:
      - bookstore-api
    volumes:
      - ./dynamic-env.json://usr/share/nginx/html/dynamic-env.json
    networks:
      - abp-network
```

This is the angular application we deploy on http://localhost:4200 by default using the image we have built using the `build-images-locally.ps1` script. **It is not running on HTTPS** using the `localhost.pfx` since it is running on **Nginx** and it doesn't accept `pfx` files for SSL. You can check [Nginx Configuring HTTPS Servers documentation](http://nginx.org/en/docs/http/configuring_https_servers.html) for more information and apply necessary configurations it to `nginx.conf` file under the `angular` folder. 

> Don't forget to rebuild the `acme/bookstore-angular:latest` image after updating the `nginx.conf` file.

{{ end }}