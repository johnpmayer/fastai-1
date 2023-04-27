# CUDA

Specifically, my setup is Windows 11, WSL, Ubuntu

Following <https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html>

My RTX 4080

Got <https://www.nvidia.com/content/DriverDownloads/confirmation.php?url=/XFree86/Linux-x86_64/525.116.03/NVIDIA-Linux-x86_64-525.116.03.run&lang=us&type=TITAN>

Copy the file into WSL, Run the shells script as root

It failed. Not the way

## Attempt 2

`conda install -c nvidia cuda-python`

## Attempt 3

Following all the stuff 

<https://github.com/pytorch/pytorch/issues/85773>
<https://github.com/microsoft/WSL/issues/5663#issuecomment-1068499676>

## Attempt 4

It's not a shared library thing, CUDNN is just separate

<https://github.com/pytorch/pytorch/issues/85773>
<https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html>

What a mess

``` shell
  282  mv Downloads/cudnn-local-repo-ubuntu2204-8.9.0.131_1.0-1_amd64.deb ~/
  283  cd
  284  ls
  285  sudo dpkg -i cudnn-local-repo-ubuntu2204-8.9.0.131_1.0-1_amd64.deb
  286  sudo cp /var/cudnn-local-repo-ubuntu2204-8.9.0.131/cudnn-local-D7522631-keyring.gpg /usr/share/keyrings/
  287  sudo apt-get update
  288  nvcc
  289  nvcc --version
  290  mv /mnt/c/Users/User/Downloads/cudnn-local-repo-ubuntu2204-8.9.0.131_1.0-1_amd64.deb .
  291  ls
  292  sudo dpkg -i cudnn-local-repo-ubuntu2204-8.9.0.131_1.0-1_amd64.deb
  293  sudo cp /var/cudnn-local-repo-ubuntu2204-8.9.0.131/cudnn-local-2063C34E-keyring.gpg /usr/share/keyrings/
  294  sudo apt-get update
  295  sudo apt-get install libcudnn8=8.9.0.131-1+cuda11.1
  296  nvcc --version
  297  sudo apt-get update
  298  sudo apt-get install libcudnn8=8.9.0.131-1+cuda11
  299  sudo apt-get install libcudnn8
  300  sudo apt-get install libcudnn8=8.9.0.131-1+cuda11.8
  301  sudo apt-get install libcudnn8-dev=8.9.0.131-1+cuda11.8
  302  sudo apt-get install libcudnn8-samples=8.9.0.131-1+cuda11.8
  303  history
```

## Attempt 5

Just use package managers

<https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#wsl>
<https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#network-repo-installation-for-wsl>

``` shell
sudo apt-get update
sudo apt-get install cuda
```

``` shell
(base) john@pelor:~$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2023 NVIDIA Corporation
Built on Mon_Apr__3_17:16:06_PDT_2023
Cuda compilation tools, release 12.1, V12.1.105
Build cuda_12.1.r12.1/compiler.32688072_0
```

Ok so far!

<https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html>

``` shell
export cuda_version=cuda12.1
export cudnn_version=8.9.0.131
sudo apt-get install libcudnn8=${cudnn_version}-1+${cuda_version}
sudo apt-get install libcudnn8-dev=${cudnn_version}-1+${cuda_version}
sudo apt-get install libcudnn8-samples=${cudnn_version}-1+${cuda_version}
```

Working?

<https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#verify>

I had to install freeimage.... but

``` shell
Test passed!
```

Christ.

## OK, still not working in python

Lastly, i blew up the conda environment and started from scratch

I did it again, this time creating the environment (from VS), and starting with

`conda install -c nvidia cuda-python`

NOPE

## Alright, simpler repro

```
(/home/john/fastai/fastai-1/.conda) john@pelor:~/fastai/fastai-1$ python
Python 3.10.10 | packaged by conda-forge | (main, Mar 24 2023, 20:08:06) [GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/john/fastai/fastai-1/.conda/lib/python3.10/site-packages/torch/__init__.py", line 229, in <module>
    from torch._C import *  # noqa: F403
ImportError: /home/john/fastai/fastai-1/.conda/lib/python3.10/site-packages/torch/lib/libc10_cuda.so: undefined symbol: cudaMemPoolSetAttribute, version libcudart.so.11.0
```

OK SICK! Looks like CUDA12.1 is not supported! HOLY SHIT!

<https://github.com/pytorch/pytorch/issues/91122>
<https://github.com/teddykoker/torchsort/issues/67>

## Attempt 6

I guess I'm going to reinstall CUDA. FUCK