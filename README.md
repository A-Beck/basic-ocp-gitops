# Basic OpenShift GitOps Helm application

This repository contains a small Helm chart for an Apache httpd welcome page and two Argo CD `Application` manifests. The blue and green values files set both the page text and its background/text color.

## Layout

```text
argocd/
  blue-application.yaml
  green-application.yaml
charts/webapp/
  Chart.yaml
  values.yaml
  values-blue.yaml
  values-green.yaml
  templates/
    _helpers.tpl
    configmap.yaml
    deployment.yaml
    route.yaml
    service.yaml
```

Edit `repoURL` in both files under `argocd/` to the Git URL for this repository. Then apply one or both `Application` manifests to the namespace where OpenShift GitOps runs (commonly `openshift-gitops`):

```sh
oc apply -n openshift-gitops -f argocd/blue-application.yaml
oc apply -n openshift-gitops -f argocd/green-application.yaml
```

Each application creates its target namespace and deploys the chart from `charts/webapp`. The application names and target namespaces are `basic-ocp-blue` and `basic-ocp-green`. The chart creates a ConfigMap containing `/var/www/html/index.html`, an httpd Deployment, a Service, and an OpenShift Route. A ConfigMap checksum on the pod template triggers a rollout when the page changes.

For a direct Helm install, run `helm upgrade --install webapp ./charts/webapp -n basic-ocp-blue --create-namespace -f ./charts/webapp/values-blue.yaml` (replace the values file and namespace with the green pair for the green page).
