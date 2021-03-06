# This docker file is inherented from NVIDIA Digits docker file https://gitlab.com/nvidia/digits/blob/master/6.0/Dockerfile
# Base Image: amax/general:8.0-devel-cudnn7.0_ubuntu16.04_v01
# Image Content:
#	Digits 6.0.1 (Caffe 0.15.14, torch 0.10.7-1, tensorflow 1.2.1) 


### Build protobuf module
FROM ubuntu:16.04 as protobuf

RUN apt-get update && apt-get install -y --no-install-recommends \
        autoconf \
        automake \
        ca-certificates \
        curl \
        g++ \
        git \
        libtool \
        make \
        python-dev \
        python-setuptools \
        unzip && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /protobuf
RUN git clone -b '3.2.x' https://github.com/google/protobuf.git . && \
    ./autogen.sh && \
    ./configure --prefix=/usr/local/protobuf && \
    make "-j$(nproc)" install


### Build caffe (0.15)a caffe 0.16 also avaliable now
FROM amax/general:8.0-devel-cudnn7.0_ubuntu16.04_v01 as caffe

COPY --from=protobuf /usr/local/protobuf /usr/local

RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates \
        cmake \
        curl \
        g++ \
        git \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libnccl-dev=1.2.3-1+cuda8.0 \
        libopencv-dev \
        libsnappy-dev \
        python-all-dev \
        python-h5py \
        python-matplotlib \
        python-opencv \
        python-pil \
        python-pydot \
        python-scipy \
        python-skimage \
        python-sklearn && \
    rm -rf /var/lib/apt/lists/*

# Build pip
RUN curl -O https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    pip install --upgrade --no-cache-dir pip

# Build caffe with nccl and cudnn support
RUN git clone https://github.com/nvidia/caffe.git /caffe -b 'caffe-0.15' && \
    cd /caffe && \
    pip install ipython==5.4.1 && \
    pip install -r python/requirements.txt && \
    mkdir build && \
    cd build && \
    cmake -DCMAKE_INSTALL_PREFIX=/usr/local/caffe -DUSE_NCCL=ON -DUSE_CUDNN=ON -DCUDA_ARCH_NAME=Manual -DCUDA_ARCH_BIN="35 52 60 61" -DCUDA_ARCH_PTX="61" .. && \
    make -j"$(nproc)" install && \
    rm -rf /caffe


### Build digts 6 image baased on cuad 8 w/development toolkits and cudnn 7 under ubuntu 16.04
FROM amax/general:8.0-devel-cudnn7.0_ubuntu16.04_v01

LABEL maintainer "AMAX Information Technologies Inc <support@amax.com>"

ENV DIGITS_VERSION 6.0

LABEL com.nvidia.digits.version="6.0"

COPY --from=caffe /usr/local/caffe /usr/local
COPY --from=protobuf /usr/local/protobuf /usr/local

# Install the packages to get pip installed or else we run into numpy problems
RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        python && \
    rm -rf /var/lib/apt/lists/*

# Build pip, need to do this before DIGITS packages or else we get numpy problems
RUN curl -O https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    pip install --upgrade --no-cache-dir pip

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        graphviz \
        gunicorn \
        libatlas3-base \
        libboost-all-dev \
        libfreetype6-dev \
        libgoogle-glog0v5 \
        libhdf5-serial-dev \
        libleveldb1v5 \
        libnccl1=1.2.3-1+cuda8.0 \
        libopencv-core2.4v5 \
        libopencv-highgui2.4v5 \
        libopencv-imgproc2.4v5 \
        libpng12-dev \
        libzmq5 \
        nginx \
        pkg-config \
        python-dev \
        python-flask \
        python-flaskext.socketio \
        python-flaskext.wtf \
        python-gevent \
        python-lmdb \
        python-opencv \
        python-pil \
        python-pydot \
        python-requests \
        python-six \
        python-skimage \
        python-tk \
        python-wtforms \
        rsync \
        software-properties-common \
        torch7-nv=0.10.7-1ubuntu16.04+cuda8.0 && \
    rm -rf /var/lib/apt/lists/* 

RUN pip install https://github.com/NVIDIA/DIGITS/archive/v6.0.1.tar.gz

RUN pip install --no-cache-dir \
        setuptools\>=18.5 \
        tensorflow-gpu==1.2.1 \
        protobuf==3.2.0

VOLUME /jobs

ENV DIGITS_JOBS_DIR=/jobs
ENV DIGITS_LOGFILE_FILENAME=/jobs/digits.log
ENV PYTHONPATH=/usr/local/python

# DIGITS
EXPOSE 5000

# TensorBoard
EXPOSE 6006

ENTRYPOINT ["python", "-m", "digits"]

