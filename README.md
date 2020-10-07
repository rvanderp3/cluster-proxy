# Quick Cluster-Wide Proxy

This project describes a quick-start for deploying a Squid proxy in a container.  You may need to update squid.conf to match the requirements of the test environment.

~~~
sudo mkdir -p /opt/squid/cache
sudo mkdir -p /opt/squid/logs
sudo cp squid.conf /opt/squid

sudo podman run --privileged --name squid -d --restart=always \
  --publish 3128:3128 \
  --volume /opt/squid/squid.conf:/etc/squid/squid.conf \
  --volume /opt/squid/logs:/var/log/squid \
  --volume /opt/squid/cache:/var/spool/squid \
  sameersbn/squid:3.5.27-2
~~~

## Updating `squid.conf`

1. Update `/opt/squid/squid.conf`
2. `sudo podman stop squid`
3. `sudo podman start squid`
