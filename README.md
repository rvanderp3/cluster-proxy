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

# Deploying the Proxy as a Pod

For testing purposes it is sometimes helpful to be able to deploy a proxy as a pod on an existing cluster.  The steps below describe how to deploy squid as a pod which binds to the hostNetwork.

~~~
oc new-project squid-proxy
oc adm policy add-scc-to-user privileged -z default
oc create configmap squid-conf --from-file=squid.conf
oc apply -f squid-deployment.yaml
~~~

Any node that is labeled with `squid-proxy=""` will have the proxy deployed and bound to its host network.  Once the proxy is deployed patch the cluster-wide proxy with the proxy details.

~~~
PROXY_IP=`oc get pods -l app=squid -o=jsonpath='{.items[0].status.podIP}'`
oc patch proxy cluster -p='{"spec": {"httpProxy": "http://'"$PROXY_IP"':3128", "httpsProxy": "http://'"$PROXY_IP"':3128"}}' --type=merge
~~~
