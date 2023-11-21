# playground traefik and Lets Encrypted

## Traefik

![](traefik.png)

* Docs Traefik: https://doc.traefik.io/traefik/

* Traefik is an open-source Edge Router that makes publishing your services a fun and easy experience. It receives requests on behalf of your system and finds out which components are responsible for handling them.

* We will use traefik as an ingress

1: Here we will use traefik through helm, the kubernetes package manager

https://artifacthub.io/packages/helm/traefik/traefik

** OBS: Have an operational Kubernetes cluster, in this tutorial, I am using kind. **

## Deployment Traefik

First, we need to add the repository.

```sh
helm repo add traefik https://traefik.github.io/charts
```

Updating helm repositories
```sh
helm repo update
```

Before actually installing traefik, let's download the traefik file used, so we can check it in more detail and know what is being installed.

```sh
helm show values traefik/traefik > values-traefik.yml
```

See that he created a file called values-traefik.yml, in which you can study it and change some things, if you want.

We will use the default file.

First, let's create a namespace for traefik

```sh
kubectl create namespace traefik-system
```

Now!, Installing the traefik package

```sh
helm upgrade --install traefik traefik/traefik --namespace traefik-system
```
We use upgrade --install, so if it is installed, it will update and if it is not installed, it will install.

You can customize the install with a values file. There are some EXAMPLES provided. Complete documentation on all available parameters is in the default file.

```sh
helm install -f values-traefik.yaml traefik traefik/traefik
```

Command output

```sh
➜  playground-traefik-ssl git:(main) ✗ helm upgrade --install traefik traefik/traefik --namespace traefik-system
Release "traefik" does not exist. Installing it now.
NAME: traefik
LAST DEPLOYED: Tue Nov 21 15:11:37 2023
NAMESPACE: traefik-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Traefik Proxy v2.10.5 has been deployed successfully on traefik-system namespace !
```

Now, we check that everything looks good in Kubernetes.
```sh
kubectl get all -n traefik-system
```

Command output
```sh
➜  playground-traefik-ssl git:(main) ✗ kubectl get all -n traefik-system
NAME                           READY   STATUS    RESTARTS   AGE
pod/traefik-756b58d769-4qx7v   1/1     Running   0          59s

NAME              TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
service/traefik   LoadBalancer   10.96.56.238   <pending>     80:31575/TCP,443:30684/TCP   59s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/traefik   1/1     1            1           59s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/traefik-756b58d769   1         1         1       59s

```

Note that in external ip it is pending, as we are using kind, we do not have a public ip. We will test everything internally.


To test locally, you can get the IP of the kind container and the port that the traefik service delivers, in this case port 31575.

```sh
➜  playground-traefik-ssl git:(main) curl http://172.18.0.2:31575/
404 page not found
```

Everything is fine, as we don't have any applications yet.

## Now we will play by creating a web server with Nginx

We have two kubernetes manifests created, one is the deployment and the other is the service, used to expose the service.

Let's start deployment and service.

```sh
kubectl create -f deployment.yml
kubectl create -f service.yml
```

Now, you can check the services that have been uploaded with the command:

```sh
kubectl get all
```

Command output

```sh
➜  playground-traefik-ssl git:(main) ✗ kubectl get all
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-79dfddc6bf-mk6jf   1/1     Running   0          8s

NAME                 TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1     <none>        443/TCP   81m
service/nginx        ClusterIP   10.96.84.99   <none>        80/TCP    5s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           8s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-79dfddc6bf   1         1         1       8s
```

Notice that the services have increased steadily.

Now let's make a route to access our web server. Enter the following command.

```sh
kubectl port-forward service/nginx 8080:80
```

Command output

```sh
➜  playground-traefik-ssl git:(main) ✗ kubectl port-forward service/nginx 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

We did a port forward, exposing the nginx service created on port 8080 from outside and 80 from inside the application.

Now, type localhost:8080 in the terminal or browser and see the result. I will do it in the terminal with curl.

```sh
curl localhost:8080
```

Command output

```sh
➜  playground-traefik-ssl git:(main) ✗ curl localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Perfect!!! Now we can play more!