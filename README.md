Here’s a minimal `Dockerfile` based on Ubuntu that installs OpenCode using the official installer:

```dockerfile
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update && apt-get install -y \
    curl \
    ca-certificates \
    git \
    bash \
    && rm -rf /var/lib/apt/lists/*

# Install OpenCode
RUN curl -fsSL https://opencode.ai/install | bash

# Add OpenCode to PATH
ENV PATH="/root/.opencode/bin:${PATH}"

# Default shell
CMD ["/bin/bash"]
```

# Documentation

## Build

### General Ubuntu Image

#### User Image
```bash
docker build -f Dockerfiles/dockerfile_ubuntu_user -t opencode-user .
```

#### Root Image
```bash
docker build -f Dockerfiles/dockerfile_ubuntu_root -t opencode-root .
```

### Node 24 Ubuntu Image

#### User Image
```bash
docker build -f Dockerfiles/dockerfile_ubuntu_node_user -t opencode-node-user .
```

### Max Image (Node + Android Build Environment)

#### User Image
```bash
docker build -f Dockerfiles/dockerfile_ubuntu_max_user -t opencode-user-max .
```


## Run

### Run as User

#### General
```bash
docker run -it \
    -v "$HOME/.config/opencode:$HOME/.config/opencode" \
    -v /etc/passwd:/etc/passwd:ro -v /etc/group:/etc/group:ro --user "$(id --user):$(id --group)" \
    -v "$(pwd):$(pwd)" --workdir "$(pwd)" \
    opencode-user
```

#### For CubeIDE and J-Link Embedded
```bash
docker run -it \
    -v "$HOME/.config/opencode:$HOME/.config/opencode" \
    -v /etc/passwd:/etc/passwd:ro -v /etc/group:/etc/group:ro --user "$(id --user):$(id --group)" \
    -v /opt/st/stm32cubeide_2.0.0/:/opt/st/stm32cubeide_2.0.0/ \
    -v /opt/SEGGER/:/opt/SEGGER/ \
    -v /dev/bus/usb:/dev/bus/usb \
    --privileged \
    -v "$(pwd):$(pwd)" --workdir "$(pwd)" \
    opencode-user
```

#### For Android Development (opencode-user-max)
```bash
docker run -it \
    -v "$HOME/.config/opencode:$HOME/.config/opencode" \
    -v /etc/passwd:/etc/passwd:ro -v /etc/group:/etc/group:ro --user "$(id --user):$(id --group)" \
    -v "$HOME/Android/Sdk:/opt/android-sdk" \
    -v "$(pwd):$(pwd)" --workdir "$(pwd)" \
    opencode-user-max
```

Adjust `$HOME/Android/Sdk` to match your host Android SDK path.


### Run as Root
```bash
docker run -it \
    -v "$HOME/.config/opencode:/root/.config/opencode" \
    -v "$(pwd):/root/workdir" --workdir /root/workdir \
    opencode-root
```

### Verify installation inside the container:

```bash
opencode --version
```

The official install method uses:

```bash
curl -fsSL https://opencode.ai/install | bash
```

and installs the binary into `~/.opencode/bin`. ([computingforgeeks.com][1])

[1]: https://computingforgeeks.com/install-opencode-oh-my-openagent-linux/?utm_source=chatgpt.com "Install OpenCode with Oh-My-OpenAgent on Linux (Ubuntu / Rocky Linux) | ComputingForGeeks"
