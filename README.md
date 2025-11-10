# MCP System PoC

This document provides instructions on how to set up the environment for the MCP System PoC.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Installation

### Minikube

1.  Install minikube by following the official documentation: [https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)
2.  Start minikube:

    ```bash
    minikube start
    ```

### AWX Operator

Follow the official AWX Operator installation guide: [https://ansible.readthedocs.io/projects/awx-operator/en/latest/installation/basic-install.html](https://ansible.readthedocs.io/projects/awx-operator/en/latest/installation/basic-install.html)

A summary of the steps is provided below:

1.  Clone the `awx-operator` repository and check out the latest tag:

    ```bash
    git clone https://github.com/ansible/awx-operator.git
    cd awx-operator
    git checkout $(git describe --tags --abbrev=0)
    ```

2.  Deploy the operator:

    ```bash
    make deploy
    ```

3.  Create an AWX instance. Create a file named `awx-demo.yml` with the following content:

    ```yaml
    ---
    apiVersion: awx.ansible.com/v1beta1
    kind: AWX
    metadata:
      name: awx-demo
    spec:
      service_type: nodeport
      nodeport_port: 30080
    ```

4.  Apply the `awx-demo.yml` to create the AWX instance:

    ```bash
    kubectl apply -f awx-demo.yml
    ```

5.  Retrieve the admin password:

    ```bash
    kubectl get secret awx-demo-admin-password -o jsonpath="{.data.password}" | base64 --decode
    ```

## Running the application

To run the application, you can use the `docker-compose.yml` file located in the `docker` directory.

1.  Navigate to the `docker` directory:

    ```bash
    cd ../docker
    ```

2.  Run the following command to start the containers:

    ```bash
    docker-compose up -d
    ```

## SSH into the application

To connect to the applications through SSH you have to additionaly provide the port the application is running on (currently: 2221, 2222 or 2223). The root password is located inside the Dockerfile.

```bash
ssh -p 2223 root@localhost
```
