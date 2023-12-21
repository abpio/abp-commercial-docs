# Microservice Solution: Database Migrations

This document explains the database migration system that is designed and implemented for the ABP Studio Microservice solution template.

## Introduction

When you use a relational database, the database schema (tables, fields, views, etc) should be explicitly created before using it. In addition, whenever you deploy a new version of your service, the database schema should be updated if you made a change on it.

For example, if you have added a new field to a database table, you should also add that field to the production database before (or while) deploying your service to the production environment.

Managing the schema changes manually is not a good practice and error-prone in a highly dynamic system like a microservice solution. The Microservice solution template uses [Entity Framework migrations](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/) to maintain the database schema and automatically migrate it whenever you deploy a new version of your service/application.

In addition to the schema changes, you may also need to insert some initial (seed) data to some tables in order to make your server properly works. That process is called as [data seeding](https://docs.abp.io/en/abp/latest/Data-Seeding). The Microservice solution is also configured so it can seed such initial data on the application startup.

> If you are using **MongoDB** as your database provider, the schema migration is not needed (But you should care about some kind of data and schema migrations in case of you made a breaking change on your database schema - this is something depends on your application, so you should understand how to work with a document database like MongoDB). However, the data seeding system is still used to insert initial data to the database.

