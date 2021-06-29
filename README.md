

**Table of Contents**

## First part optional
```shell
minikube start --cpus 4 --memory 8192 docker-env --disk-size=80GB
minikube profile list
|----------|-----------|---------|--------------|------|---------|---------|-------|
| Profile  | VM Driver | Runtime |      IP      | Port | Version | Status  | Nodes |
|----------|-----------|---------|--------------|------|---------|---------|-------|
| minikube | parallels | docker  | 10.211.55.27 | 8443 | v1.20.2 | Running |     1 |
|----------|-----------|---------|--------------|------|---------|---------|-------|
```


## Enable Internal Container Registry and Registry Aliases

```shell
minikube addons enable registry-aliases
minikube addons enable registry
    ▪ Using image quay.io/rhdevelopers/core-dns-patcher
    ▪ Using image alpine:3.11
    ▪ Using image gcr.io/google_containers/pause:3.1
🌟  The 'registry-aliases' addon is enabled
    ▪ Using image registry:2.7.1
    ▪ Using image gcr.io/google_containers/kube-registry-proxy:0.4
🔎  Verifying registry addon...
🌟  The 'registry' addon is enabled
```

# minikube addon list
```shell
minikube addons list

|-----------------------------|----------|--------------|
|         ADDON NAME          | PROFILE  |    STATUS    |
|-----------------------------|----------|--------------|
| ambassador                  | minikube | disabled     |
| auto-pause                  | minikube | disabled     |
| csi-hostpath-driver         | minikube | disabled     |
| dashboard                   | minikube | disabled     |
| default-storageclass        | minikube | enabled ✅   |
| efk                         | minikube | disabled     |
| freshpod                    | minikube | disabled     |
| gcp-auth                    | minikube | disabled     |
| gvisor                      | minikube | disabled     |
| helm-tiller                 | minikube | disabled     |
| ingress                     | minikube | disabled     |
| ingress-dns                 | minikube | disabled     |
| istio                       | minikube | disabled     |
| istio-provisioner           | minikube | disabled     |
| kubevirt                    | minikube | disabled     |
| logviewer                   | minikube | disabled     |
| metallb                     | minikube | disabled     |
| metrics-server              | minikube | disabled     |
| nvidia-driver-installer     | minikube | disabled     |
| nvidia-gpu-device-plugin    | minikube | disabled     |
| olm                         | minikube | disabled     |
| pod-security-policy         | minikube | disabled     |
| registry                    | minikube | enabled ✅   |
| registry-aliases            | minikube | enabled ✅   |
| registry-creds              | minikube | disabled     |
| storage-provisioner         | minikube | enabled ✅   |
| storage-provisioner-gluster | minikube | disabled     |
| volumesnapshots             | minikube | disabled     |
|-----------------------------|----------|--------------|
```

## Link for the installation of FLUX https://fluxcd.io/docs/installation/

## install flux on Mac
```shell
brew install fluxcd/tap/flux
brew install kustomize
```

## install flux on Windows
```shell
1. download the file https://github.com/fluxcd/flux2/releases/download/v0.15.3/flux_0.15.3_windows_amd64.zip 

2. create a new path into "Program Files x86"
3. unzip the file content of flux_0.15.3_windows_amd64.zip into the folder created
4. add the PATH into Windows PATH
```

## install flux on Linux
```shell
curl -s https://fluxcd.io/install.sh | sudo bash

# enable completions in ~/.bash_profile
. <(flux completion bash)
```

## Open a command shell and check if flux is working
```shell
flux check --pre


Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
Updated 1 formula.

==> Installing flux from fluxcd/tap
==> Downloading https://github.com/fluxcd/flux2/releases/download/v0.13.1/flux_0.13.1_darwin_amd64.tar.gz
==> Downloading from https://github-releases.githubusercontent.com/258469100/d3d9a980-a423-11eb-9ead-25f455a1f24e?X-Amz-Algorithm=AWS4-HMAC-SHA256&X
######################################################################## 100.0%
Info: flux 0.13.0 is already installed
To upgrade to 0.13.1, run:
  brew upgrade fluxcd/tap/flux
Warning: kustomize 4.1.2 is already installed and up-to-date.
To reinstall 4.1.2, run:
  brew reinstall kustomize
► checking prerequisites
✗ flux 0.13.0 <0.13.1 (new version is available, please upgrade)
✔ kubectl 1.19.0 >=1.18.0-0
✔ Kubernetes 1.20.2 >=1.16.0-0
✔ prerequisites checks passed


```

