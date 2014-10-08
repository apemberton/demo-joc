demo-joc
=========

This repo is a complex JOC demo w/HA, client masters, and shared slaves.

Usage
-----

```
// create storage
docker run -d --name storage apemberton/jenkins-storage

// start skydns and skydock
docker run -d -p 172.17.42.1:53:53/udp --name skydns crosbymichael/skydns -nameserver 8.8.8.8:53 -domain beedemo.io
docker run -d -v /var/run/docker.sock:/docker.sock --name skydock crosbymichael/skydock -ttl 30 -environment dev -s /docker.sock -domain beedemo.io -name skydns

// start joc ha cluster
docker run -d --dns=172.17.42.1 --name joc-1 --volumes-from storage -e JENKINS_HOME=/data/var/lib/jenkins/joc apemberton/jenkins-operations-center --prefix=""
docker run -d --dns=172.17.42.1 --name joc-2 --volumes-from storage -e JENKINS_HOME=/data/var/lib/jenkins/joc apemberton/jenkins-operations-center --prefix=""

// start haproxy and joc haproxy monitor
docker run -d --dns=172.17.42.1 --name proxy -p 80:80 --volumes-from storage apemberton/demo-joc-haproxy -j /data/var/lib/jenkins/joc -p '' joc-1.jenkins-operations-center.dev.beedemo.io:8080 joc-2.jenkins-operations-center.dev.beedemo.io:8080

// add an example master ha cluster
docker run -d --dns=172.17.42.1 --name api-team-1 --volumes-from storage -e JENKINS_HOME=/data/var/lib/jenkins/api-team apemberton/jenkins-enterprise --prefix="/api-team"
docker run -d --dns=172.17.42.1 --name api-team-2 --volumes-from storage -e JENKINS_HOME=/data/var/lib/jenkins/api-team apemberton/jenkins-enterprise --prefix="/api-team"

// add slaves
docker run -d --dns=172.17.42.1 --name slave-1 apemberton/jenkins-slave
docker run -d --dns=172.17.42.1 --name slave-2 apemberton/jenkins-slave
