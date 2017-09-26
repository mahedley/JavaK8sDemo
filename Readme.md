<h1>Azure Demo for WebSphere deploy on docker in K8s </h1>
Pre-Requisites

- Docker
  - http://docker.com/get-docker
 
- Helm
  - http://helm.sh
 
- kubectl

- az cli
  - https://github.com/Azure/azure-cli
 
 Instructions
1. Clone the repository
2. Download DefaultServlet from here: https://developer.ibm.com/wasdev/docs/article_appdeployment/
3. Unpack the DefaultServletEngine folder into the WebSphereContainer folder
4. Build the WebSphere Container 
  a. cd WebSphereContainer
  b. build -t demowas .
5. Build the Apache Container
  a. cd WebContainer
  b. build -t demoapache .
6. Create a new container registry with az cli
  a. az group create --location WestUS --name yourregistrygroup
  b. az acr create --name youracr --resource-group yourregistrygroup --sku Basic --admin-enabled=true
7. Connect to your acr with Docker
  a. docker login youracr.azurecr.io -u youracr -p <admin pass>
8. Push your images to your acr
  a. docker image tag demowas youracr.azurecr.io/demowas
  b. docker image tag demoapache youracr.azurecr.io/demoapache
  c. docker push youracr.azurecr.io/demowas
  d. docker push youracr.azurecr.io/demoapache
9. Create a kubernetes deployment on azure
  a. az group create --name youracs --location WestUS
  b. az acs create -n youracs --resource-group youracs -t kubernetes
10. Setup kubectl
  a. Follow the instructions here https://docs.microsoft.com/en-us/azure/container-service/kubernetes/container-service-connect#connect-to-a-kubernetes-cluster
11. Add your container registry to your cluster
  a. kubectl create secret docker-registry youracr.azurecr.io --docker-username=youracr --docker-password=<admin pw> --docker-email=fakemail@fake.com
12. Deploy the helm template
  a. helm install javademo
13. Use kubectl to get the ip address of the apache service
  a. kubectl get services
  b. Record the public ip for the apacheservice once it's up
14. Check that it works
  a. Use a browser to go to http://<apacheservice ip>
