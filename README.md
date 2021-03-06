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

Cf.) https://coreos.com/rkt/docs/latest/getting-started-guide.html

```bash
$ vagrant ssh
Welcome to Barge 2.5.5, Docker version 1.10.3, build 20f81dd
[bargee@barge ~]$ cd /vagrant/hello
[bargee@barge hello]$ make run
[bargee@barge hello]$ wget -qO- http://localhost:5000
hello
```

## Running Docker images with rkt

Cf.) https://coreos.com/rkt/docs/latest/running-docker-images.html

```bash
$ vagrant ssh
Welcome to Barge 2.5.5, Docker version 1.10.3, build 20f81dd
[bargee@barge ~]$ rkt --insecure-options=image fetch docker://quay.io/zanui/nginx
Downloading sha256:f0a98344d60 [=============================]       86 B / 86 B
Downloading sha256:2bd0ba29925 [=============================]     212 B / 212 B
Downloading sha256:7158495444d [=============================] 1.22 KB / 1.22 KB
Downloading sha256:8af4a816187 [=============================]     212 B / 212 B
Downloading sha256:1622327c5d5 [=============================]     393 B / 393 B
Downloading sha256:1d52d717b55 [=============================]     455 B / 455 B
Downloading sha256:0b78c848bd5 [=============================]     207 B / 207 B
Downloading sha256:20aa884e700 [=============================] 28.6 MB / 28.6 MB
Downloading sha256:fdd950a5860 [=============================] 11.2 KB / 11.2 KB
Downloading sha256:3569e33d720 [=============================]     189 B / 189 B
Downloading sha256:c140e43f686 [=============================]     214 B / 214 B
Downloading sha256:0f1077d5c3f [=============================]     210 B / 210 B
Downloading sha256:e6ba3c3cbe7 [=============================]     186 B / 186 B
Downloading sha256:6f9c3df9cd6 [=============================]     187 B / 187 B
Downloading sha256:d3b54b8677a [=============================] 71.3 KB / 71.3 KB
Downloading sha256:ea4dc0e76ce [=============================]     584 B / 584 B
Downloading sha256:61d5313dc1d [=============================]     210 B / 210 B
Downloading sha256:7040f66c7fc [=============================]   884 KB / 884 KB
Downloading sha256:bc192f048d3 [=============================] 67.5 MB / 67.5 MB
Downloading sha256:c286dd0e209 [=============================] 31.8 MB / 31.8 MB
Downloading sha256:e6ba3c3cbe7 [=============================]     186 B / 186 B
Downloading sha256:452ddc1156a [=============================]     818 B / 818 B
Downloading sha256:afb5b7121f3 [=============================]     209 B / 209 B
sha512-51ef060333687272ca1d995bc8716c0e
[bargee@barge ~]$ rkt image list
ID			NAME				SIZE	IMPORT TIME	LAST USED
sha512-51ef06033368	quay.io/zanui/nginx:latest	276MiB	5 minutes ago	5 minutes ago
[bargee@barge ~]$ sudo start-stop-daemon -S -b --exec rkt -- --insecure-options=image run --port=80-tcp:8080 docker://quay.io/zanui/nginx
[bargee@barge ~]$ rkt list
UUID		APP	IMAGE NAME			STATE	CREATED		STARTED		NETWORKS
eeb55271	nginx	quay.io/zanui/nginx:latest	running	3 seconds ago	3 seconds ago	default:ip4=172.16.28.2
```

```bash
$ open http://localhost:8080
```

```bash
[bargee@barge ~]$ sudo rkt stop eeb55271
"eeb55271-7464-49c3-8d22-c3d32e6e7410"
[bargee@barge ~]$ rkt list
UUID		APP	IMAGE NAME			STATE	CREATED		STARTED		NETWORKS
eeb55271	nginx	quay.io/zanui/nginx:latest	exited	2 minutes ago	2 minutes ago
[bargee@barge ~]$ sudo rkt rm eeb55271
"eeb55271-7464-49c3-8d22-c3d32e6e7410"
[bargee@barge ~]$ rkt list
UUID	APP	IMAGE NAME	STATE	CREATED	STARTED	NETWORKS
```
