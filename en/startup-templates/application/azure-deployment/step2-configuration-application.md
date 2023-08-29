````json
//[doc-params]
{
    "UI": ["MVC", "Blazor", "BlazorServer", "NG"],
    "DB": ["EF", "Mongo"],
    "Tiered": ["Yes", "No"]
}
````

## Step 2: Customizing the Configuration of Your ABP Application

To customize the configuration of your ABP application, modify the `ConnectionStrings` in every location throughout your project. 

This includes the following files:

{{ if UI == "MVC" && Tiered == "No" }}

**./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.Web/appsettings.json**

{{else}}

**./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

{{if Tiered == "Yes"}}

**./src/yourapp.AuthServer/appsettings.json**

{{end}}
{{end}}

```json
"ConnectionStrings": {
    "Default": "Server=tcp:yourserver.database.windows.net,1433;Initial Catalog=yourdatabase;Persist Security Info=False;User ID=yourusername;Password=yourpassword;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
}
```

{{ if UI == "MVC" }}

    {{if Tiered == "No"}}

- Modify the **yourapp.Web** url in every location throughout your project, especially within the **./src/yourapp.Web/appsettings.json** and **./src/yourapp.DbMigrator/appsettings.json** files, to match your Azure Web App Service url.

```json
    "App": {
        "SelfUrl": "https://yourapp.azurewebsites.net"
    }
```

    {{else}}

- Modify the **yourapp.Web** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Web/appsettings.json** , **./src/yourapp.DbMigrator/appsettings.json** , **./src/yourapp.HttpApi.Host/appsettings.json** and **./src/yourapp.AuthServer/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp.azurewebsites.net"
}
```

- Modify the **yourapp.ApiHost** url in every location throughout your project.

This includes the following files:

**./src/yourapp.HttpApi.Host/appsettings.json** , **./src/yourapp.Web/appsettings.json** , **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.AuthServer/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp-apihost.azurewebsites.net"
}
```

- Modify the **yourapp.AuthServer** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Web/appsettings.json** , **./src/yourapp.AuthServer/appsettings.json** ,  **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp-authserver.azurewebsites.net"
}
```

- Modify the **Redis__Configuration** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Web/appsettings.json** , **./src/yourapp.AuthServer/appsettings.json** ,  **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

```json
"Redis": {
    "Configuration": "redis-abpdemo.redis.cache.windows.net:6380,password={yourpassword},ssl=true,abortConnect=False"
  },
```

    {{end}}

{{ elseif UI == "NG" }}

- Modify the **`localhost:4200`** in every location throughout your project.

This includes the following files:

**./angular/src/environments/environment.prod.ts** , **./aspnet-core/src/yourapp.DbMigrator/appsettings.json** and **./aspnet-core/src/yourapp.HttpApi.Host/appsettings.json**

```typescript
    application: {
        baseUrl: 'https://yourapp.azurestaticapps.net'
    }
```

- Modify the **yourapp.HttpApi.Host** url in every location throughout your project.

This includes the following files:

**./angular/src/environments/environment.prod.ts** , **./aspnet-core/src/yourapp.DbMigrator/appsettings.json** and **./aspnet-core/src/yourapp.HttpApi.Host/appsettings.json**

```json
    "App": {
        "SelfUrl": "https://yourApiHost.azurewebsites.net"
    }
```

{{ elseif UI == "Blazor" }}

- Modify the **yourapp.Blazor** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Blazor/appsettings.json** , **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

```json
    "App": {
        "SelfUrl": "https://yourapp.azurewebsites.net"
    }
```

- Modify the **yourapp.HttpApi.Host** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Blazor/appsettings.json** , **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

```json
    "App": {
        "SelfUrl": "https://yourApiHost.azurewebsites.net"
    }
```

{{ else }}

    {{if Tiered == "No"}}

- Modify the **yourapp.Web** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Blazor/appsettings.json** , **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp.azurewebsites.net"
}
```

- Modify the **yourapp.ApiHost** url in every location throughout your project.

This includes the following files:

**./src/yourapp.HttpApi.Host/appsettings.json** , **./src/yourapp.Blazor/appsettings.json** and **./src/yourapp.DbMigrator/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp-apihost.azurewebsites.net"
}
```

    {{else}}

- Modify the **yourapp.Web** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Blazor/appsettings.json** , **./src/yourapp.DbMigrator/appsettings.json** , **./src/yourapp.HttpApi.Host/appsettings.json** and **./src/yourapp.AuthServer/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp.azurewebsites.net"
}
```

- Modify the **yourapp.ApiHost** url in every location throughout your project.

This includes the following files:

