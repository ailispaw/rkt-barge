# rkt on Barge

[rkt](https://coreos.com/rkt/) is the next-generation container manager for Linux clusters.

This shows how to create a rkt environment on [Barge](https://atlas.hashicorp.com/ailispaw/boxes/barge) with [Vagrant](https://www.vagrantup.com/) instantly.

## Requirements

- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)

## Boot up

```bash
$ vagrant up
```

That's it.

## Hello World

```bash
$ vagrant ssh
Welcome to Barge 2.1.8, Docker version 1.9.1, build 66c06d0-stripped
[bargee@barge ~]$ sudo start-stop-daemon -S -b --exec rkt -- --insecure-options=image run --port=80-tcp:8080 docker://quay.io/zanui/nginx
[bargee@barge ~]$ rkt list
UUID    APP IMAGE NAME      STATE CREATED   STARTED   NETWORKS
21baf091  nginx quay.io/zanui/nginx:latest  running 3 seconds ago 3 seconds ago default:ip4=172.16.28.2
```

```bash
$ open http://localhost:8080
```

```bash
[bargee@barge ~]$ sudo rkt stop 21baf091
[bargee@barge ~]$ rkt list
UUID    APP IMAGE NAME      STATE CREATED   STARTED   NETWORKS
21baf091  nginx quay.io/zanui/nginx:latest  exited  30 minutes ago  30 minutes ago
[bargee@barge ~]$ sudo rkt rm 21baf091
[bargee@barge ~]$ rkt list
UUID  APP IMAGE NAME  STATE CREATED STARTED NETWORKS
```
