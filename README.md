# How to deploy SpringBoot WebAPI to Azure Kubernetes(AKS)

## 1. Create the SpringBoot WebAPI with VSCode

https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode

## 2. Create the Dockerfile

```
# Start with a base image containing Java runtime
FROM openjdk:11-jdk-slim as build

# Add Maintainer Info
LABEL maintainer="your_email@example.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make port 8080 available to the world outside this container
EXPOSE 8080

# The application's jar file
ARG JAR_FILE=target/demoapi-0.0.1-SNAPSHOT.jar

# Add the application's jar to the container
ADD ${JAR_FILE} demoapi.jar

# Run the jar file
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/demoapi.jar"]
```

## 3. Create the Kubernetes manifest files (deployment.yml and service.yml)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demoapi-deployment
spec:
  replicas: 1  # The number of Pods to run
  selector:
    matchLabels:
      app: demoapi
  template:
    metadata:
      labels:
        app: demoapi
    spec:
      containers:
        - name: demoapi
          image: myregistryluiscoco1974.azurecr.io/mywebapi:v1  # Replace with your Docker image, e.g., "username/demoapi:latest"
          ports:
            - containerPort: 8080
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: demoapi-service
spec:
  type: LoadBalancer  # Exposes the service externally using a load balancer
  selector:
    app: demoapi
  ports:
    - protocol: TCP
      port: 80  # The port the load balancer listens on
      targetPort: 8080  # The port the container accepts traffic on
```

## 4. Create Service-Principal

```
az ad sp create-for-rbac --name luis-service-principal-name ^
    --scopes /subscriptions/1d640b19-d6ae-466b-a0bd-d80869b565b8/resourceGroups/myRG/providers/Microsoft.ContainerRegistry/registries/myregistryluiscoco1974 ^
    --role acrpull ^
    --query "password" ^
    --output tsv
```

**SecretValue**: bKB8Q~Zs2bho7SUQy1G4Qhhq7cOf7Sdgti-nwckf

## 5. Get the ApplicationID

```
az ad sp list --display-name luis-service-principal-name --query "[].appId" --output tsv
```

**ApplicationID**: 70566a6a-4ba8-4b63-9f6e-3979467545c6

## 6. Create Azure Container Registy ACR

```
az acr create --resource-group myRG --name myregistryluiscoco1974 --sku Basic --location westeurope
```

## 7. Login in Azure Container Registry ACR with this command

```
az acr login --name myregistryluiscoco1974
```

```
docker login myregistryluiscoco1974.azurecr.io -u ApplicationID -p SecretValue
```

```
docker build -t myregistryluiscoco1974.azurecr.io/mywebapi:v1 .
```

## 8. Push the Docker image to Azure ACR

```
docker push myregistryluiscoco1974.azurecr.io/mywebapi:v1
```

## 9. Create a new Azure Kubernetes Cluster AKS

```
az aks create --resource-group myRG --name myAKSClusterluiscoco1974 --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Connect to Azure Kubernetes Cluster
```
az aks get-credentials --resource-group myRG --name myAKSClusterluiscoco1974
```

## 10. Deploy the docker image stored in ACR to Azure AKS

```
kubectl apply -f deployment.yml
```

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

To get all the Kubernetes resource type the command:

```
kubectl get all
```

**IMPORTANT NOTE**: see this repo for creating the Docker image (**DockerFile**) and Kubernetes manifest files (**deployment.yml** and **service.yml**): 

https://github.com/luiscoco/SpringBoot_Sample4-Deploy-WebAPI-to-LocalKubernetes
