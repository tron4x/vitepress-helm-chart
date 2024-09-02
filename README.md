## Custom Helm Chart for vitepress

See [Vitepress Complete Guide](https://docs.devops.bayern/nodejs/vitepress.html) on my site [docs.devops.bayern](docs.devops.bayern)
<img src="https://docs.devops.bayern/devops.png" width="100" height="100">

Values must be adjusted for your needs.

## Docker Image

```yaml
image:
  repository: # example ghcr.io/<account>/docs 
  pullPolicy: Always
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""
  ```

## Create kubernetes image pull secret

Image pull secret is needed to pull image from ghcr.io<br>
To create a docker secret in kubernetes, do this:<br>
Please set your values in: <br>
 - DOCKER_REGISTRY_SERVER
 - DOCKER_USER
 - DOCKER_PASSWORD ( use github classic token here )
 - DOCKER_EMAIL
 - NAMESPACE

```shell
kubectl create secret docker-registry dockerimg \
--docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER \
--docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL \
-n NAMESPACE
```
## Ingress 

Please set your domain or subdomain.<br>
Cert-Manager is required. See [here how to install and create cluster-issuer](https://docs.devops.bayern/rancher/rancher.html#cert-manager).

```yaml
ingress:
  enabled: true
  className: "nginx"
  annotations: 
    cert-manager.io/cluster-issuer: cert-issuer
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: <domain or subdoamin>
      paths:
        - path: /
          pathType: ImplementationSpecific
  tls: 
    - secretName: cert-issuer-secret
      hosts:
        - <domain or subdoamin>
```

All other values ​​can remain unchanged.

## Deploy helm chart to kubernetes

```shell
cd < on your helm chart directory >
```

```shell
helm install <release name> . -f values.yaml -n <namespace>
```
Example:

```shell
helm install docs . -f values.yaml -n docs
```

## Cheking if all kubernetes resources are up and running

```shell
kubectl get all -n docs 
```
```shel
NAME                       READY   STATUS    RESTARTS   AGE
pod/docs-8cf5cd5bd-xzmm5   1/1     Running   0          7m48s

NAME           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/docs   ClusterIP   10.43.136.15   <none>        80/TCP    7m48s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/docs   1/1     1            1           7m48s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/docs-8cf5cd5bd   1         1         1       7m48s
```

Thats it. Have fun 😃