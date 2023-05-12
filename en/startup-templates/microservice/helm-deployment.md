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
- **build-imaegs.ps1** script is used to build docker images using the `Dockerfile` file located under all the applications, microservices and gateways. This dockerfile uses multi-stage image building that restores, publishes and copies the applications in the containers. Since all the microservice solution is cached, first image creation can be slow but the others would be created faster. **This approach is more suitable for CI&CD environments** when you don't want to add extra step to install .NET SDK.

## Removing Unused Helm Charts



## How to Run



### Configuring HTTPS



### Installing mkcert



### Generating TLS Secret



### Mapping Host Name



## Helm Charts Explained





## Next

- [Guides: Adding New Microservice](add-microservice.md)
