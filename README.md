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

```bash
docker build -f Dockerfiles/dockerfile_ubuntu -t opencode .
```

## Run

### Run as Administrator
```bash
docker run -it \
    -v $HOME/.config/opencode:/root/.config/opencode \ 
    -v $(pwd):/root/workdir --workdir /root/workdir \
    opencode 
```

### Run as User
```bash
docker run -it \
    -v $HOME/.config/opencode:/root/.config/opencode \
    -v /etc/passwd:/etc/passwd:ro -v /etc/group:/etc/group:ro --user $(id --user):$(id --group) \
    -v $(pwd):$(pwd) --workdir $(pwd) \
    opencode 
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
