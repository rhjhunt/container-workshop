# Building a Container

By using the `buildah` command you can build containers from a _Dockerfile_. Like
the one below. Which builds a markdown linter.

```dockerfile
# The base container
FROM registry.access.redhat.com/ubi8-minimal:latest

LABEL   name="mdl" \
        version="1.0.0" \
        architecture="x86_64" \
        summary="Open source Markdown link tool in Ruby"
    
# RHEL 8 comes with several versions of ruby, this step enables version 2.7
RUN echo -e "[ruby]\nname=ruby\nstream=2.7\nprofiles=\nstate=enabled\n" > /etc/dnf/modules.d/ruby.module && \
    # Update any out of date packages
    microdnf -y --nodocs update && \
    # Install Ruby
    microdnf -y --nodocs install ruby ruby-devel && \
    # Post package install clean up
    microdnf clean all  && \
    # Remove the yum cache
    rm -rf /var/cache/yum && \
    # Use gem to install the markdownlinter
    gem install --no-document mdl

# When the container starts this is the command executed
ENTRYPOINT ["mdl"]
# These are the options passed to the ENTRYPOINT command
CMD ["--help"]
```

You can build the container with the following command.

```console
buildah bud --tag mdl .
```

You can confirm that the container was built with the following command.

```console
podman images          
REPOSITORY             TAG     IMAGE ID      CREATED         SIZE
localhost/mdl          latest  d598d52b0c7b  50 seconds ago  138 MB
```

You can run the container to lint this _README.md_ file.

```console
$ podman run --rm -it --workdir "$(pwd)" --volume "${HOME}:${HOME}:rslave" --security-opt label=disable localhost/mdl .
./README.md:14: MD009 Trailing spaces
./README.md:43: MD009 Trailing spaces
./README.md:16: MD013 Line length
./README.md:51: MD013 Line length
```
