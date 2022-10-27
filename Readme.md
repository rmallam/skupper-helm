# Install skupper with Vault integration 

## Pre-requisites

1. Images for deployment should be pulled from quay and pushed into custom repository
2. Certificates required for each namespaces are created and pushed into hashicorp vault 

## Process

Skupper resources require a configmap named skupper-site to be available prior to deployment of other components, Hence the deployment has been split into two parts.

1. Deployment of Skupper-site configmap
2. Deployment of skupper router and configmaps
3. Deployment of skupper controllers i.e (service controller and Site controller)

Note: All three components are in different using different helm charts and should be deployed in the same order as mentioned above because of their inter dependencies.

## Deployment of Skupper-site configmap

Helm chart called skupper-site is responsible for deploying a config map, this configmap has the information about how the site should look like, The router resources, ingress values etc. This is a straight forward deployment.

```
helm upgrade --install skupper-site ./skupper-site
```
 This will deploy the configmap required for skupper.

more information here
https://github.com/skupperproject/skupper/tree/master/cmd/site-controller

## Deployment of skupper router and configmaps.

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

## how to deploy

### Values based on environments/namespaces

#### certificates

All the certificates required for skupper to run are pulled from vault at the time of installation. The path for the skupper certs are under ddtb/skupper. Each squad has a single certificate created for them and are present with the naming convention skupper-access, skupper-payments etc.

Values file specific to the namespaces should be updated for the field "squad" to match the squad(namespace), so that the right certificate is pulled at the time of deployment. below snipped from values.yaml

```
squad: test
```

#### Remote sites -- linking skupper with other skupper install
if this is your first install of skupper, and you dont have any remote site to connect to, Then ignore the remotesites section, but if you have any remote sites and you want to connect your skupper to Remote. Update the "remoteSites" section with the name of the site and the interrouter remote URL in the namespace specific values file, This will be used to update the skupper-internal configmap with the right details required to make connection.

```
# remoteSites 
# if you need to link this site to any other skupper site, add the details below. Name of the other skupper site which will usually be the namespace of the other site where skupper is installed and the inter-router route URL from the other namespace.
remoteSites:
    #example
    # - name: test
    #   host: skupper-inter-router-test.test.com
```

#### images for deployment

All images are pulled from Quay.io/skupper and pushed into custom registry . These images are specific to each version of skupper. every version of skupper will have 4 container images associated with it. 
1. skupper-router 
2. skupper-service-controller
3. skupper-site-controller
4. config-sync

Skupper-router will have a different version but all the other images have the same version as the skupper version. Please check the manifests under 

Once the values file is updated. deployment is straightforward.

``` 
helm upgrade --install skupper ./skupper
```

## Deployment of skupper controllers i.e (service controller and Site controller)

Once the Skupper-site configmap and skupper-routers have been deployed, Deploy the controllers(Service and site controller) using the helm chart.

```
helm upgrade --install skupper-controller 
```


### Example deployment

```
helm upgrade --install skupper-site skupper-site  # installs skupper site configmap
helm upgrade --install skupper skupper # install skupper-router and other resources like configmaps, role, rolebindings etc
helm upgrade --install skupper-controller skupper-controllers  # deploys site and service controllers
```