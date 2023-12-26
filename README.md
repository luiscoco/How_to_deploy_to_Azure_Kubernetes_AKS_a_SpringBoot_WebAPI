# How to deploy SpringBoot WebAPI to Azure Kubernetes(AKS)

## 1. Create the SpringBoot WebAPI with VSCode




## 2. Create the Dockerfile




## 3. Create the Kubernetes manifest files (deployment.yml and service.yml)




## 4. Create Service-Principal

az ad sp create-for-rbac --name luis-service-principal-name --scopes /subscriptions/1d640b19-d6ae-466b-a0bd-d80869b565b8/resourceGroups/myRG/providers/Microsoft.ContainerRegistry/registries/myregistryluiscoco1974 --role acrpull --query "password" --output tsv

SecretValue: bKB8Q~Zs2bho7SUQy1G4Qhhq7cOf7Sdgti-nwckf

## 5. Get the ApplicationID

az ad sp list --display-name luis-service-principal-name --query "[].appId" --output tsv

ApplicationID: 70566a6a-4ba8-4b63-9f6e-3979467545c6

## 6. Login in Azure Container Registry ACR with this command

docker login myregistryluiscoco1974.azurecr.io -u ApplicationID -p SecretValue



## 7. Push the Docker image to Azure ACR




## 8. Deploy the docker image stored in ACR to Azure AKS









**IMPORTANT NOTE**: see this repo for creating the Docker image (**DockerFile**) and Kubernetes manifest files (**deployment.yml** and **service.yml**): 

https://github.com/luiscoco/SpringBoot_Sample4-Deploy-WebAPI-to-LocalKubernetes

## 1. Prerequisites

Install **kubectl** command in Windows: https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/

Download and Install **Docker Desktop**: https://docs.docker.com/desktop/install/windows-install/

Install **Azure CLI**: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows

## 2. Create Azure Container Registry (ACR)

### 2.1. Login in to Azure:

```
az login
```

### 2.2. Create a ResourceGroup:

```
az group create --name myRG --location westeurope
```

### 2.3. Create an ACR instance (**Note**: only use **lowercase letters** for the ACR name):

```
az acr create --resource-group myRG --name myregistryluiscoco1974 --sku Basic --location westeurope
```

### 2.4. Set the **Admin user** in the ACR and copy the username and password:



## 3. Build and Push Docker image

### 3.1. Navigate to your project

```
cd path/to/your/project
```

### 3.2. Log in to ACR:

```
az acr login --name myregistryluiscoco1974
```

![image](https://github.com/luiscoco/Azure_AKS_Deploy_.NET_7_Web_API/assets/32194879/9cf9cb0d-2d98-40d4-be6c-17aed5b4e0db)

**NOTE**: if you cannot enter with this command run again "az login" and try again running the command "az acr login --name myregistryluiscoco1974" 

### 3.3. Build your Docker image:

```
docker build -t myregistryluiscoco1974.azurecr.io/mywebapi:v1 .
```

![image](https://github.com/luiscoco/Azure_AKS_Deploy_.NET_7_Web_API/assets/32194879/fc101461-c21f-4e26-8ff4-94f71b9a36f4)

### 3.4. Push the Image to ACR:

```
docker push myregistryluiscoco1974.azurecr.io/mywebapi:v1
```

![image](https://github.com/luiscoco/Azure_AKS_Deploy_.NET_7_Web_API/assets/32194879/2255b4e1-47ba-4666-91d9-40c32ffb2348)

## 4. Create Azure Kubernetes AKS Cluster

```
az aks create --resource-group myRG ^
--name myAKSClusterluiscoco1974 ^
--node-count 1 ^
--enable-addons monitoring ^
--generate-ssh-keys ^
--attach-acr myregistryluiscoco1974 ^
--location westeurope
```

## 5. Connect to Azure Kubernetes AKS Cluster

```
az aks get-credentials --resource-group myRG --name myAKSClusterluiscoco1974
```

## 6. Deploy your app to AKS using a Kubernetes manifest files

The **deployment.yaml** and **serivce.yml** files define how your app should run and what image to use. 

Assuming you have a manifest file (e.g., deployment.yaml), use the following command:

```
kubectl apply -f deployment.yml
```

and 

```
kubectl apply -f service.yml
```

Verify the Deployment. To ensure your deployment is running, use:

```
kubectl get deployments
```

For detailed information on the deployed pods, use:

```
kubectl get pods
```

To see the LoadBalancer IP address run this command:

```
kubectl get services
```
