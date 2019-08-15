# Traefik on GKE

Project template to build an application running on Google Kubernetes Engine (GKE) with Traefik.

# Requirements

This project just requires an existing cluster on GKE ond kubectl connected to it.

# Run Treafik

During this setup, for the sake of simplicity we'll store the ACME (Automated Certificate Management Environment) configuration on a GCE disk. This will prohibit you from adding another pod to the Treafik deployment. To overcome this issue, use [another way](https://docs.traefik.io/configuration/acme/#storage) of storing Treaefik's `acme.json`.

First, create the GCE disk:

```bash
gcloud compute disks create --size=2GB --zone=your-region demo-acme
```

Second, update the following values in the config files in the `traefik/` folder:

- `<Email>` in `traefik/01_permissions.yml` and `traefik/03_config.yml`
- `example.com` in `traefik/03_config.yml` to your domain pointing to your Kubernetes load balancer (IP visible on GKE after deployment)
- if you want to create a separate ingress for the Treafik dashboard, also update `traefik.example.com` in `traefik/06_ingress.yml` to the domain you previously specified

After updating the values, apply the configuration to your Kubernetes cluster using the following command:

```bash
kubectl apply -f traefik/
```

> If you don't need an ingress for the Traefik dashboard, just apply the config files 1 - 5.

Traefik should now be up & running on GKE. You can now point your domain to the IP of the load balancer (visible in `Services & Ingress` > `treafik-ingress-controller`).

# Deploying a Demo Service

After Traefik is running, you can start deploying other services into the cluster. Traefik will automatically pick them up and create a frontend and backend according to the rules specified in the ingress definition.

To the deploy the `whoami` demo service from `containous`, first update the host rule in `whoami/03_ingress.yml`. After that deploy the service:

```bash
kubectl apply -f whoami/
```
