# playground traefik and Lets Encrypted

## Traefik

![](traefik.png)

* Docs Traefik: https://doc.traefik.io/traefik/

* Traefik is an open-source Edge Router that makes publishing your services a fun and easy experience. It receives requests on behalf of your system and finds out which components are responsible for handling them.

* We will use traefik as an ingress

1: Here we will use traefik through helm, the kubernetes package manager

https://artifacthub.io/packages/helm/traefik/traefik

## Deployment Traefik

```sh
helm install traefik traefik/traefik
```

You can customize the install with a values file. There are some EXAMPLES provided. Complete documentation on all available parameters is in the default file.

```sh
helm install -f myvalues.yaml traefik traefik/traefik
```

Upgrading
One can check what has changed in the Changelog.

```sh
# Update repository
helm repo update
# See current Chart & Traefik version
helm search repo traefik/traefik
# Upgrade Traefik
helm upgrade traefik traefik/traefik
```