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



