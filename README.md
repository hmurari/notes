# Notes
Notes for various useful stuff


## Azure IoTEdge Commands
```bash
# List modules
iotedge list

# Logs for a module
iotedge logs moduleName

# Restart a module
iotedge restart moduleName

# System logs
iotedge system logs -- -f

# Stop/Restart entire iotedge system
iotedge system stop
iotedge system restart
```

## Docker commands
```bash
# Login into Dockerhub
docker login --username your_username --password your_password

# Login into ACR
docker login --username your_username --password your_password your-registry.azurecr.io

# Build & push docker image
docker build --rm -f Dockerfile -t dockerhub-uname/image-name:1.0.0 .
docker push dockerhub-uname/image-name:1.0.0 # dockerhub
docker push your-registry.azurecr.io/image-name:0.0.1  # ACR

# Run docker image (interactive)
docker run -it --privileged --gpus=all --ipc host --network host -p 554:554 nvidia/cuda:11.3.0-cudnn8-devel-ubuntu20.04 /bin/bash

# Docker shell on running container
docker exec -it nvidia/cuda:11.3.0-cudnn8-devel-ubuntu20.04 /bin/bash

```
