ARG UBUNTU_VERSION=22.04
# This needs to generally match the container host's environment.
ARG CUDA_VERSION=11.7.1
# Target the CUDA build image
ARG BASE_CUDA_DEV_CONTAINER=nvidia/cuda:${CUDA_VERSION}-devel-ubuntu${UBUNTU_VERSION}
# Target the CUDA runtime image
ARG BASE_CUDA_RUN_CONTAINER=nvidia/cuda:${CUDA_VERSION}-runtime-ubuntu${UBUNTU_VERSION}

FROM ${BASE_CUDA_DEV_CONTAINER} as build

# Unless otherwise specified, we make a fat build.
ARG CUDA_DOCKER_ARCH=all
ARG LLAMACPP_RELEASE=b2350

RUN apt-get update && \
	apt-get install -y build-essential git

# Build llama.cpp project
RUN git clone https://github.com/ggerganov/llama.cpp --branch ${LLAMACPP_RELEASE} --depth 1

WORKDIR /llama.cpp

# Set nvcc architecture
ENV CUDA_DOCKER_ARCH=${CUDA_DOCKER_ARCH}
# Enable cuBLAS
ENV LLAMA_CUBLAS=1

RUN make

FROM ${BASE_CUDA_RUN_CONTAINER} as runtime

COPY --from=build /llama.cpp/server /server

ENTRYPOINT [ "/server" ]
