# How to deploy to Azure Kubernetes (AKS) a SpringBoot WebAPI

See this repo for creating the Docker image and Kubernetes manifest files: 

https://github.com/luiscoco/SpringBoot_Sample4-Deploy-WebAPI-to-LocalKubernetes

## 1.



## 2. 



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
az aks create --resource-group myRG --name myAKSClusterluiscoco1974 --node-count 1 --enable-addons monitoring --generate-ssh-keys --attach-acr myregistryluiscoco1974 --location westeurope
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
