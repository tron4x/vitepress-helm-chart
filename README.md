## Custom Helm Chart for vitepress

See [Vitepress Complete Guide](https://docs.devops.bayern/nodejs/vitepress.html)

Values must be adjustet for your needs.

## Docker Image

```yaml
image:
  repository: # example ghcr.io/<account>/docs 
  pullPolicy: Always
  # Overrides the image tag whose default is the chart appVersion.
  tag: "1.1"
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
Cert-Manager is required. See 

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