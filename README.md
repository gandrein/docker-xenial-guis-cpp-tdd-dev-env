## Purpose

Docker image with GTest and GMock framework for C++ Test-Driven Development.

The image is customized such that:
* the docker image is created with a non-root user (default user-name `docker`)
* [Oh My ZSH](http://ohmyz.sh/) is installed and configured for the non-root user
* docker containers are launched with [`terminator`](https://gnometerminator.blogspot.nl/p/introduction.html) as the default terminal emulator (as opposed to default `gnome-terminal`)
* bash completion for Docker image names and tags when launching the container by using the `./run_docker.sh` script
	* remember to source the [bash_docker_images_completion.sh](./docker/configs/bash_docker_images_completion.sh) file from the [docker/configs](./docker/configs) folder.

## Requirements

This docker image has been build and tested on a machine running Ubuntu 16.04 with `docker` version `17.09.0-ce`. 

If your machine has a NVIDIA Video card, to successfully run OpenGL dependent GUIs from the docker container you will need to install [`nvidia-docker`](https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-1.0)) version `1.0`. 

## Automatic building of GTest & GMock

The docker image contains examples on how to integrate the GoogleTest framework in your existing project. This is based on Google's recommendation from the [Incorporating Into An Existing CMake Project](https://github.com/google/googletest/blob/master/googletest/README.md#incorporating-into-an-existing-cmake-project) on the GoogleTest's github repository. 

The choice is to use the last, least dependent method. As per [Google](https://github.com/google/googletest/blob/master/googletest/README.md#incorporating-into-an-existing-cmake-project) the recommended way is based on the article [Building GoogleTest and GoogleMock directly in a CMake project](https://crascit.com/2015/07/25/cmake-gtest/). 

The generalized approach for adding any External Project to your own CMake project is available in this github repository: [Crascit/DownloadProject](https://github.com/Crascit/DownloadProject).

### CMake Module Way

In order to separate the configuration of the GTest from your own CMake file, a second example is given where the [Crascit/DownloadProject](https://github.com/Crascit/DownloadProject) example is modified such that the CMake Module facility is used. This example is available in the [gtest_cmake_module_based_example](./gtest_cmake_module_based_example) folder.


Both examples are available in the resulting Docker containers.

### Building the image

The [Makefile](./docker/Makefile) contained in the repository allows you to create docker images for the target platform you are interested in NVIDIA/nonNVIDIA. In a terminal type `make` followed by a `<TAB>` to see the available auto-complete options. 
```
make <TAB>
```

If you wish to change the names given to the Docker images the recommended way is to give a new TAG to the image by using the 
```
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```
command, after you have build it with the `make` instruction. 

You could also change the names in the `Makefile`. However, make sure you do not break the dependencies between the two images as the second image (for NVIDIA platform) depends on the first. 

#### The ENTRYPOINT `entrypoint.sh` script
For each image type, the bash script [entrypoint.sh](./docker/entrypoint.sh) will be copied at build time into the Docker image and will be ran as the default _entrypoint_ when the container is launched. 

The current entrypoint script is customized for the use case where the host's QT Creator folder is mounted into the container at `$HOME/Qt` folder of the `non-root` user. 

The script creates an alias for the QT Creator's executable so it can be launched from the container's terminal with `qtcreator` command.

### Running a container

In the terminal call the [./run_docker.sh](./docker/run_docker.sh) script from the [docker](./docker) folder with the default given name or the name. The script will perform some Docker environment variables setting and volume mounting and set other docker flags, e.g., remove the container upon exit (i.e., it is ran with the `--rm` flag).

```
./run_docker.sh IMAGE_NAME
```

The script checks for the existence and the version of `nvidia-docker` and calls `docker run` differently based on version. The container shares the X11 unix socket with the host and its network interface.

### Bash auto-completion for `./run_docker.sh`

When using `./run_docker.sh` in a bash shell to launch the container, source the [configs/bash_docker_images_completion.sh](./docker/configs/bash_docker_images_completion.sh) script. Now you should be able to get the names of the available docker images on your system whenever you type 
```
./run_docker.sh <TAB><TAB>
```