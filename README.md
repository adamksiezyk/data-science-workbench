# Data Science Workbench
<p align="center">
  <img src="https://github.com/adamksiezyk/data-science-workbench/blob/main/assets/ds-workbench.svg?raw=true" alt="Logo"/>
</p>

Modern Data Science Workbench with LLM server.

## Prerequisites

This project requires Docker and Docker Compose to be installed. Additionally, you have to download an LLM,
place it in the `./data/llms` directory and edit the `LLM_NAME` variable in the `.env` file.

## Run

Run the project in background.

```bash
docker compose up -d
```

## Default configuration

### Ports

By default the services run at following ports:
- MLflow - 5000
- Jupyter Lab - 8888
- LlamaCpp server - 8080

You can change them by modifying the variables in `.env`:

```
LLM_PORT=8080
MLFLOW_SERVER_PORT=5000
JUPYTER_PORT=8888
```

### Jupyter user

To allow a seamingles cooperation between the docker environment and your file system, the Jupyter user is
defined as your host user. Update these variables in the `.env file`:

```
JUPYTER_USER=jovyan
JUPYTER_UID=1000
JUPYTER_GID=1000
```

### Cuda

By default the LLM server is built for CUDA 11.7.1. To change that run:

```bash
docker compose build --build-arg CUDA_VERSION=<CUDA-VERSION> llamacpp
```

The Jupyter server also supports CUDA, so you can run Machine Learning models on it using your favorite framework.
By default it is PyTorch with CUDA 11.7.1. To change it, modify the `docker-compose.yml` file.
You can find example images [here](https://quay.io/organization/jupyter).

### LLM

The LLM file has to be located in `./data/llms`. These variables control its behavior:

```
# model name, has to match with `./data/llms`
LLM_NAME=mistral-7b-instruct-v0.2.Q4_K_M.gguf
# number of LLM layers offloaded into the GPU
# variable for different models
# the 7B model has 33 layers, 24 layers fit into a 4GB GPU
LLM_GPU_LAYERS=24
# context size, for Llama-based models max 2048
LLM_CONTEXT_SIZE=1024
```

## Connect to services

The services are accessible through web interfaces.

### MLflow

Go to http://localhost:5000 to open the Web UI. Connect Python SDK when running experiments on host machine:

```python
import mlflow

mlflow.set_tracking_uri("http://localhost:5000")
```

Jupyter Lab is preconfigured to connect to the above URI. Therefore, it does not need to be set.

### Jupyter Lab

In order to connect to Jupyter Lab, you need to find the connection token in docker compose logs:

```
docker compose logs -f jupyter

ds_workbench-jupyter-1  | [I 2024-03-08 18:33:55.349 ServerApp] Jupyter Server 2.12.5 is running at:
ds_workbench-jupyter-1  | [I 2024-03-08 18:33:55.349 ServerApp] http://facfb0c96c8a:8888/lab?token=a201ddfd294f76eac3e44037fba17bb44c0713e01c86b095
ds_workbench-jupyter-1  | [I 2024-03-08 18:33:55.349 ServerApp]     http://127.0.0.1:8888/lab?token=a201ddfd294f76eac3e44037fba17bb44c0713e01c86b095
ds_workbench-jupyter-1  | [I 2024-03-08 18:33:55.349 ServerApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
ds_workbench-jupyter-1  | [C 2024-03-08 18:33:55.351 ServerApp]
ds_workbench-jupyter-1  |
ds_workbench-jupyter-1  |     To access the server, open this file in a browser:
ds_workbench-jupyter-1  |         file:///home/adam/.local/share/jupyter/runtime/jpserver-59-open.html
ds_workbench-jupyter-1  |     Or copy and paste one of these URLs:
ds_workbench-jupyter-1  |         http://facfb0c96c8a:8888/lab?token=a201ddfd294f76eac3e44037fba17bb44c0713e01c86b095
ds_workbench-jupyter-1  |         http://127.0.0.1:8888/lab?token=a201ddfd294f76eac3e44037fba17bb44c0713e01c86b095
```

### LlamaCpp

LlamaCpp offers a REST API (see the [docs](https://github.com/ggerganov/llama.cpp/tree/master/examples/server#api-endpoints)).
You can also interact with the LLM through a Web UI under http://localhost:8080.
