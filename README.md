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
#curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
#sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
#sudo apt update
#sudo apt install --yes docker-ce
sudo apt install -y docker.io
sudo usermod -aG docker $USER

# logout and then re-login
logout 
```
