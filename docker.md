# Docker

- Virtualization software
- Makes developing and deploying applications much easier.
- Packages apps with all their dependecies, configuration, system tools and runtime.
- Standardizes process of running any service on any dev environment.

## Docker Architecture.

- Virtualizes the application layer of the operating system as opposed to the Vm which virtualizes the kernle layer.
- Docker Engine
  - a server that is running dockerd
  - manages images and containers
- Docker CLI - Client
  - cli to interact with docker server.
- Docker Images
  - an executable application artifact
  - includes app spurce code, but also complete configuration
  - add environment variables, create directories and files
- Docker Containers
  - running instance of an image
  - can run multiple containers from one container
- Docker Registry
  - repository of docker images with their corresponding tags
  - `docker pull image_name` or docker run immediately
- Docker port binding
  - forward container port to our network port
