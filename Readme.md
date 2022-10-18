# Install skupper with Vault integration 

Skupper resources require a configmap named skupper-site to be available prior to deployment of other components, Hence the deployment has been split into two parts.

1. Deployment of Skupper-site configmap
2. Deployment of Other skupper resources

## Deployment of Skupper-site configmap

Helm chart called skupper-site is responsible for deploying the config map. This is a straight forward deployment.

```
helm upgrade --install skupper-site ./skupper-site
```
 This will deploy the configmap required for skupper.


## Deployment of Other skupper resources.

Multiple resources get deployed as part of skupper install, Below are the summary.

1. Roles 
2. Rolebindings
3. Deployments 
4. service accounts
5. configmaps
6. services
7. routes

### Deployments

There are three deployments for skupper

1. Skupper-router -  Takes care of all routing 
2. skupper-service-controller - Takes care of service creation and service sync between sites
3. skupper-site-controller - Monitors namespace for skupper annotations to create services

### Configmaps

There are three configmaps for skupper

1. skupper-site -- This will hold information of the skupper site such as its name, router config etc
2. skupper-internal -- it has all the details required for the router to work. 
3. skupper-services -- holds the details of all the services being exposed, Will be empty at the time of install and will be updated at the time of Service exposure

### certificates

All the certificates required for skupper to run are pulled from vault at the time of installation. The path for the skupper certs are under ddtb/skupper. Each squad has a single certificate created for them and are present with the naming convention skupper-access, skupper-payments etc.

Values file should be updated for the field "tribe" to match the squad(namespace), so that the right certificate is pulled at the time of deployment.

if this is your first install of skupper, and you dont have any remote site to connect to, Then ignore the remotesites section, but if you have any remote sites and you want to connect your skupper to Remote. Update the "remoteSites" section with the name of the site and the interrouter remote URL, This will be used to update the skupper-internal configmap with the right details required to make connection.

## how to deploy

Once the values file is updated. deployment is straightforward.

``` 
helm install skupper ./skupper 
```