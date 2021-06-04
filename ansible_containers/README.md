# Installing An ELK Stack with Ansible and Podman

In this excercise you will install an elasticsearch, logstash, and kibana (ELK) stack.
A Pod will be created to hold the containers, using an Ansible playbook to setup
everything.

> **NOTE** You may need to open firewall ports, if you have a firewall running.

First the _containers.podman_ Ansible collection is installed.

```console
ansible-galaxy collection install containers.podman
```

Once the collection is installed you can run the playbook. It will run as your user
on your local machine as localhost. The directory _~/etc_ will created in your
home directory to hold the configuration files.

```console
ansible-playbook elk.yml
```

Once the playbook has completed running you should have a podman pod with 4 containers.

```console
$ podman pod ps
POD ID        NAME    STATUS    CREATED            INFRA ID      # OF CONTAINERS
204a25d8bdd8  elk     Running   About an hour ago  22f06e318334  4
```

You can also list all the containers.

```console
$ podman ps --format "{{.ID}}  {{.Image}}"
22f06e318334  k8s.gcr.io/pause:3.5
2238a8286653  docker.elastic.co/kibana/kibana:7.13.0
8dbc31818ade  docker.elastic.co/logstash/logstash:7.13.0
b4f160d78e23  docker.elastic.co/elasticsearch/elasticsearch:7.13.0
```

You can access your elk stack at http://localhost:5601/

Once you are done you can remove the containers with the `podman rm` command and 
using the container ids or container names.

```console
podman pod stop elk
podman rm kibana logstash elasticsearch
podman pod rm elk
```
