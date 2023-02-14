<h1 align="center"><code>vbackup</code></h1>

The [docker export](https://docs.docker.com/engine/reference/commandline/export/) and [docker commit](https://docs.docker.com/engine/reference/commandline/commit/) commands do not save the container
volumes. Use this script to save and load the container volumes.

## Usage

```bash
vbackup [-v|--verbose] <subcommand> <subcommand-arguments>
```

## Podman

To use [Podman](https://podman.io) instead of Docker, set the `VBACKUP_RUNTIME` environment
variable to `podman` like so:

```bash
VBACKUP_RUNTIME=podman vbackup ...
```

## Examples

Let's migrate a container to another host along with all its volumes.

```bash
# Stop the container
❯ docker stop $CONTAINER
# Create a new image
❯ docker commit $CONTAINER $CONTAINER
# Transfer the image to another host
❯ docker save $CONTAINER | ssh $USER@$HOST docker load
# Save the volumes (using .tar.gz extension to enable compression)
❯ vbackup export-all $CONTAINER $CONTAINER-volumes.tar.gz
# Copy volumes to other host
❯ scp $CONTAINER-volumes.tar.gz $USER@$HOST:~/

## On the other host

# Create the new container with the same options used when creating the
# previous one
❯ docker create --name $CONTAINER [<previous-container-options>] $CONTAINER
# Load the volumes
❯ vbackup import-all $CONTAINER $CONTAINER-volumes.tar.gz
# Start the container
❯ docker start $CONTAINER
```
