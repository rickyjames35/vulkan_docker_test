# vulkan_docker_test
Simple docker image for running a Vulkan application in a docker container.

To run this example `cd` into the repo and run the following command:
```
xhost + && \
docker compose up --build
```

After that you should see `vkcube` popup along with info about the GPU printed to console.
