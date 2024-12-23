# Getting Zedire Environment ready

## Docker Desktop

To install Ingress-Nginx Controller on **Docker Desktop** follow [Quick start](https://kubernetes.github.io/ingress-nginx/deploy/#quick-start).

**If above link is not working**, you can run the following commands instead:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0-beta.0/deploy/static/provider/cloud/deploy.yaml
```
#### Firewall configuration

To check which ports are used by your installation of ingress-nginx, look at the output of 

```
kubectl -n ingress-nginx get pod -o yaml
```

In general, you need:

 - Port 8443 open between all hosts on which the kubernetes nodes are running. This is used for the ingress-nginx admission controller.
 - Port 80 (for HTTP) and/or 443 (for HTTPS) open to the public on the kubernetes nodes to which the DNS of your apps are pointing.

#### Pre-flight check 
A few pods should start in the ingress-nginx namespace:

```
kubectl get pods --namespace=ingress-nginx
```
After a while, they should all be running. The following command will wait for the ingress controller pod to be up, running, and ready:

```
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=120s
```
#### Local testing

Then manually adding your ingresses' hostnames to /etc/hosts:

> 127.0.0.1     ingress.local

Then if you make a request on http://ingress.local:

 - DNS resolution will route to your cluster node
 - The ingress controller will serve the request on port 80
 - Then ingress will route the request to the configured backend service
  - And the service will route to an available pod
