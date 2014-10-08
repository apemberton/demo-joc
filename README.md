demo-base
=========

This repo is a base image to be used as storage for a Jenkins demo.

Usage
-----

```
docker run -d --name storage apemberton/jenkins-storage git clone https://github.com/apemberton/demo-base.git .
docker run -d --name jenkins --volumes-from storage -p 80:8080 -e JENKINS_HOME=/data/var/lib/jenkins apemberton/jenkins-enterprise
```

Optional
--------

If you'd like to save off your own demo, there are just a few steps:

1. Expose the storage volume so it's viewable from OS X via Samba

```
docker run --rm -v /usr/local/bin/docker:/docker -v /var/run/docker.sock:/docker.sock svendowideit/samba storage
```

2. Mount the storage volume

Instructions are here: https://github.com/boot2docker/boot2docker but essentially open cifs://192.168.59.103/

3. Commit your changes

```
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
git add -A
git commit -m "committing my demo"
git push
```
