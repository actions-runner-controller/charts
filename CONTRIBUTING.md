## How to Contribute

1. Fork this repository, develop, and test your changes
2. Submit a pull request

### Technical Requirements

* Must follow [Charts best practices](https://helm.sh/docs/topics/chart_best_practices/)
* Must pass CI jobs for linting and installing changed charts with the [chart-testing](https://github.com/helm/chart-testing) tool

### Immutability

Chart releases must be immutable. Any change to a chart warrants a chart version bump even if it is only changed to the documentation.

### Versioning

The chart `version` should follow [semver](https://semver.org/). Any breaking (backwards incompatible) changes to a chart should:

1. Bump the MAJOR version

### Chart Releases

* A release will happen once the maintainers are ready for a new version of the chart to be released, we do not release on every change