**./src/yourapp.HttpApi.Host/appsettings.json** , **./src/yourapp.Blazor/appsettings.json** , **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.AuthServer/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp-apihost.azurewebsites.net"
}
```

- Modify the **yourapp.AuthServer** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Blazor/appsettings.json** , **./src/yourapp.AuthServer/appsettings.json** ,  **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

```json
"App": {
    "SelfUrl": "https://yourapp-authserver.azurewebsites.net"
}
```

- Modify the **Redis__Configuration** url in every location throughout your project.

This includes the following files:

**./src/yourapp.Blazor/appsettings.json** , **./src/yourapp.AuthServer/appsettings.json** ,  **./src/yourapp.DbMigrator/appsettings.json** and **./src/yourapp.HttpApi.Host/appsettings.json**

```json
"Redis": {
    "Configuration": "redis-abpdemo.redis.cache.windows.net:6380,password={yourpassword},ssl=true,abortConnect=False"
    },
```

    {{end}}

{{end}}


- Modify the **GetSigningCertificate** method in your project. This method should be located in the **\*Module.cs** file.

```csharp
private X509Certificate2 GetSigningCertificate(IWebHostEnvironment hostingEnv, IConfiguration configuration)
{
    var fileName = $"cert-signing.pfx";
    var passPhrase = configuration["MyAppCertificate:X590:PassPhrase"]; 
    var file = Path.Combine(hostingEnv.ContentRootPath, fileName);        
    if (File.Exists(file))
    {
        var created = File.GetCreationTime(file);
        var days = (DateTime.Now - created).TotalDays;
        if (days > 180)          
            File.Delete(file);
        else
            return new X509Certificate2(file, passPhrase,
                        X509KeyStorageFlags.MachineKeySet);
    }
    // file doesn't exist or was deleted because it expired
    using var algorithm = RSA.Create(keySizeInBits: 2048);
    var subject = new X500DistinguishedName("CN=Fabrikam Signing Certificate");
    var request = new CertificateRequest(subject, algorithm, 
                        HashAlgorithmName.SHA256, RSASignaturePadding.Pkcs1);
    request.CertificateExtensions.Add(new X509KeyUsageExtension(
                        X509KeyUsageFlags.DigitalSignature, critical: true));
    var certificate = request.CreateSelfSigned(DateTimeOffset.UtcNow, 
                        DateTimeOffset.UtcNow.AddYears(2));
    File.WriteAllBytes(file, certificate.Export(X509ContentType.Pfx, string.Empty));
    return new X509Certificate2(file, passPhrase, 
                        X509KeyStorageFlags.MachineKeySet);
}
private X509Certificate2 GetEncryptionCertificate(IWebHostEnvironment hostingEnv,
                            IConfiguration configuration)
{
    var fileName = $"cert-encryption.pfx";
    var passPhrase = configuration["MyAppCertificate:X590:PassPhrase"]; 
    var file = Path.Combine(hostingEnv.ContentRootPath, fileName);
    if (File.Exists(file))
    {
        var created = File.GetCreationTime(file);
        var days = (DateTime.Now - created).TotalDays;
        if (days > 180)
            File.Delete(file);
        else
            return new X509Certificate2(file, passPhrase, 
                            X509KeyStorageFlags.MachineKeySet);
    }
    // file doesn't exist or was deleted because it expired
    using var algorithm = RSA.Create(keySizeInBits: 2048);
    var subject = new X500DistinguishedName("CN=Fabrikam Encryption Certificate");
    var request = new CertificateRequest(subject, algorithm, 
                        HashAlgorithmName.SHA256, RSASignaturePadding.Pkcs1);
    request.CertificateExtensions.Add(new X509KeyUsageExtension(
                        X509KeyUsageFlags.KeyEncipherment, critical: true));
    var certificate = request.CreateSelfSigned(DateTimeOffset.UtcNow,
                        DateTimeOffset.UtcNow.AddYears(2));
    File.WriteAllBytes(file, certificate.Export(X509ContentType.Pfx, string.Empty));
    return new X509Certificate2(file, passPhrase, X509KeyStorageFlags.MachineKeySet);
}
```

- In the same file, modify the **PreConfigureServices** method by ensuring the two methods above are called when your application is not running in a production environment

```csharp
    if (!hostingEnvironment.IsDevelopment())
    {
        PreConfigure<AbpOpenIddictAspNetCoreOptions>(options =>
        {
            options.AddDevelopmentEncryptionAndSigningCertificate = false;
        });
        PreConfigure<OpenIddictServerBuilder>(builder =>
        {
            // In production, it is recommended to use two RSA certificates, 
            // one for encryption, one for signing.
            builder.AddEncryptionCertificate(
                    GetEncryptionCertificate(hostingEnvironment, context.Services.GetConfiguration()));
            builder.AddSigningCertificate(
                    GetSigningCertificate(hostingEnvironment, context.Services.GetConfiguration()));
        });
    }
```

- In the same file, add **```using System.Security.Cryptography;```** to the top of the file.

- In the same file, comment **```ConfigureHealthChecks(context);```** in the **ConfigureServices** method.

    ![ConfigureHealthChecks](../../../images/azure-deploy-configure-health-checks.png)

- Add a custom passphrase to your **appsettings.json** or Azure configuration:

```json
"MyAppCertificate": {
"X590": "[custom string]"
}
```