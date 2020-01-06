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
