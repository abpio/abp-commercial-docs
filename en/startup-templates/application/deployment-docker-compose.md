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

You can manually build this image by navigating to DbMigrator folder and run `docker build -f Dockerfile.local -t mycompanyname/bookstore-db-migrator:latest .`

## Web



## Running Docker-Compose on Localhost

