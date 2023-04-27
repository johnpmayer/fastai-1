
# FastAI with CUDA on WSL Ubuntu

## Create the WSL VM

wsl --install -d Ubuntu-22.04

## Installing up CUDA

https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html

https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#pre-installation-actions

Note: PCI won't work, but nvidia-smi does

```
sudo lspci | grep nvidia # nothing
nvidia-smi # good

sudo apt install gcc

# If prompted
sudo apt upgrade
```

https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#wsl

```
sudo apt-key del 7fa2af80
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-keyring_1.0-1_all.deb
sudo dpkg -i cuda-keyring_1.0-1_all.deb
sudo apt-get update
```
   
### Ensure the right version of CUDA!!!

```
sudo apt install cuda-11-8
```

https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions

```
ls /usr/local/cuda-11.8/
ls /usr/local/cuda-11.8/bin
ls /usr/local/cuda-11.8/lib64
```

Add to .bashrc

```
# CUDA 11.8
export PATH=/usr/local/cuda-11.8/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

```
john@pelor:~$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2022 NVIDIA Corporation
Built on Wed_Sep_21_10:33:58_PDT_2022
Cuda compilation tools, release 11.8, V11.8.89
Build cuda_11.8.r11.8/compiler.31833905_0
```

Optional libraries

```
sudo apt-get install g++ freeglut3-dev build-essential libx11-dev \
    libxmu-dev libxi-dev libglu1-mesa-dev libfreeimage-dev libglfw3-dev
```

Show installation

```
john@pelor:~$ update-alternatives --display cuda
cuda - auto mode
  link best version is /usr/local/cuda-11.8
  link currently points to /usr/local/cuda-11.8
  link cuda is /usr/local/cuda
/usr/local/cuda-11.8 - priority 118
```

# Anaconda

Expected SHA256: d4c4256a8f46173b675dd6a62d12f566ed3487f932bab6bb7058f06c124bcc27

```
sudo apt-get update
sudo apt install curl -y
cd /tmp
curl --output anaconda.sh https://repo.anaconda.com/archive/Anaconda3-5.3.1-Linux-x86_64.sh
sha256sum anaconda.sh
bash anaconda.sh
cd
cat .bashrc
vi .bashrc
source .bashrc
nvcc --version
conda --version
```

# Conda Environment

```
conda create --name fastai-cuda-11.8 python=3
conda activate fastai-cuda-11.8
```

# CUDNN

https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html
https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#package-manager-ubuntu-install

```
export OS=ubuntu2204
wget https://developer.download.nvidia.com/compute/cuda/repos/${OS}/x86_64/cuda-${OS}.pin
sudo mv cuda-${OS}.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/${OS}/x86_64/3bf863cc.pub
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/${OS}/x86_64/ /"
sudo apt-get update
export cuda_version=cuda11.8
export cudnn_version=8.9.0.131
sudo apt-get install libcudnn8=${cudnn_version}-1+${cuda_version}
sudo apt-get install libcudnn8-dev=${cudnn_version}-1+${cuda_version}
```

https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#verify

Samples weren't installed, so ignoring this

# Manually install FastAI

Ensure I'm in the new conda Environment

```
pip install -Uqq fastai
```

# Verify Everything

```
(fastai-cuda-11.8) john@pelor:~$ python
Python 3.10.4 (main, Mar 31 2022, 08:41:55) [GCC 7.5.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from fastcore.all import *
>>> import time
>>> from fastdownload import download_url
>>> from fastai.vision.all import *
>>>
```

It works!

# Visual Studio Code

- WSL
- Jupyter
- Python

Open the notebook, and select the above "fastai-cuda-11.8" conda virtual environment.

Manually install the IPython Kernel for Jupyter (again, ensure in the new environment)

```
conda install ipykernel
```

# Success


Now, bird-not-bird.ipynb is working