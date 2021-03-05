# SAME-Core

# Transportable K3S AI

## Project Overview

Today, Data Scientists wanting to focus on what they do best are forced to deal with many inconveniences when it comes to setting up, stabilizing and sharing their working environments. This makes it challenging to easy organize, configure, transport and share their work.

Fortunately, Terraform (a technology to enable simplification of developer infrastructure) offers solutions to help streamline these challenges. This project seeks to leverage Terraform to create a standardized way to deploy a Kubernetes environment such that Kubeflow (AI training on Kubernetes) or any other Kubernetes based ML workloads can be deployed declaratively to any environment (local, on a VM or in *any* cloud) (NOTE: Actual deployment of that workload is out of scope for this project - it will be handled elsewhere). Providing this consistent, declarative setup system will unleash the productivity of Data Scientists and their teams. 

## Ultimate Vision

**“With a single command, a Data Scientist can set up, provision and run a reproducible, reusable ML development environment.”**

## Basic Design Considerations
# User experience
Our goal is to have a user experience that looks like this:
```
# Local experience
# Install same CLI & configure K8s cluster (as easy as pip install foobaz)
$ export SAME_ENVIRONMENT=local
$ curl http://projectsame.org/install.sh | bash
```
The above:
- Downloads and installs a CLI tool
- Installs a local kubernetes API (e.g., via k3s/k3d)
- Installs Kubeflow into that cluster
- Provides an SAME endpoint inside the Kubernetes cluster that can respond to `same` commands

```
# Create a program in Kubernetes and populates it with the defaults generated
# by the original author. Also uploads history exported by the original author
$ same create program -f https://github.com/uoftoronto/papers/arXiv.5778v1:1.0
```
The above:
- Creates all necessary services inside the Kubernetes cluster (same experience on local and/or hosted)
- Provisions resources necessary (e.g. Premium disk, GPU notes, etc) OR clearly alerts the user that the expected provisioning is not possible
- Pushes all metadata into the chosen metadata store for future comparison 

```
# Execute a pipeline with new parameters defined by the original author
$ same run program arXiv.1303.5778v1:1.0 --epochs=1000
```
The above: 
- Executes the program previously uploaded with the override parameter of 'epochs=100'
- Remainder of the execution occurs as expectedwith the defaults
- Is _usually_ driven off a git like system
- Pushes results into expected metadata store

```
# Export the entire pipeline and history to a single file. Results in file 
# arXiv.1303.5778v1:1.1.tgz.
$ same export program -n arXiv.1303.5778v1:1.1
```
The above:
- Includes all necessary services and infrastructure descriptions to recreate the system anywhere
- Also offers a way to select metadata to export and include
- Does not NECESSARILY include data, but could include pointers to the data


With all this, we think we can make a breakthrough in the way that machine learning is reproduced across multiple environments.

## Technical Objectives
By setting up this consistent API
Anyone can run KubeFlow on any cloud or at the edge via consistent, simple provisioning of a k8s API (either via k3s or hosted k8s)
Run KubeFlow locally, on a laptop or workstation, dedicated training rig - as easily as ‘pip install jupyter’
Seamlessly transport AI training from edge device to cloud as needed

However setup of platforms on top of the k8s API is another project’s scope.

## Project Roadmap
- Define and document project scope & deliverables, key personnel and objectives 
- Set up and refine a shared GitHub repo (project will be separate from Kubeflow)
- Create Terraform configurations to support basic launch configurations:

**P0:**
K3S/K3D for the local experience
K3S (VM)
plugins:
- Kubeflow pipelines (Argo and Tekton engine)
- Katib

**P1 - extend the same code to support standardized deployments of..** 
- AKS (Azure) 
- GKS (GCP) 
- EKS (AWS) 

## Near Term Gaps (The purpose of this document)
- Collection of terraform (one per provider) scripts for setting up:
- A VM on the target clouds 
- K3s on those VMs
- Doing both in such a way that maximizes shared code and parameterization via environment settings
- Testing using the Porter CNAB to deploy KF to those deployments (proof of concept from Bernd Verst and Ralph Squillace (porter install -c kubeflow --tag ghcr.io/squillace/aks-kubeflow:v0.3.1 --param AZURE_RESOURCE_GROUP=winlin --param CLUSTER_NAME=kubeflow`)

## Nice to have: 
- Common pattern for TF scripts for setting up K8s on the hosted provider
 (these exist but are often in very different formats/styles)
- if we could fork and centralize and share code (with a similar pattern as the k3s work), that would be hugely valuable.

## Target Local,Cloud & Owners
- Google Cloud Platform - Gabe Weiss 
- Amazon Web Services - Jeremy Wallace (?)
- Microsoft Azure - Paul DeCarlo
- Nuno De Carmo (https://twitter.com/nunixtech)
- Ralph Squillace  (https://twitter.com/ralph_squillace) 
- Alessandro Festa (https://twitter.com/bringyourownai)
- Gabriele Santomaggio (https://twitter.com/GSantomaggio)
- NVIDIA - Rex St. John

## Supported Environments
- K3S Local
- K3S (VM)
- AKS (Azure)
- GKS (GCP)
- EKS (AWS)

## Need Help With…
- VM setup via TF
- What do we need to do about additional resources - nothing?
...

## Link: 
- https://github.com/kubernauts/bonsai
- https://twitter.com/ralph_squillace/status/1360646969258090499

## Job to be done:
I am a data scientist who may or may not have access to provision a full cluster. I want to set up Kubeflow in a reliable way and so to get there, I’m either going to declaratively set up a hosted K8s cluster on the provider of my choice or set up a k3s “cluster) on a single beefy VM or my laptop - this consistently provided k8s API will let me deploy Kubeflow (OUT OF SCOPE FOR THIS WORK) and get going in a production ready way quickly without understanding any of the details for Kubeflow or k8s

## Our theory -
- Make it easy to run Kubernetes (K3S preferred or K8s) on 5+ infrastructure platforms via Terraform
- Then install KubeFlow to that
- Then party on ML (as a Data Scientist who doesn't want to do setting up all the things)

By supporting k3s as easily as hosted k8s, we make swapping out from k3s to k8s a single environment setting.
By separating the terraform installation of a Kubernetes API from the kubeflow installation, it enables us to move to multi cloud Reproducibility much easier.
