---
layout: page
title: How it works
---

## Prerequisites

We'll assume that you have some familiarity with GitHub Actions and CI/CD concepts. If they are
totally new to you, please checkout the following links to grasp the basics and come back.

1. [GitHub Actions][1]
1. [Continuous Integration](https://continuousdelivery.com/foundations/continuous-integration/)
1. [Continuous Delivery](https://continuousdelivery.com/)

# How it works

PipelineBot is built around [GitHub Actions][1]. It builds pipelines based on GitHub workflow.
It allows you to join your workflows as pipelines with auto or manual trigger. 

<iframe style="border:none" width="100%" height="300" src="https://gifted-lamport-c29e52.netlify.app/"></iframe>

PipelineBot is responsible for
figuring out when to trigger a task. When conditions are met it triggers
a task to the environment that you've specified in the [configuration file][2].
Once the task is triggered, your script defined in the workflow will execute as designed.

The diagram below shows how you interact with the workflows through PipelineBot:
![how-it-works](/assets/images/docs-how-it-works.png)
The benefit to this architecture is that your workflows are separate from
the platform that schedule them. If you change platforms or use multiple
platforms two schedule your workflows you can still enforce the same continuous delivery
processes.

This architecture is well suited for [GitHub Actions][1] which is the GitHub
supported system for running your code in response to GitHub events. It means
that setting up PipelineBot is much simpler since you don't also have to manage
infrastructure to listen to events. The next sections go through guides on how
to implement auto trigger and manual trigger.

[1]: https://github.com/features/actions/
[2]: /docs/configuration/

> TODO: create an example repo as a demo.

## Auto trigger

PipelineBot can be looked as a wrapper of GitHub Actions. Actions
run on GitHub infrastructure in response to GitHub events. The basic format for
setting up a GitHub action looks like this:

```yaml
# .github/workflows/build.yml
name: 'build'
on: [push]

jobs:
  build:
    runs-on: 'ubuntu-latest'
    steps: []
    # Steps to execute your build.
```

```yaml
# .github/workflows/test.yml
name: 'test'
on: workfolow_dispatch

jobs:
  build:
    runs-on: 'ubuntu-latest'
    steps: []
    # Steps to execute your test.
```

The following configuration sets up a pipeline that starts the **Test** `stage`
automatically when the **Build** `stage` finishes.

```yaml
# .github/pipelines/pipeline-0.yml
version: 0.3.0-beta
stages:
  - Build
  - Test
 
tasks:
  - workflow: build
    stage: Build
  - workflow: test
    stage: Test
```

## Manual trigger

Let's say we have a third stage `Production` that you would like to trigger it manually.
The corresponding workflow looks like this:

```yaml
# .github/workflows/deploy-to-production.yml
name: 'deploy to production'
on: workflow_dispatch

jobs:
  build:
    runs-on: 'ubuntu-latest'
    steps: []
    # Steps to execute your deployment.
``` 

In the pipeline configuration file, you need to add a new stage and mark the trigger
as manual.

```yaml
# .github/workflows/pipeline-0.yml
version: 0.3.0-beta
stages:
  - Build
  - Test
  - Production

tasks:
  - workflow: build
    stage: Build
  - workflow: test
    stage: Test
  - workflow: deploy to production
    stage: Production
```
