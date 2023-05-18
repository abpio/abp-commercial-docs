# Helm Deployment on Local Kubernetes Cluster

> This documentation introduces guidance for running your microservice template on local kubernetes cluster using helm charts. The ABP microservice template already provides scripts to deploy your solution into your local kubernetes cluster. It is **required** to have basic knowledge about [kubernetes](https://kubernetes.io/) and [helm charts](https://helm.sh/). 

##  Pre-Requirements

- [Docker for Desktop](https://www.docker.com/products/docker-desktop/) with Kubernetes enabled or [Minikube](https://minikube.sigs.k8s.io/docs/start/).
- [Helm](https://helm.sh/docs/intro/install/) for running helm charts.

- [NGINX ingress](https://kubernetes.github.io/ingress-nginx/deploy/) for Kubernetes

    OR

    NGINX ingress using helm

  ```powershell
  helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
  
  helm repo update
  
  helm upgrade --install --version=4.0.19 ingress-nginx ingress-nginx/ingress-nginx
  ```


## Building Images

The ABP microservice template provides docker image build scripts based on your solution. There are two scripts located under the `build` folder:

- **build-images-locally.ps1** script is used to build docker images using the `Dockerfile.local` file located under all the applications, microservices and gateways. This is a very basic image build that copies the files under the `bin/Release` folder to the image. `build-images-locally.ps1` script basically navigates all the related applications/microservices and runs `dotnet publish` on release before creating the docker image. **You need .NET SDK to build these images.**
- **build-images.ps1** script is used to build docker images using the `Dockerfile` file located under all the applications, microservices and gateways. This dockerfile uses multi-stage image building that restores, publishes and copies the applications in the containers. Since all the microservice solution is cached, first image creation can be slow but the others would be created faster. **This approach is more suitable for CI&CD environments** when you don't want to add extra step to install .NET SDK.

## Removing Unused Helm Charts

Necessary helm charts are created under `etc/k8s/mystore` folder. Based on your back-office application UI, **remove** the other UI charts. Ex, if you are using ***\*angular\**** UI, remove ***\*web\****, ***\*blazor\**** and ***\*blazor-server\**** charts.

## How to Run

The default application is configured to be running on HTTPS using domain names. This guide will be using **MyStore** as project name. The generated deployment scripts and namespaces will be **based on your project name**.

### Configuring HTTPS

There are various ways to create a self-signed certificate. dotnet tooling with the command `dotnet dev-certs https` for generating self-signed certificate **will not work** since it only generates certificate for the **localhost** domain. The k8s environment will be requiring real domain names. 

#### Using mkcert

This guide will be using `mkcert` for creating self-signed certificates. Follow the [installation guide](https://github.com/FiloSottile/mkcert#installation) to install mkcert. Use the command to create root (local) certificate authority for your certificates:

```powershell
mkcert -install
```

This command will make all the certificates created by **mkcert** trusted by the local machine.

> You can also use [OpenSSL](https://www.openssl.org/) to generate self-signed certificate for multiple domains. 

### Generating TLS Secret

Use the `create-tls-secrets.ps1` script under the `etc/k8s` folder to generate self-signed certificate and create the tls secrets that will be used by ingress. The generated script will be based on your project name.

```powershell
mkcert `
"mystore-st-authserver" `
"mystore-st-angular" `
"mystore-st-public-web" `
"mystore-st-gateway-web" "mystore-st-gateway-web-public" `
"mystore-st-identity" "mystore-st-administration" "mystore-st-saas" "mystore-st-product" 
```

This command generates `mystore-st-authserver+8.pem` and `mystore-st-authserver+8-key.pem` files under the `etc/k8s` folder.

```powershell
kubectl create namespace mystore
kubectl create secret tls -n mystore mystore-tls --cert=./mystore-st-authserver+8.pem  --key=./mystore-st-authserver+8-key.pem
```

These commands will create a namespace of your project name and the tls secret using the generated files.

> **Note:** If you add a new microservice or an other application, don't forget to generate SSL certificate for it and update the TLS secret.

### Mapping Host Name

Now we need to **map the domain names** we have generated the SSL certificate for. Add entries to the hosts file (in Windows: `C:\Windows\System32\drivers\etc\hosts`, in linux and macos: `/etc/hosts` ):

```
127.0.0.1 mystore-st-angular
127.0.0.1 mystore-st-public-web
127.0.0.1 mystore-st-authserver
127.0.0.1 mystore-st-identity
127.0.0.1 mystore-st-administration
127.0.0.1 mystore-st-product
127.0.0.1 mystore-st-saas
127.0.0.1 mystore-st-gateway-web
127.0.0.1 mystore-st-gateway-web-public
```

This configuration will allow k8s ingress to take over when you navigate to any of these domain in the browser.

### Running the Solution

Use `deploy-staging.ps1` script to deploy your microservice solution as a single helm chart deployment. The script contains:
```powershell
helm upgrade --install mystore-st mystore --namespace mystore --create-namespace
```

the command to install (or upgrade) the helm deployment using mystore-st **release name** to the mystore **namespace** (and creates the namespace if it doesn't exist). The generated script will be based on your project name.

## Helm Charts Explained

Under `etc/k8s` folder you can see the **MyStore** (your application name) folder that contains the main helm chart and sub-charts. 

### Main Chart

The `deploy-staging.ps1` script uses the MyStore `Chart.yaml` and the `values.yaml` files for single helm deployment for the whole microservice solution as a single chart. **MyStore** folder contains the **values.yaml** file that is used to **override all the sub-charts**.

### Sub-Charts

Under the **charts** folder, you can see all the 3rd party applications (like redis, sql-server etc) and the solution project charts (applications, microservices, gateways). They are all helm charts and contain their own `Chart.yaml` and `values.yaml` files. Under the **templates** folder, you can find **ingress**, **service**, **deployment** and **configmap** (gateways and angular app have it) files.

- **Ingress** file is used for exposing services/applications to browser with a DNS. It is using **nginx** as `ingress.class` and contains host and tls configurations. 
- **Service** file the service definition for the helm chart.
- **Deployment** file is contains all the deployment configuration and the most importantly **environment variable overriding**. You can examine and/or add your own appsettings overriding to the this file. You can see all the overriding values are pointing to the `values.yaml` file.
- **Configmap** file contains json files to replace the existing files. Gateways replace `ocelot.json` files, **angular application** contains `dynamic-env.json` file to replace the **remote environment** file. You can find volume mounts under the `deployment.yaml` files. This is used to prevent the recreation of the image for environment changes. 

Each template deployment override information are located under their own chart folder. The `values.yaml` files contains the keys but the **values are commented out**. This is because **the main chart values.yaml file overrides all these values in a single file.** If you want to deploy any application, microservice or a gateway **individually**, you need to **fill the commented values** of the related `values.yaml` files.

## FAQ

- **Can't reach this page!** I get `DNS_PROBE_FINISHED_NXDOMAIN` error when I try to navigate any of the applications (like `https://mystore-st-public-web`).
  - It means your local kubernetes ingress can not resolve the DNS. 
    1) Check your docker for desktop (or your preferred k8s cluster) is up and running and make sure the [NGINX ingress](https://kubernetes.github.io/ingress-nginx/deploy/) for Kubernetes is **up and running** properly.
    2) Check your host name mapping under the `etc\hosts` folder. **Make sure you have proper mapping** for the domain names and your local kubernetes cluster has also correct mapping (like `127.0.0.1 kubernetes.docker.internal`), added by docker for desktop (or your preferred k8s cluster).
- **Your connection isn't private!** I get `NET::ERR_CERT_AUTHORITY_INVALID` error when I try to navigate any of the applications (like `https://mystore-st-public-web`).
  - It means there is a problem with the **SSL certificate**. Make sure you have generated the certificates properly and created the TLS secret correctly.

## Next

- [Guides: Adding New Microservice](add-microservice.md)
