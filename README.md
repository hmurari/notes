# Notes
Notes for various useful stuff

## Shell useful commands
```bash
# Search for a specific pattern of file recursively & delete it.
find . -name "*.pyc" -exec rm {} \;

# Search for something only in text files
find . -name "*.txt" -exec grep "hello world" {} \;  

# Seach for something in txt files, also print filename
grep -R "hello world" * | grep ".txt"  
```


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


## Git useful commands
```bash
# Set global config
git config --global -l # list config
git config --global user.name "First Last"
git config --global user.email "your-email@provider.com"
git config --global core.editor vim
git config --global push.default=current  # push current branch only by default
git config --global pull.default=current

# Clone
git clone https://github.com/hmurari/notes.git

# Make changes, check status, diff, stage, commit & push
git status
git diff
git checkout .        # discard all changes
git checkout file.txt # discard all changes in file.txt
git add .
git commit
git push         # current branch
git push --all   # all branches
git push --tags  # push tags

# Merge
git pull --set-upstream origin main  # pull origin's main to current main
git merge legacy main                # merge legacy branch into main
git commit                           # resolve conflicts & commit
git push                             # Push latest to remote

# Going back
git reset --hard HEAD    # go back to HEAD (discard staged changes)
git reset --hard HEAD~1  # go back one commit before HEAD
git reset --hard HEAD~2  # go back two commits before HEAD

# Tag (list, create, push)
git tag            # list tags
git tag -a v1.0.0  # tag the current state
git push --tags    # push tags to remote

# Add multiple remotes
git remote -v                                             # view remote connections
git remote add origin2 git@github.com:hmurari/notes.git   # Add a second remote
git push origin2 main                                     # Push main banch to second remote

# Add an empty folder to git
mkdir -p test-folder && touch test-folder/.gitkeep
git add .
git commit

# branch
git branch # show branches
git checkout -b new-branch  # start a new dev branch
git branch -m main  # change master branch name to main

# history
git log
git log --oneline
git reflog
git show <commit-uuid>

# remove a large file from git history
get bfg repo cleaner from https://rtyley.github.io/bfg-repo-cleaner/
bfg --strip-blobs-bigger-than 2M --replace-text banned.txt repo.git
git reflog expire --expire=now --all && git --prune=now --aggressive
git push --force  # in github, unprotect branch before this.

# Get latest tag
git ls-remote --refs --tags $GIT_REPO  # from remote, pushed tags
git describe                           # locally available tag, or a uuid
```

## Python

### Avoid import error (python run.py or python module/run.py)
```python
# Add this to the top of file to avoid module import errors 
# (when the directory where we are running is different)

import sys
from pathlib import Path

FILE = Path(__file__).resolve()
ROOT = FILE.parents[0]  # Root directory
if str(ROOT) not in sys.path:
    sys.path.append(str(ROOT))  # add ROOT to PATH
ROOT = Path(os.path.relpath(ROOT, Path.cwd()))  # relative
```

### Python Docker SDK (docker-py)
```python
# pip install docker
# provides easy methods to start/stop containers & access their status
import docker
client = docker.from_env()

# docker ps
ctainers = client.containers.list()

# find container by image name
def get_container_by_image_name(image_name):
    for ctainer in client.containers.list():
        if image_name.lower() in ctainer.attrs['Config']['Image']:
            return ctainer
    return None
     
# Stop a running container
image_name = 'nvcr.io/nvidia/tritonserver:22.12-py3'
ctainer = find_container_by_image_name(image_name)
ctainer.stop()

# Start a container
image_name = 'nvcr.io/nvidia/tritonserver:22.12-py3'
command = 'tritonserver --models-repo /models'
volumes = [{"/workspace/models-repo:/models:rw"}]
device_requests = [ docker.types.DeviceRequest(capabilities=[['gpu']]) ]
client.containers.run(
    image=image_name,               # image name
    command=command,                # command to run in container
    stdout=False,                   # disable logs
    stderr=False,                   # disable stderr logs
    detach=True,                    # detached mode - daemon
    remove=True,                    # remove fs after exit
    auto_remove=True,               # remove fs if container fails
    runtime='nvidia',               # Use nvidia-container-runtime
    device_requests=device_requests,# --gpus=all
    network_mode='host',            # --net=host
    volumes=volumes                 # -v (mount volumes)
)

```

### Python specify types
[Python Type Hints Cheat Sheet](https://mypy.readthedocs.io/en/latest/cheat_sheet_py3.html)
