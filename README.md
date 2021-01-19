# OpenShift 4 - Kitchen Sink Demo

## Platform Overview - The Foundation

### Helping Overcome Common Challenges

#### Challenge: Consistency for Developers and Operations
* Same platform in any data centre
* Operational consistency - Configuration as code, UI, tooling
* Developer consistency - Same base OS, RBAC, kubernetes version, networking, UI, cli...
* Use the CI/CD tooling that comes out of the box, or any other tool that's right for you (GitLab, Azure DevOps, Bamboo, etc..)

**Tour:** Walk through Admin and Developer dashboards.  Use clusters in different data centres if possible.

#### Challenge: Multi-Tenancy
* Infrastructure sprawl is expensive and hard to manage (VMs, k8s cluster per project ,etc...)
* OpenShift is multi-tenant by default with strong RBAC baked in as well as Quotas/Limits, NetworkPolicy, Monitoring/Logging, and an integrated CLI.
* Safe and effective self-service: Empower developer teams while still limiting sprawl and managing capacity.

**Tour:** Create a Project as `user1`.  Log in as `user2` to see that there is no access.  Access can be granted with Role Based Access Control.

#### Challenge: Secure, Supported Containers and Best Practices
    * Maintaining secure container images can be challenging and time consuming.
    * Red Hat offers a wide range of base container images for most popular languages and runtimes, such as:
        * OpenJDK 8/11
        * .Net Core
        * Python
        * PHP
        * Go
        * Ruby
        * more...
    * This also includes certain open source databases, such as:
        * PostgreSQL
        * MySQL and MariaDB
    * And Identity Management with Red Hat SSO (Keycloak)

**Tour:** Explore the catalog of languages and runtimes, as well as the built-in OperatorHub interface.

## Demos

#### Get Started

1. Login to your cluster with the OpenShift cli as a `cluster-admin`

Now, for some rapid-fire demos to highlights a few core features of OpenShift, the advantages they bring, and the benefit they can have for your organization:

#### Source-to-Image (s2i)

Source-to-Image (or *s2i* for short) is a feature that allows Developers to quickly take source code, or a pre-built binary, and create easily create an OCI-compliant container image for the application.

The advantage of using s2i base images is they provided a consisent mechanism for developers to containerize their applications, either directly from source code in a Git repository, or from an application binary that was pre-built by a CI/CD system.

The benefits of this process include consistency of container image builds and no need to maintain an image building tool such as Docker or Buildah as part of your CI/CD system.

**Demo:** 

* Build and deploy Pet Clinic from source.
    * Create a new project, the `Add from git` the repository: https://github.com/pittar/spring-petclinic

#### CI/CD From Scratch!

You may already have your favourite CI/CD tooling, but did you know that OpenShift comes with a supported Jenkins image, as well as *OpenShift Pipelines*?

Jenkins fills the role of traditional CI/CD tool and provides a huge amount of flexibility with a vast amount of community plugins.

OpenShift Pipelines, based on the upstream [Tekton](https://tekton.dev/) project, is a Kubernetes-native "serverless" CI/CD framework.  It is currently "Tech Preview" in OpenShift 4.6, but expected to be a fully "GA" component of OpenShift 4.7.

Either way, the declarative nature of Kubernetes gives you the ability to apply "infrastructure as code" to anything in your environment, including your CI/CD stack.

The advantage here is two fold:
1. If you don't already have CI/CD tooling in place, you now have two supported options "out of the box" with OpenShift.
2. The configuration of these tools can be distilled into Kubernetes resources and versioned in git.

The benefits for both the tools and approach are easy access to extremely well adopted (and portable) CI/CD tooling, and the ability to re-create your CI/CD stack in minutes, or move/copy it to another cluster.

**Demo:**

* Deploy a standard CI/CD stack for Java (Jenkins, Nexus, SonarQube) with one command.
    * `oc apply -k 01-cicd-with-app/overlays/cicd` (the `-k` is for *Kustomize*)
* Build and deploy the Pet Clinic application with the tools that were just deployed.
    * `oc apply -k 01-cicd-with-app/overlays/app` (create the application projets/environments)
    * In the `cicd` project, find the `petclinic-jenkins-pipeline` build and start it.

**Demo:** The same process, app, and tools, but using OpenShift Pipelines instead of Jenkins.

* Create the Tekton pipeline and resources:
    * `oc apply -k 02-tekton/tekton`
* Start the pipeline:
    * `oc create 02-tekton/pipelinerun/run-build.yaml -n cicd`

#### Operators: Deploy, Manage and Update Complex Software

The concept of [Kubernetes Operators](https://coreos.com/operators/), and the [Operator Framework](https://operatorframework.io/) were originally created by CoreOS.  CoreOS is now a part of Red Hat!

The idea behind Operators is to distill the knowledge of a "human operator" of a particular system and codify this into a Kubernetes-native application.  Operators are not specific to OpenShift, although OpenShift has embraced Operators more intimately than any other Kubernetes distribution, even including an embedded version of [OperatorHub](https://operatorhub.io) in the Adminstration console!

Operators provide the distinct advantage of abstracing the complexity of managing complex software in your Kubernetes clusters.

The benefit is I higher degree of standardization, less configuration "drift" between environments, and best practices and processes or installing/managing a system built-in by default.

**Demo:** 

* Deploy a highly-available [Apache Kafka](https://kafka.apache.org/) cluster using the AMQ Streams Operator.
* Create new topics using Kubernetes Custom Resource.
* Deploy a simple application to produce/consume topics on this Kafka cluster.

#### Serverless: Unlocking Serverless Potential Without Cloud Vendor Lock-in!

OpenShift Container Platform includes full support for both [Knative Serving](https://knative.dev/docs/serving/) and [Knative Eventing](https://knative.dev/docs/eventing/).  This allows developer to create *scale-to-zero* serverless functions in a standard and language-agnostic manner.  Eventing provides the underlying framwork and tooling to create *Event Driven Architectures* that can be developed and deployed the same way, regardless of the data centre or cloud you are using.

The advantage of using OpenShift Serverless is that you get a *fully tested and supported* implementation of Knative Eventing and Serving that works the same way no matter where you are running OpenShift.

This offers a huge benefit in the ability to easily install and manage the Serverless framework that is cloud agnostic with an Operator. 

**Demo:**

* Deploy OpenShift Serverless
* Deploy a simple Serverless application

#### Standard Development Environment Made Easy

Another core component of OpenShift Container Platform is [CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview).

*CodeReady Workspaces* gives developers full workspaces that run on your OpenShift cluster, accessed through a modern browser such as Chrome or FireFox.  The UI is the same "Theia" UI developers who use *VS Code* are used to, and it can even use most of the same plugins!

Each workspace can be tailored to suit each individual project.  In fact, the configuration of a workspace is encoded in a `devfile` that is included in your git repositry.  This means that a developer can be up and running on a project in a minute or two, using the same tooling (including tool/runtime versions), commands, and plugins as the rest of the team.

This has obvious benefits over traditional IDEs that run on a desktop that can easily have configuration drift from machine to machine, or bulky VDIs that require a developer to use a full VM to have a standard development environment.

**Demo:** Explore CodeReady Workspaces