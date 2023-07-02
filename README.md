# vulkan_docker_test
Simple docker image for running a Vulkan application in a docker container.

To run this example `cd` into the repo and run the following command:
```
xhost + && \
docker compose up --build
```

After that you should see `vkcube` popup along with info about the GPU printed to console.
There seems to be some issues with running Vulkan in a container using the [Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/overview.html). They claim to support Vulkan but there might be some bugs.
> All graphics APIs are supported, including OpenGL, Vulkan, OpenCL, CUDA and NVENC/NVDEC.

For this test I'm running Ubuntu 22.04 on my host as well as in the container `FROM ubuntu:22.04`. For this test I'm seeing that the only device `vulkaninfo` is finding is `llvmpipe` which is a CPU based graphics driver. I'm also seeing that llvmpipe can't render when running `vkcube` both in the container and on the host for Ubuntu 22.04. Here is the container output for `vkcube`:
```
Selected GPU 0: llvmpipe (LLVM 13.0.1, 256 bits), type: 4
Could not find both graphics and present queues
```

On my host I can tell it to use `llvmpipe`:
```
vkcube --gpu_number 1
Selected GPU 1: llvmpipe (LLVM 13.0.1, 256 bits), type: Cpu
Could not find both graphics and present queues
```
As you can see they have the same error. What's interesting is if I swap the container to `FROM ubuntu:20.04` then `llvmpipe` can render but this is moot since I do not wish to do CPU rendering. The main issue here is that Vulkan is unable to detect my Nvidia GPU from within the container when using the [Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/overview.html) with `NVIDIA_DRIVER_CAPABILITIES=all` and `NVIDIA_VISIBLE_DEVICES=all`. I've also tried using [nvidia/vulkan](https://gitlab.com/nvidia/container-images/vulkan). When running `vulkaninfo` in this container I get:
```
vulkaninfo
ERROR: [Loader Message] Code 0 : vkCreateInstance: Found no drivers!
Cannot create Vulkan instance.
This problem is often caused by a faulty installation of the Vulkan driver or attempting to use a GPU that does not support Vulkan.
ERROR at /vulkan-sdk/1.3.236.0/source/Vulkan-Tools/vulkaninfo/vulkaninfo.h:674:vkCreateInstance failed with ERROR_INCOMPATIBLE_DRIVER
```
I'm suspecting this has to to with me running Ubuntu 22.04 on the host although the whole point of docker is the host OS generally should not affect the container.
