# openio/sds Dockerfile

This image provides an easy way to run an OPENIO namespace with an Openstack Swift/Swift3 proxy.
It deploys and configure a simple non-replicated namespace in a single Docker container.

## How to get this image from the hub

To get the latest one built on the [docker hub](https://hub.docker.com/r/openio/sds) 

```console
# docker pull openio/sds
```

or

```console
# docker pull openio/sds:latest
```

If you want a specific OpenIO SDS version & linux distribution

For example, run:

```console
# docker pull openio/sds:16.10-centos-7
```

You can choose between:

- 16.10-centos-7
- 17.04-centos-7
- 17.04-ubuntu-xenial

Those images are rebuilt regularly, but if you want to have the most up-to-date
versions, the best is to rebuild them locally (on your computer).

## How to (re-)build this image

First get the source:

```console
# git clone https://github.com/open-io/dockerfiles.git
# cd dockerfiles
```

Then build one specific image:

```console
# docker build -t openio/sds:17.04-centos-7-local openio-sds/17.04/centos/7
```

## How to use this image

OpenIO SDS depends on IPs, meaning that you can't change service IPs after they have been registered to the cluster.
By default, Docker networking change your IP when your container restarts which is not compatible with OpenIO SDS at the moment.

### Keep it simple

By default, start a simple namespace listening on 127.0.0.1 inside the container.
An Openstack Swift proxy with the Swift3 middleware is available, with [TempAuth](https://docs.openstack.org/developer/swift/overview_auth.html#tempauth) authentication system and default credentials set to `demo:demo` and password `DEMO_PASS`.


```console
# docker run -d openio/sds
```

When starting the container, it takes a few seconds to start (depending on your host performance). To show the container startup, you can remove the `-d` option to the command line.

To access the `openio` command line, you can access inside the container (supposing the OpenIO SDS container is the latest you started):

```console
# docker exec -ti --tty $(docker ps -l --format "{{.ID}}") /bin/bash
```


### Using host network interface

You can start an instance using Docker host mode networking, it allows you to access the services outside your container. You can specify the interface or the IP you want to use.

Setting the interface:
```console
# docker run -e OPENIO_IFDEV=enp0s8 --net=host openio/sds
```

Specifying the IP:
```console
# docker run -e OPENIO_IPADDR=192.168.56.101 --net=host openio/sds
```

Change the Openstack Swift default credentials:
```console
# docker run -e OPENIO_IFDEV=enp0s8 --net=host -e SWIFT_CREDENTIALS="myproject:myuser:mypassord:.admin" openio/sds
```

Bind the Openstack Swift/Swift3 proxy port to you host:
```console
# docker run -p 192.168.56.101:6007:6007 openio/sds
```

Setting the default credentials to test Openstack Swift functionnal tests:
```console
# docker run -p 192.168.56.101:6007:6007 -e SWIFT_CREDENTIALS="admin:admin:admin:.admin .reseller_admin,test2:tester2:testing2:.admin,test5:tester5:testing5:service,test:tester:testing:.admin,test:tester3:testing3" openio/sds
```

Before using `openio` CLI or Python, Java or C API from the outside, copy the contents of `/etc/oio/sds.conf.d/OPENIO` from the container to the same file on your host.
```console
# docker exec -ti --tty $(docker ps -l --format "{{.ID}}") /bin/cat /etc/oio/sds.conf.d/OPENIO > /etc/oio/sds.conf.d/OPENIO
```

## Documentation

To test with different object storage clients:
- [OpenIO SDS command line](http://docs.openio.io/user-guide/openiocli.html)
- [Openstack Swift command line (using TempAuth)](http://docs.openio.io/user-guide/swiftcli.html#tempauth)
- [AWS S3 command line](http://docs.openio.io/user-guide/awscli.html)
