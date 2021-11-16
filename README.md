# spacy_transformer_env
Environment for working with spacy transformer based models

# Setup

1. Get nvidia-drivers installed
2. Check you can run `nvidia-smi` 
3. Follow: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html
4. Restart!
5. Check you can run `sudo docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi`#
6. Make a note of the nvidia driver version on the host.

# Driver Versions
Different minimum driver versions are required

I am developing against 450.51.06 in my prod host and 470.82.00 on my nvidia-docker development machine.

For cuda 11.x >= 450.80.02 is required, so greater than the version available in prod.

As such, I think I want to build cuda 10.x images.

# Set up GPU Jupyter
GPU Jupyter from https://github.com/iot-salzburg/gpu-jupyter.git uses the jupyter-stacks repositories dockerfiles to write a new dockefile with a GPU enabled base image.

## Generate a Dockerfile
 Build a `gpu-jupyter` 
```sh
git clone https://github.com/iot-salzburg/gpu-jupyter.git
cd gpu-jupyter
git checkout v1.4_cuda-10.1_ubuntu-18.04  # select the desired (CUDA)-version
# generate a Dockerfile with python and without Julia and R
./generate-Dockerfile.sh --python-only   # generate the Dockerfile with only a python interpreter
```

## Build
```
docker build -t gpu-jupyter .build/  # will take a while
```

## Test Run
```sh
docker run \
 --gpus all -d -it \
 -p 8848:8888 \
 -v $(pwd)/data:/home/jovyan/work \
 -e GRANT_SUDO=yes \
 -e JUPYTER_ENABLE_LAB=yes \
 -e NB_UID="$(id -u)" \
 -e NB_GID="$(id -g)" \
 --user root \
 --restart always \
 --name gpu-jupyter_1 \
 gpu-jupyter
```
