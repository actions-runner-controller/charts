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
# Add the GitHub Pages release feed 
helm repo add actions-runner-controller-charts https://actions-runner-controller.github.io/charts/
# Create a namespace to deploy into (skip this if you have one you intend to use already)
kubectl create namespace actions-runner-system
# Install the chart into your chosen namespace
helm upgrade --install --namespace actions-runner-system actions-runner-controller-charts/actions-runner-controller --values values.yaml
```

## Configuration

_Default values are documented as of the latest released version of the chart_

_Default values are the defaults set in the charts values.yaml, some properties have default configurations in the code for when the property is omitted or invalid_

| Key                                                      | Description                                                                                                                | Default                                                              |
|----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| `labels`                                                 | Set labels to apply to all resources in the chart                                                                          |                                                                      |
| `replicaCount`                                           | Set the number of controller pods                                                                                          | 1                                                                    |
| `syncPeriod`                                             | Set the period in which the controler reconciles the desired runners count                                                 | 10m                                                                  |
| `githubAPICacheDuration`                                 | Set the cache period for API calls                                                                                         |                                                                      |
| `logLevel`                                               | Set the log level of the controller container                                                                              |                                                                      |
| `authSecret.create`                                      | Deploy the controller auth secret                                                                                          | true                                                                 |
| `authSecret.name`                                        | Set the name of the auth secret                                                                                            | controller-manager                                                   |
| `authSecret.github_app_id`                               | The ID of your GitHub App. **This can't be set at the same time as `authSecret.github_token`**                             |                                                                      |
| `authSecret.github_app_installation_id`                  | The ID of your GitHub App installation. **This can't be set at the same time as `authSecret.github_token`**                |                                                                      |
| `authSecret.github_app_private_key`                      | The multiline string of your GitHub App's private key. **This can't be set at the same time as `authSecret.github_token`** |                                                                      |
| `authSecret.github_token`                                | Your chosen GitHub PAT token. **This can't be set at the same time as the `authSecret.github_app_*`**                      |                                                                      |
| `image.repository`                                       | The "repository/image" of the controller container                                                                         | summerwind/actions-runner-controller                                 |
| `image.tag`                                              | The tag of the controller container                                                                                        |                                                                      |
| `image.dindSidecarRepositoryAndTag`                      | The "repository/image" of the dind sidecar container                                                                       | docker:dind                                                          |
| `image.pullPolicy`                                       | The pull policy of the controller image                                                                                    | IfNotPresent                                                         |
| `metrics.serviceMonitor`                                 | Deploy serviceMonitor kind for for use with prometheus-operator CRDs                                                       | false                                                                |
| `metrics.port`                                           | Set port of metrics service                                                                                                | 8443                                                                 |
| `metrics.proxy.enabled`                                  | Deploy kube-rbac-proxy container in controller pod                                                                         | true                                                                 |
| `metrics.proxy.image.repository`                         | The "repository/image" of the kube-proxy container                                                                         | quay.io/brancz/kube-rbac-proxy                                       |
| `metrics.proxy.image.tag`                                | The tag of the kube-proxy image to use when pulling the container                                                          | v0.8.0                                                               |
| `imagePullSecrets`                                       | Specifies the secret to be used when pulling the controller pod containers                                                 |                                                                      |
| `fullNameOverride`                                       | Override the full resource names	                                                                                          |                                                                      |
| `nameOverride`                                           | Override the resource name prefix	                                                                                        |                                                                      |
| `serviceAccont.annotations`                              | Set annotations to the service account                                                                                     |                                                                      |
| `serviceAccount.create`                                  | Deploy the controller pod under a service account                                                                          | true                                                                 |
| `podAnnotations`                                         | Set annotations for the controller pod                                                                                     |                                                                      |
| `podLabels`                                              | Set labels for the controller pod                                                                                          |                                                                      |
| `serviceAccount.name`                                    | Set the name of the service account                                                                                        |                                                                      |
| `securityContext`                                        | Set the security context for each container in the controller pod                                                          |                                                                      |
| `podSecurityContext`                                     | Set the security context to controller pod                                                                                 |                                                                      |
| `service.port`                                           | Set controller service type                                                                                                |                                                                      |
| `service.type`                                           | Set controller service ports                                                                                               |                                                                      |
| `topologySpreadConstraints`                              | Set the controller pod topologySpreadConstraints                                                                           |                                                                      |
| `nodeSelector`                                           | Set the controller pod nodeSelector                                                                                        |                                                                      |
| `resources`                                              | Set the controller pod resources                                                                                           |                                                                      |
| `affinity`                                               | Set the controller pod affinity rules                                                                                      |                                                                      |
| `tolerations`                                            | Set the controller pod tolerations                                                                                         |                                                                      |
| `env`                                                    | Set environment variables for the controller container                                                                     |                                                                      |
| `priorityClassName`                                      | Set the controller pod priorityClassName                                                                                   |                                                                      |
| `scope.watchNamespace`                                   | Tells the controller which namespace to watch if `scope.singleNamespace` is true                                           |                                                                      |
| `scope.singleNamespace`                                  | Limit the controller to watch a single namespace                                                                           | false                                                                |
| `githubWebhookServer.logLevel`                           | Set the log level of the githubWebhookServer container                                                                     |                                                                      |
| `githubWebhookServer.replicaCount`                       | Set the number of webhook server pods                                                                                      | 1                                                                    |
| `githubWebhookServer.enabled`                            | Deploy the webhook server pod                                                                                              | false                                                                |
| `githubWebhookServer.secret.create`                      | Deploy the webhook hook secret                                                                                             | true                                                                 |
| `githubWebhookServer.secret.name`                        | Set the name of the webhook hook secret                                                                                    | github-webhook-server                                                |
| `githubWebhookServer.secret.github_webhook_secret_token` | Set the webhook secret token value                                                                                         |                                                                      |
| `githubWebhookServer.imagePullSecrets`                   | Specifies the secret to be used when pulling the githubWebhookServer pod containers                                        |                                                                      |
| `githubWebhookServer.nameOveride`                        | Override the resource name prefix	                                                                                        |                                                                      |
| `githubWebhookServer.fullNameOveride`                    | Override the full resource names	                                                                                          |                                                                      |
| `githubWebhookServer.serviceAccount.create`              | Deploy the githubWebhookServer under a service account                                                                     | true                                                                 |
| `githubWebhookServer.serviceAccount.annotations`         | Set annotations for the service account                                                                                    |                                                                      |
| `githubWebhookServer.serviceAccount.name`                | Set the service account name                                                                                               |                                                                      |
| `githubWebhookServer.podAnnotations`                     | Set annotations for the githubWebhookServer pod                                                                            |                                                                      |
| `githubWebhookServer.podLabels`                          | Set labels for the githubWebhookServer pod                                                                                 |                                                                      |
| `githubWebhookServer.podSecurityContext`                 | Set the security context to githubWebhookServer pod                                                                        |                                                                      |
| `githubWebhookServer.securityContext`                    | Set the security context for each container in the githubWebhookServer pod                                                 |                                                                      |
| `githubWebhookServer.resources`                          | Set the githubWebhookServer pod resources                                                                                  |                                                                      |
| `githubWebhookServer.topologySpreadConstraints`          | Set the githubWebhookServer pod topologySpreadConstraints                                                                  |                                                                      |
| `githubWebhookServer.nodeSelector`                       | Set the githubWebhookServer pod nodeSelector                                                                               |                                                                      |
| `githubWebhookServer.tolerations`                        | Set the githubWebhookServer pod tolerations                                                                                |                                                                      |
| `githubWebhookServer.affinity`                           | Set the githubWebhookServer pod affinity rules                                                                             |                                                                      |
| `githubWebhookServer.priorityClassName`                  | Set the githubWebhookServer pod priorityClassName                                                                          |                                                                      |
| `githubWebhookServer.service.type`                       | Set githubWebhookServer service type                                                                                       |                                                                      |
| `githubWebhookServer.service.ports`                      | Set githubWebhookServer service ports                                                                                      | `[{"port":80, "targetPort:"http", "protocol":"TCP", "name":"http"}]` |
| `githubWebhookServer.ingress.enabled`                    | Deploy an ingress kind for the githubWebhookServer                                                                         | false                                                                |
| `githubWebhookServer.ingress.annotations`                | Set annotations for the ingress kind                                                                                       |                                                                      |
| `githubWebhookServer.ingress.hosts`                      | Set hosts configuration for ingress                                                                                        | `[{"host": "chart-example.local", "paths": []}]`                     |
| `githubWebhookServer.ingress.tls`                        | Set tls configuration for ingress                                                                                          |                                                                      |

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
