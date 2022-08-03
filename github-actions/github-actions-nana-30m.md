# GitHub Actions Tutorial - Basic Concepts and CI/CD Pipeline with Docker

* source: <GitHub Actions Tutorial - Basic Concepts and CI/CD Pipeline with Docker>
* channel: <https://www.youtube.com/c/TechWorldwithNana>
* tags: #githubactions, #github, #cicd

## Timestamps

* [0:00](https://www.youtube.com/watch?v=R8_veQiYBjI&t=0s) - Intro
* [01:03](https://www.youtube.com/watch?v=R8_veQiYBjI&t=63s) - What is Github Actions?
* [01:29](https://www.youtube.com/watch?v=R8_veQiYBjI&t=89s) - What are developer workflows? Use Cases for GitHub Actions
* [04:50](https://www.youtube.com/watch?v=R8_veQiYBjI&t=290s) - Basic Concepts of GitHub Actions: How GitHub Actions automates those workflows? GitHub Events & Actions
* [06:41](https://www.youtube.com/watch?v=R8_veQiYBjI&t=401s) - GitHub Actions CI/CD
* [07:18](https://www.youtube.com/watch?v=R8_veQiYBjI&t=438s) - Why another CI/CD Tool - Benefits of Github Actions
* [09:50](https://www.youtube.com/watch?v=R8_veQiYBjI&t=590s) - DEMO - Create CI Workflow or Pipeline
* [13:04](https://www.youtube.com/watch?v=R8_veQiYBjI&t=784s) - Syntax of Workflow File
* [20:40](https://www.youtube.com/watch?v=R8_veQiYBjI&t=1240s) - Where does this Workflow Code run? GitHub Action Runner
* [24:35](https://www.youtube.com/watch?v=R8_veQiYBjI&t=1475s) - Build Docker Image and push to private Docker Repo

## Concepts

* Automates workflow, more than just CI/CD pipelines

* Use Cases for GitHub Actions: new release of the code - issue fixed, PR, rebuild: test
* happens based on events - something happens IN your repo or TO your repo
* event can be produced by integrations

Event triggers one or more runners that run jobs - in parallel or in sequence

![](https://docs.github.com/assets/cb-25535/images/help/images/overview-actions-simple.png)

Workflow is defined by YAML file, runs one or more jobs.

Repo can have multiple workflows in `.github/workflows`

JOB: set of steps in a workflow that execute on the same runner. Each step is action or shell script. Steps can share data.

Action: custom app for GH Actions platform

Runner: a container

Job can depend on other job => waits for it

Event: specific activity in a repository that triggers a workflow run.

* PR
* push
* open issue
* scheduler
* REST POST to API

See practice repo: <https://github.com/miroadamy-practice/github-actions-demo-1>

## Examples Workflows

* CI/CD: on commit: TEST -> BULD -> PUSH -> DEPLOY

In addition to default, there is marketplace: <https://github.com/marketplace>

## Syntax of the file

Must have `.yml` or `.yaml`

See <https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions>

Where is this documented: <https://github.com/actions>, see `./action.yml` => <https://github.com/actions/checkout/blob/main/action.yml>

<https://github.com/actions/checkout#Checkout-multiple-repos-private>

This one is useful: <https://github.com/actions/starter-workflows/blob/main/deployments/aws.yml>
