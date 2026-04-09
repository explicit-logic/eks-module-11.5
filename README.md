# Module 11 - Kubernetes on AWS - EKS

This repository contains a demo project created as part of my **DevOps studies** in the [TechWorld with Nana – DevOps Bootcamp](https://www.techworld-with-nana.com/devops-bootcamp).

**Demo Project:** CD - Deploy to DigitalOcean Kubernetes cluster from Jenkins Pipeline

**Technologies used:** Kubernetes, Jenkins, DigitalOcean Kubernetes, Docker, Linux

**Project Description:**

- Create K8s cluster on DigitalOcean
- Install `kubectl` as Jenkins Plugin
- Adjust `Jenkinsfile` to use Plugin and deploy to DigitalOcean Kubernetes cluster

---

### Overview

- Kubectl command line tool available inside Jenkins container

- Install Kubernetes CLI Jenkins plugin - Execute kubectl with kubeconfig credentials

- Configure `Jenkinsfile` to deploy to LKE cluster

---

### Create K8s cluster on DigitalOcean

- Create a minimal kubernetes cluster with 1 node

![](./images/create-cluster.png)

- Download kubeconfig file and point to it

```sh
export KUBECONFIG=k8s-kubeconfig.yaml
```

### Configure a Multibranch Pipeline in Jenkins

1. Go to **Dashboard** → **New Item**
2. Name it `k8s-digital-ocean`, select **Multibranch Pipeline**, click **OK**

**Branch Sources:**

Click **Add source** → **GitHub** and fill in:

| Field | Value |
|---|---|
| Credentials | `github` |
| Repository HTTPS URL | `https://github.com/explicit-logic/eks-module-11.5` |

Click **Validate** to confirm access.

**Behaviors** — click **Add** and enable:
- `Discover branches`

**Build Configuration:**
- Script Path: `Jenkinsfile`

3. Click **Save** — Jenkins will scan the repository and create a job for each branch.


#### Store credentials in Jenkins

Go to `k8s-digital-ocean` → **Credentials** → **Global** → **Add Credentials**

Add **Secret file** credentials:

File: upload a kubeconfig file of your digitalocean k8s cluster

ID: `digitalocean`

![](./images/add-secret-file.png)

### Install Kubernetes CLI Jenkins plugin

- Navigate to `Manage Jenkins` -> `Plugins` -> `Available plugins`

- search for `Kubernetes CLI`

- Install

![](./images/kubernetes-cli-plugin.png)

- Restart Jenkins to complete installation

> SSH to jenkins server and start jenkins container again


### Configure `Jenkinsfile` to deploy to LKE cluster

Get `serverUrl` from your `kubeconfig` file clusters -> cluster -> server

Add the following to Jenkis file

```groovy
stage('deploy') {
    steps {
        script {
            echo "deploying docker image..."
            withKubeConfig([credentialsId: 'digitalocean', serverUrl: params.SERVER_URL]) {
                sh 'kubectl create deployment nginx-deployment --image=nginx'
            }
        }
    }
}
```

