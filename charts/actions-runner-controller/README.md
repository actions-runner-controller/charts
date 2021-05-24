# actions-runner-controller

The source code for app itself can be found at [actions-runner-controller](https://github.com/actions-runner-controller/actions-runner-controller). The Helm chart implementation of this repository is 
maintained here.

Details around the solution are kept in the main repository, if you are a new user of the actions-runner-controller repository it is well worth going through the [README.md](https://github.com/actions-runner-controller/actions-runner-controller/blob/master/README.md) to understand how the project works and to track issues.

## Installation
**cert-manager**

actions-runner-controller uses [cert-manager](https://cert-manager.io/docs/installation/kubernetes/) for certificate management of Admission Webhook. Make sure you have already installed cert-manager before you install. The installation instructions for cert-manager with Helm can be found below.

- [Installing cert-manager on Kubernetes with Helm](https://cert-manager.io/docs/installation/kubernetes/#installing-with-helm)

**actions-runner-controller**

Before you can deploy the controller you must decide on an authentication method and configure the chart's parameters in the values.yaml for your settings. See the projects [README.md authentication section](https://github.com/actions-runner-controller/actions-runner-controller#setting-up-authentication-with-github-api) for details about your options and the specifics of each authentication type. See the [Configuration](#Configuration) section for the relevant `authSecret` keys.

Once you have decided on your authentication method and configured your values.yaml file we can start the install process:

```shell
# Add the GitHub pages release feed 
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller
# Create a namespace for the deployment (skip this if you have one already)
kubectl create namespace actions-runner-system
# Install the chart into your chosen namespace
helm upgrade --install --namespace actions-runner-system actions-runner-controller-charts/actions-runner-controller --values values.yaml
```

## Configuration

| Key                                            | Description                                                                                                                   | Default                              |
|------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| labels                                         | Kubernetes custom labels that are applied to all resources in the chart                                                                |                                      |
| replicaCount                                   | The number of controller pods to deploy                                                                                                | 1                                    |
| syncPeriod                                     | The period in which the controler will reconcile the number of desired runners                                                         | 10m                                  |
| authSecret.create                              | Deploys the auth secret                                                                                                                | true                                 |
| authSecret.name                                | The name of the auth secret                                                                                                            | controller-manager                   |
| authSecret.github_app_id                       | The ID of your GitHub App. **This can't be set at the same time as authSecret.github_token configuration**                             |                                      |
| authSecret.github_app_installation_id          | The ID of your GitHub App installation. **This can't be set at the same time as authSecret.github_token configuration**                |                                      |
| authSecret.github_app_private_key              | The multiline string of your GitHub App's private key. **This can't be set at the same time as authSecret.github_token configuration** |                                      |
| authSecret.github_token                        | Your chosen GitHub PAT token. **This can't be set at the same time as the authSecret.github_token_app_ configuration**                 |                                      |
| image.repository                               | The "repository/image" of the controller container                                                                                     | summerwind/actions-runner-controller |
| image.dindSidecarRepositoryAndTag              | The "repository/image" of the dind sidecar container                                                                                   |                                      |
| image.pullPolicy                               | The pull policy of the controller image                                                                                                | IfNotPresent                         |
| kube_rbac_proxy.enabled                        | Deploys the kube-proxy container as part of the controller pod if set to true. This is used to protect the metrics endpoint            | true                                 |
| kube_rbac_proxy.image.repository               | The "repository/image" of the kube-proxy                                                                                               | quay.io/brancz/kube-rbac-proxy       |
| kube_rbac_proxy.image.tag                      | The tag of the kube-proxy image to use when pulling the container                                                                      | v0.8.0                               |
| imagePullSecrets                               | Specifies the secret to be used when pulling the controller image                                                                      |                                      |
| nameOverride                                   | Name to be used instead of the chart name for some resources                                                                           |                                      |
| serviceAccount.create                          | Creates a service account if enabled                                                                                                   | true                                 |
| serviceAccont.annotations                      | Adds annotations to the service account                                                                                                |                                      |
| serviceAccount.name                            | The name of the service account                                                                                                        |                                      |
| podAnnotations                                 | Adds pod annotations to the controller and webhook server                                                                              |                                      |
| podSecurityContext                             |                                                                                                                                        |                                      |
| securityContext                                |                                                                                                                                        |                                      |
| service.type                                   |                                                                                                                                        |                                      |
| service.port                                   |                                                                                                                                        |                                      |
| resources                                      |                                                                                                                                        |                                      |
| autoscaling.enabled                            |                                                                                                                                        |                                      |
| autoscaling.minReplicas                        |                                                                                                                                        |                                      |
| autoscaling.maxReplicas                        |                                                                                                                                        |                                      |
| autoscaling.targetCPUUtilizationPercentage     |                                                                                                                                        |                                      |
| autoscaling.targetMemoryUtilizationPercentage  |                                                                                                                                        |                                      |
| nodeSelector                                   |                                                                                                                                        |                                      |
| tolerations                                    |                                                                                                                                        |                                      |
| affinity                                       |                                                                                                                                        |                                      |
| priorityClassName                              |                                                                                                                                        |                                      |
| env                                            | Environment variables to inject into the controller container                                                                          |                                      |
| scope.singleNamespace                          | Limts the controller to watching a single namespace.                                                                                   | false                                |
| scope.watchNamespace                           | Tells the controller which namespace to watch                                                                                          |                                      |
| githubWebhookServer.enabled                    | Deploys the webhook server                                                                                                             | false                                |
| githubWebhookServer.labels                     |                                                                                                                                        |                                      |
| githubWebhookServer.replicaCount               |                                                                                                                                        | 1                                    |
| githubWebhookServer.syncPeriod                 |                                                                                                                                        | 10m                                  |
| githubWebhookServer.secret.create              |                                                                                                                                        | true                                 |
| githubWebhookServer.secret.name                |                                                                                                                                        | github-webhook-server                |
| githubWebhookServer.imagePullSecrets           |                                                                                                                                        |                                      |
| githubWebhookServer.nameOveride                |                                                                                                                                        |                                      |
| githubWebhookServer.fullNameOveride            |                                                                                                                                        |                                      |
| githubWebhookServer.serviceAccount.create      |                                                                                                                                        | true                                 |
| githubWebhookServer.serviceAccount.annotations |                                                                                                                                        |                                      |
| githubWebhookServer.serviceAccount.name        |                                                                                                                                        |                                      |
| githubWebhookServer.podAnnotations             |                                                                                                                                        |                                      |
| githubWebhookServer.podSecurityContext         |                                                                                                                                        |                                      |
| githubWebhookServer.podSecurityContext.fsGroup |                                                                                                                                        |                                      |
| githubWebhookServer.securityContext            |                                                                                                                                        |                                      |
| githubWebhookServer.resources                  |                                                                                                                                        |                                      |
| githubWebhookServer.nodeSelector               |                                                                                                                                        |                                      |
| githubWebhookServer.tolerations                |                                                                                                                                        |                                      |
| githubWebhookServer.affinity                   |                                                                                                                                        |                                      |
| githubWebhookServer.priorityClassName          |                                                                                                                                        |                                      |
| githubWebhookServer.service.type               |                                                                                                                                        |                                      |
|                                                |                                                                                                                                        |                                      |
| githubWebhookServer.ingress.enabled            |                                                                                                                                        | false                                |
| githubWebhookServer.ingress.annotations        |                                                                                                                                        |                                      |
|                                                |                                                                                                                                        |                                      |
|                                                |                                                                                                                                        |                                             