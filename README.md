# MCP System PoC

This document provides instructions on how to set up the environment for the MCP System PoC.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [uv](https://docs.astral.sh/uv/getting-started/installation/)

## Installation

### Minikube

1.  Install minikube by following the official documentation: [https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)
2.  Start minikube:

    ```bash
    minikube start
    ```

### uv

Install uv by following the official documentation: [https://docs.astral.sh/uv/getting-started/installation/](https://docs.astral.sh/uv/getting-started/installation/)

### AWX Operator

Follow the official AWX Operator installation guide: [https://ansible.readthedocs.io/projects/awx-operator/en/latest/installation/basic-install.html](https://ansible.readthedocs.io/projects/awx-operator/en/latest/installation/basic-install.html)

A summary of the steps is provided below:

1.  Clone the `awx-operator` repository and check out the latest tag:

    ```bash
    git clone https://github.com/ansible/awx-operator.git
    cd awx-operator
    git checkout $(git describe --tags --abbrev=0)
    ```

2.  Deploy the operator using `make` or through `kustomization.yaml`:

    1. `make`
    ```bash
    make deploy
    ```

    2. `kustomization.yaml`
    ```yaml
    ---
    apiVersion: kustomize.config.k8s.io/v1beta1
    kind: Kustomization
    resources:
    # Find the latest tag here: https://github.com/ansible/awx-operator/releases
        - github.com/ansible/awx-operator/config/default?ref=<tag>

    # Set the image tags to match the git version from above
    images:
        - name: quay.io/ansible/awx-operator
          newTag: <tag>

    # Specify a custom namespace in which to install AWX
    namespace: awx
    ```
    ```bash
    kubectl apply -k .
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

## MCP server configuration

1. The MCP server can be connected to various MCP clients. The PoC was tested with Gemini CLI, Claude Desktop and LM Studio. They can be installed using the following resources:

- https://github.com/google-gemini/gemini-cli
- https://www.claude.com/download
- https://lmstudio.ai/

2. The MCP server configuration has to be done inside the clients settings JSON file. Example for Claude Desktop: https://modelcontextprotocol.io/docs/develop/build-server

> **WARNING**: You may need to put the full path to the ```uv``` executable in the command field. You can get this by running ```which uv``` on macOS/Linux or ```where uv``` on Windows.

> **Info**: Make sure you pass in the absolute path to your server. You can get this by running ```pwd``` on macOS/Linux or ```cd``` on Windows Command Prompt. On Windows, remember to use double backslashes (```\\```) or forward slashes (```/```) in the JSON path.

- macOS / Linux (```~/.config/Claude/claude_desktop_config.json```)
```json
{
  "mcpServers": {
    "Tomcat server maintainer": {
      "command": "uv",
      "args": [
        "--directory",
        "/ABSOLUTE/PATH/TO/PARENT/FOLDER/src/poc",
        "run",
        "main.py"
      ]
    }
  }
}
```
- Windows (```%APPDATA%\Claude\claude_desktop_config.json```)
```json
{
  "mcpServers": {
    "Tomcat server maintainer": {
      "command": "uv",
      "args": [
        "--directory",
        "C:\\ABSOLUTE\\PATH\\TO\\PARENT\\FOLDER\\src\\poc",
        "run",
        "main.py"
      ]
    }
  }
}
```

