# This confiration file is inheritancd from NVIDIA CUDA dockerfile
# Ref: https://gitlab.com/nvidia/cuda 
# This image is build on Ubuntu 14.04 with CUDA 8.0 Development Toolkit, and cuDNN 7.0.3.11
# Build command:
#	docker build . --build-arg repository=nvidia/cuda -t <tag_name>

ARG repository
FROM ${repository}:8.0-devel-ubuntu14.04
LABEL maintainer "AMAX INFORMATION TECHNOLOGIES INC <SUPPORT@AMAX.COM>"

RUN echo "deb http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1404/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list

ENV CUDNN_VERSION 7.0.3.11

LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
            libcudnn7=$CUDNN_VERSION-1+cuda8.0 \
            libcudnn7-dev=$CUDNN_VERSION-1+cuda8.0 && \
    rm -rf /var/lib/apt/lists/*
