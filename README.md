# vulkan_docker_test
Simple docker image for running a Vulkan application in a docker container.

To run this example `cd` into the repo and run the following command:
```
xhost +local:docker && \
docker compose up --build
```

After that you should see `vkcube` popup along with info about the GPU printed to console.

If you have mutiple gpu's being passed into the container vkcube requires you to specify which one you wish to use if the first device is not attached to the display.
[vkcube with VK_KHR_display fails when acquirable displays are on not-the-first physical devices](https://github.com/KhronosGroup/Vulkan-Tools/issues/308)
```
vkcube --gpu_number 1
```
