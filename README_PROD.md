# Instructions

This readme will show you how to create a production ready Matomo setup with Helm.

## Requirements

* A kubernetes cluster
* Kubectl
* Helm

## Install Helm.

1. Make sure you have helm installed in your Kubernetes cluster.

    `helm init`


## Deploy Matomo helm charts to K8S for the first time.

1. Create matomo namespace.

    `kubectl create namespace matomo-prod`

2. Create secret about your registry to where the matomo image is and the authentication for that.

    `kubectl --namespace matomo-prod create secret docker-registry matomo-registry-secret --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-password> --docker-email=<your-email>`

3. Create secret about your OpenStack authentication details.

    ```
    kubectl -n matomo create secret generic matomo-openstack-secret \
    --from-literal=ST_AUTH_VERSION=3 \
    --from-literal=OS_USERNAME=<your-username> \
    --from-literal=OS_USER_DOMAIN_NAME=<your-user-domain-name> \
    --from-literal=OS_PASSWORD=<your-password> \
    --from-literal=OS_PROJECT_NAME=<your-project-name> \
    --from-literal=OS_PROJECT_DOMAIN_NAME=<your-project-domain-name> \
    --from-literal=OS_AUTH_URL=<your-provider-auth-url> \
    --from-literal=OS_USER_ID=<your-user-id> \
    --from-literal=OS_PROJECT_ID=<your-project-id> \
    --from-literal=OS_SWIFT_CONTAINER=<your-swift-container> \
    ```

4. Deploy the redis helm chart.

    Go to the redis directory and run:

    `helm dependencies update` - Downloads dependencies for the Helm chart

    `helm install -n matomo-redis . --namespace=matomo-prod -f values.yaml`

5. Deploy the mysql helm chart.

    Go to the mysql directory and run:

    `helm dependencies update` - Downloads dependencies for the Helm chart

    `helm install -n matomo-db . --namespace=matomo-prod -f values.yaml`

6. Deploy the matomo helm chart.

    Go to the matomo directory and run:

    `helm install -n matomo . --namespace=matomo-prod -f values.yaml`

---

## To update the Matomo K8S setup with your changes:

If you have made any changes in a helm chart, use this command to update pods, services, configmaps etc. in your cluster. Go to the specific directory for the helm chart.

Can either be 'matomo', 'matomo-db' or 'matomo-mysql'.

`helm upgrade matomo . --namespace=matomo-prod -f values.yaml`