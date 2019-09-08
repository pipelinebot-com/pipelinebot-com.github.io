---
layout: page
title: Recipes
group: Recipes
---

# Recipes

Recipes for deploy configuration with Deliverybot are found here:

- [Deploy on master](deploy-on-master)
- [Deploy a PR environment](deploy-pr-environments)
- [Build promotion](build-promotion)
- [Canary deployments](canary-deployments)


Example repositories are also a great way of getting familiar with
configuration:

- [Helm example](https://github.com/deliverybot/example-helm)
- [Bare example](https://github.com/deliverybot/example)

## Configuration

Deliverybot controls deployments based on a configuration file. Targets are the
top level resource for this file which house configuration to trigger a list of
deployments depending on specific conditions. Documentation for those conditions
and fields is provided below.

```yaml {% raw %}
# .github/deploy.yml
production:
  deployments:
  - environment: name {% endraw %}
```

- [`<target>.auto_deploy_on`](#targetauto_deploy_on)
- [`<target>.deployments`](#targetdeployments)
- [`<target>.deployments[].environment`](#targetdeploymentsenvironment)
- [`<target>.deployments[].task`](#targetdeploymentstask)
- [`<target>.deployments[].auto_merge`](#targetdeploymentsauto_merge)
- [`<target>.deployments[].payload`](#targetdeploymentspayload)
- [`<target>.deployments[].transient_environment`](#targetdeploymentstransient_environment)
- [`<target>.deployments[].production_environment`](#targetdeploymentsproduction_environment)

##### `<target>.auto_deploy_on`

Controls auto deployment behaviour given a ref. If any new push events are
detected on this event, the deployment will be triggered.

```yaml {% raw %}
# .github/deploy.yml
production:
  auto_deploy_on: refs/heads/master {% endraw %}
```

##### `<target>.deployments[].environment`

The name of the environment that was deployed to (e.g., staging or production).
Default: none


##### `<target>.deployments[].task`

The name of the task for the deployment (e.g., deploy or deploy:migrations).
Default: none

Removal deployments are a special type of deployment that is fired by
deliverybot when a PR is closed and resources need to be cleaned up. This type
of deployment can be detected because task will equal `remove` in this case.
Use this as a hook to clean up resources not needed anymore.

##### `<target>.deployments[].auto_merge`

Attempts to automatically merge the default branch into the requested ref, if
it's behind the default branch. Default: false

##### `<target>.deployments[].payload`

Payload with extra information about the deployment. Default: {}

##### `<target>.deployments[].transient_environment`

Specifies if the given environment is specific to the deployment and will no
longer exist at some point in the future. Default: false

If this value is `true` all deployments triggered on a pr will be marked
inactive and a removal deployment will be fired to clean up all unique
environments.

##### `<target>.deployments[].production_environment`

Specifies if the given environment is one that end-users directly interact with.
Default: true when environment is production and false otherwise.

## Variables

The following variables are available using `{% raw %}{{ }}{% endraw %}` syntax
when evaluating deployment targets:

- `ref`: Deployment ref, ie: `master`.
- `sha`: Full git sha.
- `short_sha`: Short git sha.
- `pr`: Pr number, if this deployment is kicked off in a PR.

An example usage of this:

```yaml {% raw %}
# .github/deploy.yml
review:
  deployments:
  - # Dynamic environment name. The environment will look like pr123.
    environment: pr{{ pr }} {% endraw %}
```