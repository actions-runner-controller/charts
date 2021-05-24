# actions-runner-controller

The source code for app itself can be found at [actions-runner-controller](https://github.com/actions-runner-controller/actions-runner-controller). The Helm chart implementation of this repository is 
maintained here.

Details around the solution are kept in the main repository as well as non-helm chart specific issues. If you are a new user of the actions-runner-controller project please familiarise youself with the main repository by going through the [README.md](https://github.com/actions-runner-controller/actions-runner-controller/blob/master/README.md). This is also where the usage of the runners themselves are documented and maintained so it is essential you read the README.md to be able to use this chart.

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

_Default values are documented as of the latest released version of the chart_

| Key                                              | Description                                                                                                                              | Default                              |
|--------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| `labels`                                         | Kubernetes custom labels that are applied to all resources in the chart                                                                  |                                      |
| `replicaCount`                                   | The number of controller pods to deploy                                                                                                  | 1                                    |
| `syncPeriod`                                     | The period in which the controler will reconcile the number of desired runners                                                           | 10m                                  |
| `authSecret.create`                              | Deploys the auth secret                                                                                                                  | true                                 |
| `authSecret.name`                                | The name of the auth secret                                                                                                              | controller-manager                   |
| `authSecret.github_app_id`                       | The ID of your GitHub App. **This can't be set at the same time as `authSecret.github_token`**                                           |                                      |
| `authSecret.github_app_installation_id`          | The ID of your GitHub App installation. **This can't be set at the same time as `authSecret.github_token`**                              |                                      |
| `authSecret.github_app_private_key`              | The multiline string of your GitHub App's private key. **This can't be set at the same time as `authSecret.github_token`**               |                                      |
| `authSecret.github_token`                        | Your chosen GitHub PAT token. **This can't be set at the same time as the `authSecret.github_token_app_*`**                              |                                      |
| `image.repository`                               | The "repository/image" of the controller container                                                                                       | summerwind/actions-runner-controller |
| `image.dindSidecarRepositoryAndTag`              | The "repository/image" of the dind sidecar container                                                                                     | docker:dind                          |
| `image.pullPolicy`                               | The pull policy of the controller image                                                                                                  | IfNotPresent                         |
| `kube_rbac_proxy.enabled`                        | Deploy the kube-proxy container as part of the controller pod. This is used to protect the metrics endpoint                              | true                                 |
| `kube_rbac_proxy.image.repository`               | The "repository/image" of the kube-proxy                                                                                                 | quay.io/brancz/kube-rbac-proxy       |
| `kube_rbac_proxy.image.tag`                      | The tag of the kube-proxy image to use when pulling the container                                                                        | v0.8.0                               |
| `imagePullSecrets`                               | Specifies the secret to be used when pulling the controller image                                                                        |                                      |
| `nameOverride`                                   | Override the resource name prefix	                                                                                                      |                                      |
| `fullNameOverride`                               | Override the full resource names	                                                                                                      |                                      |	
| `serviceAccount.create`                          | Creates a service account if enabled                                                                                                     | true                                 |
| `serviceAccont.annotations`                      | Adds annotations to the service account                                                                                                  |                                      |
| `serviceAccount.name`                            | The name of the service account                                                                                                          |                                      |
| `podAnnotations`                                 | Adds pod annotations to the controller and webhook server                                                                                |                                      |
| `podSecurityContext`                             |                                                                                                                                          |                                      |
| `securityContext`                                |                                                                                                                                          |                                      |
| `service.type`                                   |                                                                                                                                          |                                      |
| `service.port`                                   |                                                                                                                                          |                                      |
| `resources`                                      |                                                                                                                                          |                                      |
| `autoscaling.enabled`                            |                                                                                                                                          |                                      |
| `autoscaling.minReplicas`                        |                                                                                                                                          |                                      |
| `autoscaling.maxReplicas`                        |                                                                                                                                          |                                      |
| `autoscaling.targetCPUUtilizationPercentage`     |                                                                                                                                          |                                      |
| `autoscaling.targetMemoryUtilizationPercentage`  |                                                                                                                                          |                                      |
| `nodeSelector`                                   | Sets pod nodeSelector                                                                                                                    |                                      |
| `tolerations`                                    | Sets pod tolerations                                                                                                                     |                                      |
| `affinity`                                       | Sets pod affinity rules                                                                                                                  |                                      |
| `priorityClassName`                              | Sets kubernetes priorityClassName                                                                                                        |                                      |
| `env`                                            | Environment variables to inject into the controller container                                                                            |                                      |
| `scope.singleNamespace`                          | Limts the controller to watching a single namespace.                                                                                     | false                                |
| `scope.watchNamespace`                           | Tells the controller which namespace to watch if `scope.singleNamespace` is true                                                         |                                      |
| `githubWebhookServer.enabled`                    | Deploy the webhook server                                                                                                                | false                                |
| `githubWebhookServer.labels`                     |                                                                                                                                          |                                      |
| `githubWebhookServer.replicaCount`               |                                                                                                                                          | 1                                    |
| `githubWebhookServer.syncPeriod`                 |                                                                                                                                          | 10m                                  |
| `githubWebhookServer.secret.create`              |                                                                                                                                          | true                                 |
| `githubWebhookServer.secret.name`                |                                                                                                                                          | github-webhook-server                |
| `githubWebhookServer.imagePullSecrets`           |                                                                                                                                          |                                      |
| `githubWebhookServer.nameOveride`                | Override the resource name prefix	                                                                                                      |                                      |
| `githubWebhookServer.fullNameOveride`            | Override the full resource names	                                                                                                      |                                      |
| `githubWebhookServer.serviceAccount.create`      |                                                                                                                                          | true                                 |
| `githubWebhookServer.serviceAccount.annotations` |                                                                                                                                          |                                      |
| `githubWebhookServer.serviceAccount.name`        |                                                                                                                                          |                                      |
| `githubWebhookServer.podAnnotations`             |                                                                                                                                          |                                      |
| `githubWebhookServer.podSecurityContext`         |                                                                                                                                          |                                      |
| `githubWebhookServer.podSecurityContext.fsGroup` |                                                                                                                                          |                                      |
| `githubWebhookServer.securityContext`            |                                                                                                                                          |                                      |
| `githubWebhookServer.resources`                  |                                                                                                                                          |                                      |
| `githubWebhookServer.nodeSelector`               | Sets pod nodeSelector                                                                                                                    |                                      |
| `githubWebhookServer.tolerations`                | Sets pod tolerations                                                                                                                     |                                      |
| `githubWebhookServer.affinity`                   | Sets pod affinity rules                                                                                                                  |                                      |
| `githubWebhookServer.priorityClassName`          | Sets pod priorityClassName                                                                                                               |                                      |
| `githubWebhookServer.service.type`               |                                                                                                                                          |                                      |
|                                                  |                                                                                                                                          |                                      |
| `githubWebhookServer.ingress.enabled`            |                                                                                                                                          | false                                |
| `githubWebhookServer.ingress.annotations`        |                                                                                                                                          |                                      |
|                                                  |                                                                                                                                          |                                      |
|                                                  |                                                                                                                                          |                                                                                       

## Advanced Deployments

### Namespaced Controllers

For large environments which are unable to use the webhook server you may run into rate limiting issues. We provide a few scaling metrics some which scale better than others in terms of API consumption however this may not be enough. In those instances you may wish to deploy multiple controllers, allowing you to use multiple PATs or GitHub App installations, effectively increasing your API rate limit.

We achieve this capability on a single cluster by deploying controllers to their own namespaces and limiting them to that namespace. For example if we wanted to deploy 2 controllers we would set the 2 values.yaml files to:

```yaml
# values1.yaml
scope:
  singleNamespace: true
  watchNamespace: actions-runner-system-1
```

`helm upgrade --install --namespace actions-runner-system-1 actions-runner-controller-charts/actions-runner-controller --values values1.yaml`

```yaml
# values2.yaml
scope:
  singleNamespace: true
  watchNamespace: actions-runner-system-2
```

`helm upgrade --install --namespace actions-runner-system-2 actions-runner-controller-charts/actions-runner-controller --values values2.yaml`

We then just need to deploy the relevant [runners](https://github.com/actions-runner-controller/actions-runner-controller#usage) for your environment to each namespace.
