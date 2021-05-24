# actions-runner-controller

The source code for app itself can be found at [actions-runner-controller](https://github.com/actions-runner-controller/actions-runner-controller). The Helm chart implementation of this repository is 
maintained here.

Details around the solution are kept in the main repository, if you are a new user of the actions-runner-controller repository it is well worth going through the [README.md](https://github.com/actions-runner-controller/actions-runner-controller/blob/master/README.md) to understand how the project works and to track issues.

## Installation
**cert-manager**

actions-runner-controller uses [cert-manager](https://cert-manager.io/docs/installation/kubernetes/) for certificate management of Admission Webhook. Make sure you have already installed cert-manager before you install. The installation instructions for cert-manager can be found below.

- [Installing cert-manager on Kubernetes](https://cert-manager.io/docs/installation/kubernetes/)

**actions-runner-controller**

```shell
# Add the GitHub pages release feed 
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller
# Deploy the default namespace
kubectl create namespace actions-runner-system
# Install the chart
helm upgrade --install --namespace actions-runner-system actions-runner-controller-charts/actions-runner-controller
```

## Configuration

| Key          | Description                                                                    | Default |
|--------------|--------------------------------------------------------------------------------|---------|
| labels       | Kubernetes custom labels that are applied to all resources in the chart        |         |
| replicaCount | The number of controller pods to deploy                                        | 1       |
| syncPeriod   | The period in which the controler will reconcile the number of desired runners | 10m     |
| authSecret   |                                                                                |         |