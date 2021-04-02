# How to Run Microservice Template on Tye

> This documentation introduces guidance for running your microservice template on [dotnet/tye](https://github.com/dotnet/tye). We suggest using tye for your development environment. You can check [tye getting started page](https://github.com/dotnet/tye/blob/main/docs/getting_started.md) for installation. 

## Pre-Requirements

- [Docker Desktop](https://www.docker.com/products/docker-desktop) v3.0+

- #### **Developer Certificates** 
  - **Windows Users:** Run the powershell script file `create-certificate.ps1`. This file will create a self-signed certificate named `localhost.pfx` with a predefined password using **dotnet dev-certs** command. 
  - **Linux Users:** dotnet dev-certs may not be fully working on Linux so you may need to generate and trust your own certificate and put `localhost.pfx` file under **etc/dev-cert** folder inside solution directory.

    **Generate the certificate:** Run the commands below to create necessary *localhost.pfx* file for https under *etc/dev-cert* folder,

    ```bash
    # See https://stackoverflow.com/questions/55485511/how-to-run-dotnet-dev-certs-https-trust
    # for more details
  
    cat << EOF > localhost.conf
    [req]
    default_bits       = 2048
    default_keyfile    = localhost.key
    distinguished_name = req_distinguished_name
    req_extensions     = req_ext
    x509_extensions    = v3_ca
    
    [req_distinguished_name]
    commonName                  = Common Name (e.g. server FQDN or YOUR name)
    commonName_default          = localhost
    commonName_max              = 64
    
    [req_ext]
    subjectAltName = @alt_names
    
    [v3_ca]
    subjectAltName = @alt_names
    basicConstraints = critical, CA:false
    keyUsage = keyCertSign, cRLSign, digitalSignature,keyEncipherment
    
    [alt_names]
    DNS.1   = localhost
    DNS.2   = 127.0.0.1
    
    EOF
    
    # Generate certificate from config
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout localhost.key -out localhost.crt \
      -config localhost.conf
    
    # Export pfx
    openssl pkcs12 -export -out localhost.pfx -inkey localhost.key -in localhost.crt -password pass:e8202f07-66e5-4619-be07-72ba76fde97f
    
    # Import CA as trusted
    sudo cp localhost.crt /usr/local/share/ca-certificates/
    sudo update-ca-certificates 
    
    # Validate the certificate
    openssl verify localhost.crt
    ```
    
  - **Mac Users:** You can either download **powershell** and use the `create-certificate.ps1` file to let dotnet dev-certs create the certificate or create manually using **openssl** as above.
  
- #### **Database Configuration (For Linux /Mac users)**

  > If you are a windows user and have sql-server express installed already, you can skip this part. 

  If you are a Mac/Linux user or if you want to run the database in container; use the following command to run the mssql-server-linux on container:

  ```sh
  docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=myPassw0rd!' -p 1434:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2019-latest
  ```
  

To update the connection strings, you can either **update appsettings.json** files in each project with database connections **or** add environment variables to override the **appsettings**. 

- **Override the appsettings:** In tye.yaml file, add env variable to each service using connection strings. Your tye.yaml file should be updated as below:
  
    ```yaml
    - name: auth-server
      project: apps/auth-server/src/MyCompanyName.MyProjectName.AuthServer/MyCompanyName.MyProjectName.AuthServer.csproj
      bindings:
        - protocol: https
          port: 44322
    env:
        - Kestrel__Certificates__Default__Path=../../../../etc/dev-cert/localhost.pfx
      - Kestrel__Certificates__Default__Password=e8202f07-66e5-4619-be07-72ba76fde97f
        - ConnectionStrings__IdentityService=Server=localhost,1434;Database=MyProjectName_Identity;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
      - ConnectionStrings__AdministrationService=Server=localhost,1434;Database=MyProjectName_Administration;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        - ConnectionStrings__SaasService=Server=localhost,1434;Database=MyProjectName_Saas;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
    
    - name: administration-service
    project: services/administration/src/MyCompanyName.MyProjectName.AdministrationService.HttpApi.Host/MyCompanyName.MyProjectName.AdministrationService.HttpApi.Host.csproj
      bindings:
        - protocol: https
          port: 44367
      env:
        - Kestrel__Certificates__Default__Path=../../../../etc/dev-cert/localhost.pfx
        - Kestrel__Certificates__Default__Password=e8202f07-66e5-4619-be07-72ba76fde97f
        - ConnectionStrings__AdministrationService=Server=localhost,1434;Database=MyProjectName_Administration;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        - ConnectionStrings__SaasService=Server=localhost,1434;Database=MyProjectName_Saas;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        
    - name: identity-service
      project: services/identity/src/MyCompanyName.MyProjectName.IdentityService.HttpApi.Host/MyCompanyName.MyProjectName.IdentityService.HttpApi.Host.csproj
      bindings:
        - protocol: https
          port: 44388
      env:
        - Kestrel__Certificates__Default__Path=../../../../etc/dev-cert/localhost.pfx
        - Kestrel__Certificates__Default__Password=e8202f07-66e5-4619-be07-72ba76fde97f
        - ConnectionStrings__IdentityService=Server=localhost,1434;Database=MyProjectName_Identity;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        - ConnectionStrings__AdministrationService=Server=localhost,1434;Database=MyProjectName_Administration;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        - ConnectionStrings__SaasService=Server=localhost,1434;Database=MyProjectName_Saas;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        
    - name: saas-service
      project: services/saas/src/MyCompanyName.MyProjectName.SaasService.HttpApi.Host/MyCompanyName.MyProjectName.SaasService.HttpApi.Host.csproj
      bindings:
        - protocol: https
          port: 44381
      env:
        - Kestrel__Certificates__Default__Path=../../../../etc/dev-cert/localhost.pfx
        - Kestrel__Certificates__Default__Password=e8202f07-66e5-4619-be07-72ba76fde97f
        - ConnectionStrings__AdministrationService=Server=localhost,1434;Database=MyProjectName_Administration;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        - ConnectionStrings__SaasService=Server=localhost,1434;Database=MyProjectName_Saas;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true    
    
    - name: product-service
      project: services/product/src/MyCompanyName.MyProjectName.ProductService.HttpApi.Host/MyCompanyName.MyProjectName.ProductService.HttpApi.Host.csproj
      bindings:
        - protocol: https
          port: 44361
      env:
        - Kestrel__Certificates__Default__Path=../../../../etc/dev-cert/localhost.pfx
        - Kestrel__Certificates__Default__Password=e8202f07-66e5-4619-be07-72ba76fde97f
        - ConnectionStrings__ProductService=Server=localhost,1434;Database=MyProjectName_ProductService;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true    
        - ConnectionStrings__AdministrationService=Server=localhost,1434;Database=MyProjectName_Administration;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
        - ConnectionStrings__SaasService=Server=localhost,1434;Database=MyProjectName_Saas;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true
    ```
  
    > You can compare your tye configuration with the [template tye configuration](https://gist.github.com/gterdem/b11f08df0557196a1635292b4d778d2f).
  
  - **Update appsettings.json (Skip this part if you have already overridden appsettings):** Update all projects using connection strings appsettings.json file with new connection strings since Trusted_Connection will not work and authentication to database must be done with sql server authentication. Sample connection strings in appsettings for auth-server is shown below:
  
    ```json
    "ConnectionStrings": {
      "IdentityService": "Server=localhost,1434;Database=MyProjectName_Identity;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true",
      "AdministrationService": "Server=localhost,1434;Database=MyProjectName_Administration;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true",
      "SaasService": "Server=localhost,1434;Database=MyProjectName_Saas;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true"
    },
    ```

> Databases will be asynchronously created and seeded when services run. Verify your databases after running tye to check if your databases are created and the admin user is created under Identity database AbpUsers table. Sometimes insufficient vm memory may cause temporary failures. 

## Running tye

> For all the run options, you can simply use `tye run -h` command.

Use the command `tye run` under your main solution directory to run the solution.

![tye-run](../../images/tye-run.png)

After tye is initialized, you can navigate to http://127.0.0.1:8000 (or http://localhost:8000) for tye dashboard.

<img src="../../images/tye-dashboard.png" alt="tye-dashboard"  />

#### Debug

You can debug any service by *attaching to a process*. To run a service on debug mode simple use the command with the service name you want to debug: 

```bash
tye run --debug my-service
```

You will see an awaiting notice for attaching to a process when you *View* the *Logs* of the service you debug:

![tye-debugger-attach](../../images/tye-debugger-attach.png)

> In this state, product service will not start until you attach a debugger.

In Visual Studio go to menu **Debug -> Attach to Process** then select the service process you are debugging.

![tye-vs-attach-debugger](../../images/tye-vs-attach-debugger.png)

You can debug your application normally.![image-20210402141429187](../../images/tye-debugging-vs.png)

If you want to attach a debugger to all of your services, you can use `*` instead of service name like:

```bash
tye run --debug *
```

#### Watch

To improve the development pace, you can also enable the file system watchers. This way, only the modified and effected services will be restarted. To enable file watcher simply add *--watch*  switch to run command:

```bash
tye run --watch
```

Stop:

To stop tye, simply use the command `Ctrl+C` on the console tye is running. Keep in mind that, **close the tye dashboard** before stopping tye since tye fails to stop container processes successfully.