# Using Systemd With Containers

First step is to pull the container.

```console
podman pull registry.access.redhat.com/ubi8/httpd-24
```

Next use `podman inspect` to see which network ports are exposed.

```console
$ podman inspect registry.access.redhat.com/ubi8/httpd-24 --format "{{.Config.ExposedPorts}}"
map[8080/tcp:{} 8443/tcp:{}]
```

This will be a rootless container, since the port is not below 1024 root permissions
are not required to run the container.

Create a directory and _index.html_ file in your home directory that will be served
up from the container.

```console
mkdir -p ~/www/html
cd ~/www/html
echo 'Hello from my containerized webserver!' > index.html
```

You are now ready to start the container.

```console
podman run -d --name httpd -v ~/www:/var/www:z -p 8080:8080 registry.access.redhat.com/ubi8/httpd-24
```

* -d Detached mode, run the container in the background
* --name Define the container name
* -v Create a bind mount, the :z at the end is for SELinux permissions
*ti -p Publish the containers ports to the host

Very your container is running with the `podman ps` command.

```console
$ podman ps
CONTAINER ID  IMAGE                                     COMMAND               CREATED        STATUS            PORTS                   NAMES
8d34b84204ed  registry.access.redhat.com/ubi8/httpd-24  /usr/bin/run-http...  4 minutes ago  Up 4 minutes ago  0.0.0.0:8080->8080/tcp  httpd
```

You can also check that the containerized webserver is servering your webpage.

```console
$ curl http://localhost:8080
Hello from my containerized webserver!
```

Now that the container is running, you can generate a systemd file so it will automatically
start when your system boots up.

```console
podman generate systemd --name httpd > ~/.config/systemd/user/container-httpd.service
systemctl --user daemon-reload
```

Next stop the running container, and restart it using systemd.

```console
podman stop httpd
systemctl --user restart container-httpd.service
systemctl --user status container-httpd.service
podman ps
```

Your container is now being managed by a systemd service file.