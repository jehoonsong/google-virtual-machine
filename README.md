# google-virtual-machine 

Basic assumption is that you are using **ubuntu**.

## INSTALLL 

**git**

```
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt update && sudo apt-get -y install git git-lfs && git lfs install

git config --global user.email "jehoon.song@net-targets.com"
git config --global user.name "Je-Hoon Song"

git config credential.helper 'cache'
```

**docker**

```
sudo apt update
sudo apt install --yes apt-transport-https ca-certificates curl gnupg2 software-properties-common
sudo apt install -y docker.io
sudo usermod -aG docker $USER

# logout and then re-login
logout 
```

**cuda**

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