# flux bootstrap github

```shell

flux bootstrap github \
--owner=fsbaraglia \
--repository=sre-gitops \
--path=dev-cluster \
--branch=master \
--personal=true \
--private=true


► connecting to github.com
► cloning branch "master" from Git repository "https://github.com/fsbaraglia/sre-gitops.git"
✔ cloned repository
► generating component manifests
✔ generated component manifests
✔ committed sync manifests to "master" ("42b314f1cc795c1a5dc65361146bb4e83a4cb1a9")
► pushing component manifests to "https://github.com/fsbaraglia/sre-gitops.git"
► installing toolkit.fluxcd.io CRDs
◎ waiting for CRDs to be reconciled
✔ CRDs reconciled successfully
► installing components in "flux-system" namespace
✔ installed components
✔ reconciled components
► determining if source secret "flux-system/flux-system" exists
✔ source secret up to date
► generating sync manifests
✔ generated sync manifests
✔ committed sync manifests to "master" ("730785e6c9c3e3d7c4442706d4f3d57d88226ca9")
► pushing sync manifests to "https://github.com/fsbaraglia/sre-gitops.git"
► applying sync manifests
✔ reconciled sync configuration
◎ waiting for Kustomization "flux-system/flux-system" to be reconciled
✔ Kustomization reconciled successfully
► confirming components are healthy
✔ source-controller: deployment ready
✔ kustomize-controller: deployment ready
✔ helm-controller: deployment ready
✔ notification-controller: deployment ready
✔ all components are healthy
```

## adding docker auth to minikube (Optional only for private repo)
```shell
docker login

cat ~/.docker/config.json

kubectl create secret generic regcred \
--from-file=.dockerconfigjson=~/.docker/config.json \
--type=kubernetes.io/dockerconfigjson
secret/regcred created

export REGISTRY_SERVER=https://index.docker.io/v1/
export REGISTRY_USER=xxx
export REGISTRY_PASS=xxx
export REGISTRY_EMAIL=xxxx

kubectl create secret docker-registry regcred \
--docker-server=$REGISTRY_SERVER \
--docker-username=$REGISTRY_USER \
--docker-password=$REGISTRY_PASS \
--docker-email=$REGISTRY_EMAIL \
--namespace=cybersec-chaos

kubectl create secret docker-registry regcred \
--docker-server=$REGISTRY_SERVER \
--docker-username=$REGISTRY_USER \
--docker-password=$REGISTRY_PASS \
--docker-email=$REGISTRY_EMAIL \
--namespace=default

kubectl create secret docker-registry regcred \
--docker-server=$REGISTRY_SERVER \
--docker-username=$REGISTRY_USER \
--docker-password=$REGISTRY_PASS \
--docker-email=$REGISTRY_EMAIL \
--namespace=monitoring
```

## let's check the flux source configuration

```shell

% flux get sources git
NAME       	READY	MESSAGE                                                          	REVISION                                       	SUSPENDED
flux-system	True 	Fetched revision: master/e630bb6e65d9d10957e24046801ec2fbbcca59d5	master/e630bb6e65d9d10957e24046801ec2fbbcca59d5	False

flux reconcile ks flux-system
► annotating Kustomization flux-system in flux-system namespace
✔ Kustomization annotated
◎ waiting for Kustomization reconciliation
✔ Kustomization reconciliation completed
✔ applied revision master/730785e6c9c3e3d7c4442706d4f3d57d88226ca9
```


# adding image build
## Image Automation controller isn’t installed by default (since it’s an alpha feature) et you need to reconfigure Flux. ## The flux bootstrap command is idempotent and you can run it again with the new parameters:

```shell
flux bootstrap github \
--owner=fsbaraglia \
--components-extra=image-reflector-controller,image-automation-controller --repository=sre-gitops \
--path=dev-cluster \
--branch=master \
--personal=true \
--private=true
```

# play with flux
```shell
-> suspend flux
flux suspend ks flux-system
► suspending kustomizations flux-system in flux-system namespace
✔ kustomizations suspended

-> resume flux
flux resume ks flux-system

-> push to download changes
flux reconcile ks flux-system
```
