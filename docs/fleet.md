# Rancher Fleet

> Fleet is a container management and deployment engine designed to offer users more control on the local cluster and constant monitoring through GitOps

:octicons-cross-reference-24: **Links:**
[:material-web: fleet.rancher.io](https://fleet.rancher.io/),
[:simple-github: repository](https://github.com/rancher/fleet)

## Quick start

### Getting to know Fleet

* Youtube videos
    * [GitOps The Planet (E4) - GitOps at Edge](https://www.youtube.com/watch?v=OPbgvBSAO9U) - January 4, 2023
    * [Using Fleet to Manage Clusters at Scale](https://www.youtube.com/watch?v=8gXbxt3AjdE&t=723s) - November 21, 2020

### Install Fleet objects in your Kubernetes cluster

```bash
# sets version
FLEET_VERSION=0.3.9

# (optional) views objects that will be created
helm template fleet-crd https://github.com/rancher/fleet/releases/download/v${FLEET_VERSION}/fleet-crd-${FLEET_VERSION}.tgz > temp/fleet-crd.yaml
helm template -n cattle-fleet-system fleet https://github.com/rancher/fleet/releases/download/v${FLEET_VERSION}/fleet-${FLEET_VERSION}.tgz > temp/fleet.yaml

# installs
helm -n cattle-fleet-system install --create-namespace --wait fleet-crd https://github.com/rancher/fleet/releases/download/v${FLEET_VERSION}/fleet-crd-${FLEET_VERSION}.tgz
helm -n cattle-fleet-system install --create-namespace --wait fleet https://github.com/rancher/fleet/releases/download/v${FLEET_VERSION}/fleet-${FLEET_VERSION}.tgz

# makes sure all containers are running fine
kubectl get all -n cattle-fleet-system
```

### Use examples repo (or your own) to deploy your first applications

```bash
# creates sample namespace
kubectl create namespace fleet-sample
# creates or updates the sample gitrepo
kubectl apply -f samples/gitrepo-guestbook-sample.yaml -n fleet-sample
# views fleet action
kubectl get fleet -n fleet-sample
```

## Configuration

* [Git Repository Contents](https://fleet.rancher.io/gitrepo-content)
    * [`fleet.yaml`](https://fleet.rancher.io/ref-fleet-yaml)

## Samples

* [Fleet examples](https://github.com/rancher/fleet-examples)
* [Martin Weiss](https://github.com/Martin-Weiss/rancher-fleet)
* [SUSE Exchange Paris 2023](https://github.com/devpro/helm-charts/tree/main/samples/suse-exchange-paris-2023)

## Alternatives

Name       | Model
---------- | ----
**ArgoCD** | Push
**Fleet**  | Pull
**Flux**   | Pull

## Limitations

### Helm chart dependencies

* tgz files in charts folder (coming from `helm dependency update`) must be added to git to be picked up by Fleet (see [issue #250](https://github.com/rancher/fleet/issues/250))

### Helm chart with objects on multiple namespaces

* You may encounter the issue `Error while running post render on files: invalid cluster scoped object [name=* kind=PodSecurityPolicy apiVersion=policy/v1beta1] found, consider using "defaultNamespace", not "namespace" in fleet.yaml`

### Modified GitRepos

* [Generating Diffs to Ignore Modified GitRepos](https://fleet.rancher.io/bundle-diffs)

## Troubleshoot

### Fleet Kubernetes objects

Kind                                      | Name
----------------------------------------- | ------------------------
bundles.fleet.cattle.io                   | CustomResourceDefinition
bundledeployments.fleet.cattle.io         | CustomResourceDefinition
bundlenamespacemappings.fleet.cattle.io   | CustomResourceDefinition
clustergroups.fleet.cattle.io             | CustomResourceDefinition
clusters.fleet.cattle.io                  | CustomResourceDefinition
clusterregistrationtokens.fleet.cattle.io | CustomResourceDefinition
gitrepos.fleet.cattle.io                  | CustomResourceDefinition
clusterregistrations.fleet.cattle.io      | CustomResourceDefinition
gitreporestrictions.fleet.cattle.io       | CustomResourceDefinition
contents.fleet.cattle.io                  | CustomResourceDefinition
imagescans.fleet.cattle.io                | CustomResourceDefinition
gitjobs.gitjob.cattle.io                  | CustomResourceDefinition
gitjob                                    | ServiceAccount
fleet-controller                          | ServiceAccount
fleet-controller-bootstrap                | ServiceAccount
fleet-controller                          | ConfigMap
gitjob                                    | ClusterRole
fleet-controller                          | ClusterRole
fleet-controller-bootstrap                | ClusterRole
gitjob-binding                            | ClusterRoleBinding
fleet-controller                          | ClusterRoleBinding
fleet-controller-bootstrap                | ClusterRoleBinding
fleet-controller                          | Role
fleet-controller                          | RoleBinding
gitjob                                    | Service
gitjob                                    | Deployment
fleet-controller                          | Deployment

### Git token expired

* If you manage the GitRepo from Rancher UI and create new git credential, save twice the UI
