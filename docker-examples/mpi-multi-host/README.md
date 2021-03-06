# Docker example - mpi-multi-host

This example runs two MPI programs across two hosts/nodes. To do so, it uses [Docker Compose](https://docs.docker.com/compose/) to build a Docker image and start two containers based on that image.

This programs are:

1. A simple `hello world` program, which you can inspect (and alter) in [helloworld_multihost.c](helloworld_multihost.c).

1. A simple `matmult` (matrix multiplication) program, which you can inspect (and alter) in [matmult_multihost.c](matmult_multihost.c).

Note that the containers are started in descending order (`node2` is started before `node1`) in [`docker-compose.yml`](docker-compose.yml) with some hackery to add an entry in `node2` for `node1`'s IP address in `/etc/hosts`. This is because Docker Compose supports one-way linking (i.e. `node1` will have an entry in its `/etc/hosts` file for `node2`, but not the way around). For two-way linking I would suggest looking at [serf](http://serfdom.io) and/or [weave](https://github.com/zettio/weave), which I don't cover here.

Note that the [Dockerfile](Dockerfile) bases this image off the single-host image example. Make sure you have built that image (see its [README](../README.md)) to do so before running any of this!

### Building the image

```bash
# View available docker images
[~]: docker images

# Change into this directory
[~]: cd /path/to/docker-examples/mpi-multi-host

# Build the docker image for this example using Docker Compose
[/path/to/docker-examples/mpi-multi-host]: docker-compose build

# Watch it build. It may take some time the first time you run it.

# Convince yourself the above image has been built
[/path/to/docker-examples/mpi-multi-host]: docker images

# Notice that this time the image is named mpimultihost_node2, which is a name
# generated by Docker Compose based on the name of this directory and the name we gave the container.
# Also notice that in docker-compose.yml we only specify the image to build for the first node, and
# for node1 we use that built image again. Naming is a bit weird, but hopefully Docker Compose will
# evolve to be friendlier that way.

```

### Running the application

```bash
# These commands assume you're in the /path/to/docker-examples/mpi-multi-host directory
# since docker-compose looks for docker-compose.yml in that directory.

# Run the app
[/path/to/docker-examples/mpi-multi-host]: docker-compose up

# Convince yourself the application has run
[~]: docker-compose ps

# Kill and remove the application
[~]: docker-compose kill && docker-compose rm

```

### Notes

These containers will sip on your local computer's RAM and munch on CPU using Docker's defaults. You can, if you so wish, play around with docker `run` options `--cpu-shares` and `--cpuset` to limit how much of a CPU a container is allowed to use or which CPUs it can use respectively. You can also limit the memory it sips on using the `--memory` option. Check out the [docs](http://docs.docker.com/reference/commandline/cli/#run) for more info and also [here](http://agileek.github.io/docker/2014/08/06/docker-cpuset/) for a great example.