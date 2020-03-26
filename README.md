# h2o-on-kubeflow
Set up h2o on a Kubeflow cluster in GCP using kubectl only. 

This is based on the [official H2O docs](https://github.com/h2oai/h2o-kubeflow).
These suggest using ksonnet, but it's just another tool you may not want to use, which is no longer being developed anyway.


## Deploy H2O Instance
1. run
```bash
# set your gcp project id
export PROJECT_ID=<your_gcp_project_id>

# build and push an image to your GCP project's container registry 
docker build . -t gcr.io/${PROJECT_ID}/kubeflow/h2o:latest
docker push gcr.io/${PROJECT_ID}/kubeflow/h2o:latest

# fill in the deployment template with your project id
sed "s/<PROJECT_ID>/${PROJECT_ID}/g" h2o-deploy.template.yaml > h2o-deploy.yaml
```

2. update the resources requested in `h2o-deploy.yaml` as appropriate

3. deploy (make sure your context is for your kubeflow cluster!)
```bash
kubectl apply -f h2o-deploy.yaml
```

4. check installation
```bash
kubectl -n h2o get all
```


---
## Connect to the Instance

- Using the service's ip `<h2o-svc cluster-ip>:54321` or 
- (preferred) domain name if you have kube-dns running `http://h2o-svc.h2o.svc:54321`


---
## Security
You may want to add network policies to secure it, should it be required.

---
## Destroy H2O Instance
To remove h2o completely, do the following:
```bash
kubeflow delete -f h2o-deploy.yaml
gcloud container images delete gcr.io/${PROJECT_ID}/kubeflow/h2o:latest
```


---
TODOs
1. turn setup into single script