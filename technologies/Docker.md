Docker
======

# Overview
**To-Do**

# Usage
## Building an Image: `Dockerfile`
* A Dockerfile defines the steps necessary to build an image given a base image.
* A base image is a starting point to add additional, project-specific, customisations to. They most likely have built upon another base image themselves. Images build a chain-like structure leading up to the `scratch` (empty) image.
  * A Python image would contain all basic Python binaries (as you would get if download Python onto a computer typically), and the binaries to build an operating system to run them (e.g. Debian).
* It is loosely similar to stating which actions somebody would perform if they were to SSH onto a machine (the base image) and wanted to set up an application before it's first execution. For example, the person may:
  * Install additional packages
  * Create a new directory to house the project
  * Copy the project files over to the directory
  * Install project requirements (e.g. pip, or npm)

## Running an Image Manually
If an image is built by a local machine, it will be placed into the local Docker repository. This means it can then be run using: `docker run [IMAGE_NAME]`

When this command is executed, Docker will look for an image matching the name in the local repository. If it cannot be found, the image will be searched for in a remote repository. If the image is found, the command specified by the `CMD` line in the Dockerfile used to build the image will be run.

Several options/parameters can be passed alongside the `docker run` command to alter its behaviour. For example:

* `-p [LOCAL_PORT]:[PORT_ON_CONTAINER]`
  * Exposes a container's ports to the host machine through the specificed port so that it can be interacted with.
  * For example, if the container had a web server running on port `8080`, running `-p 12345:8080` would mean that the user could load a web browser and navigate to `localhost:12345` and be routed through to the web server on the container.
* `-v [LOCAL_FILE_PATH]:[FILE_PATH_ON_CONTAINER]`
  * Exposes a file on the host machine to the container.
  * The container will see the image at the `[FILE_PATH_ON_CONTAINER]` and think that it is a local file, but in reality, the file lives on the host machine at `[LOCAL_FILE_PATH]`.

### Example
```
FROM python:3
WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD [ "python", "./your-daemon-or-script.py" ]
```

In the above example:
1. Defines the base image
2. Creates and sets the working directory on the image machine from which all subsequent commands will be run from
3. Copies the `requirements.txt` from the host machine to the image machine
4. Runs the pip command on the image machine
5. Copies the current directory from the host machine to the image machine
6. Defined the command to be run when the image is executed

## Orchestrating Images: `docker-compose.yml`
**To-Do**

# Resources
* https://docs.docker.com/
* [How to Get Started with Docker - dockercon 2020 - YouTube](https://www.youtube.com/watch?v=iqqDU2crIEQ)
