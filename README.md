# google-virtual-machine 

Basic assumption is that you are using **ubuntu**.

## INSTALLL 

**git**

```bash 
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt update && sudo apt-get -y install git git-lfs && git lfs install

git config --global user.email "jehoon.song@net-targets.com"
git config --global user.name "Je-Hoon Song"

git config credential.helper 'cache'
```

**cuda**
```bash
# Install NVIDIA drivers
# sudo apt-get update && sudo add-apt-repository ppa:graphics-drivers/ppa -y && \
# sudo apt-get update && sudo apt-get install nvidia

# Check for CUDA and try to install.
# https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.0/base/Dockerfile
sudo apt-get update && sudo apt-get install -y --no-install-recommends ca-certificates apt-transport-https gnupg-curl && sudo rm -rf /var/lib/apt/lists/* && \
NVIDIA_GPGKEY_SUM=d1be581509378368edeec8c1eb2958702feedf3bc3d17011adbf24efacce4ab5 && \
NVIDIA_GPGKEY_FPR=ae09fe4bbd223a84b2ccfce3f60f4b3d7fa2af80 && \
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub && \
sudo apt-key adv --export --no-emit-version -a $NVIDIA_GPGKEY_FPR | tail -n +5 > cudasign.pub && \
echo "$NVIDIA_GPGKEY_SUM  cudasign.pub" | sha256sum -c --strict - && sudo rm cudasign.pub && \
sudo sh -c 'echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/cuda.list' && \
sudo sh -c 'echo "deb https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list'

export CUDA_VERSION=9.0.176
export CUDA_PKG_VERSION=9-0=$CUDA_VERSION-1

sudo apt-get update && apt-get install -y --no-install-recommends \
        cuda-cudart-$CUDA_PKG_VERSION

curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_${CUDA_VERSION}-1_amd64.deb
sudo dpkg -i ./cuda-repo-ubuntu1604_${CUDA_VERSION}-1_amd64.deb
sudo apt-get update && sudo apt-get install -y --no-install-recommends cuda=${CUDA_VERSION}-1
sudo rm ./cuda-repo-ubuntu1604_${CUDA_VERSION}-1_amd64.deb

ln -s cuda-9.0 /usr/local/cuda

sudo sh -c 'echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf'
sudo sh -c 'echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf'

export PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/nvidia/lib:/usr/local/nvidia/lib64

# NCCL install
# https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.0/runtime/Dockerfile
export NCCL_VERSION=2.2.13

sudo apt-get update && sudo apt-get install -y --no-install-recommends \
        cuda-libraries-$CUDA_PKG_VERSION \
        cuda-cublas-9-0=9.0.176.3-1 \
        libnccl2=$NCCL_VERSION-1+cuda9.0


# CUDNN install
# https://gitlab.com/nvidia/cuda/blob/ubuntu16.04/9.0/runtime/cudnn7/Dockerfile
export CUDNN_VERSION=7.1.4.18

sudo apt-get update && sudo apt-get install -y --no-install-recommends \
        libcudnn7=$CUDNN_VERSION-1+cuda9.0

sudo rm -rf /var/lib/apt/lists/*
```

**docker**

```bash
#sudo apt update
#sudo apt install --yes apt-transport-https ca-certificates curl gnupg2 software-properties-common
#sudo apt install -y docker.io
#sudo usermod -aG docker $USER
#
# logout and then re-login
#logout 
```

**docker**
```bash
# Install docker-ce
# https://docs.docker.com/install/linux/docker-ce/ubuntu/
sudo apt-get update && sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository -y \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update && sudo apt-get install -y docker-ce

# Install nvidia-docker
# https://github.com/NVIDIA/nvidia-docker/README.md
# Add the package repositories
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update

# Install nvidia-docker2 and reload the Docker daemon configuration
sudo apt-get install -y nvidia-docker2
sudo pkill -SIGHUP dockerd

# Manage Docker as a non-root user
# https://docs.docker.com/install/linux/linux-postinstall/
sudo groupadd docker
sudo usermod -aG docker $USER
# it will work after re-login

apt-get update && \
        apt-get clean && \
        sudo rm -rf /var/lib/apt/lists/*

# Test
sudo docker run --rm nvidia/cuda nvidia-smi
```

**containers for CUDA**

You can choose various version of cuda for your specific project. 

```bash
git clone https://gitlab.com/nvidia/container-images/cuda.git
```

```bash
export IMAGE_NAME="nvidia/cuda"
export CUDA_VERSION="9.0"
export OS="ubuntu16.04"

docker build -t "${IMAGE_NAME}:${CUDA_VERSION}-base-${OS}" "dist/${OS}/${CUDA_VERSION}/base"
docker build -t "${IMAGE_NAME}:${CUDA_VERSION}-runtime-${OS}" --build-arg "IMAGE_NAME=${IMAGE_NAME}" "dist/${OS}/${CUDA_VERSION}/runtime"
docker build -t "${IMAGE_NAME}:${CUDA_VERSION}-devel-${OS}" --build-arg "IMAGE_NAME=${IMAGE_NAME}" "dist/${OS}/${CUDA_VERSION}/devel"
```

```bash
docker run --runtime=nvidia nvidia/cuda:10.0-base nvidia-smi

# If you have 4 GPUs, to isolate GPUs 3 and 4 (/dev/nvidia2 and /dev/nvidia3)
docker run --runtime=nvidia -e NVIDIA_VISIBLE_DEVICES=2,3 nvidia/cuda:10.0-base nvidia-smi
```
