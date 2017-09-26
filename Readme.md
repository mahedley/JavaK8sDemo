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
  - cd WebSphereContainer
  - build -t demowas .
5. Build the Apache Container
  - cd WebContainer
  - build -t demoapache .
6. Create a new container registry with az cli
  - az group create --location WestUS --name yourregistrygroup
  - az acr create --name youracr --resource-group yourregistrygroup --sku Basic --admin-enabled=true
7. Connect to your acr with Docker
  - docker login youracr.azurecr.io -u youracr -p adminpw
8. Push your images to your acr
  - docker image tag demowas youracr.azurecr.io/demowas
  - docker image tag demoapache youracr.azurecr.io/demoapache
  - docker push youracr.azurecr.io/demowas
  - docker push youracr.azurecr.io/demoapache
9. Create a kubernetes deployment on azure
  - az group create --name youracs --location WestUS
  - az acs create -n youracs --resource-group youracs -t kubernetes
10. Setup kubectl
  - Follow the instructions here https://docs.microsoft.com/en-us/azure/container-service/kubernetes/container-service-connect#connect-to-a-kubernetes-cluster
11. Add your container registry to your cluster
  - kubectl create secret docker-registry youracr.azurecr.io --docker-username=youracr --docker-password=adminpw --docker-email=fakemail@fake.com
12. Deploy the helm template
  - helm install javademo
13. Use kubectl to get the ip address of the apache service
  - kubectl get services
  - Record the public ip for the apacheservice once it's up
14. Check that it works
  - Use a browser to go to http://apacheserviceip
