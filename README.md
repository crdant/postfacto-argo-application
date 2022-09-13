# GitOps with ArgoCD, Helm, and Replicated KOTS

This repository contains the code and configuration needed to 
demonstrate GitOps deployment of a [KOTS](https://kots.io) 
app with [ArgoCD](https://argo-cd.readthedocs.io/en/stable/).

## Scenario

The demonstration considers a context in which a team is using
Replicated KOTS to distribute their application and a customer
requires a GitOps deployment using ArgoCD. The current 
iteration assumes that ArgoCD is running in a different cluster
from the workload, but works with a single cluster as well. A
simpler single cluster version is at the tag 
[`single-cluster-demo`](https://github.com/crdant/postfacto-argo-application/releases/tag/single-cluster-demo).

The demo application I've used is 
[Postfacto](https://pivotal.github.io/postfacto/), a tool for 
team retrospectives developed by 
[Pivotal Labs](https://en.wikipedia.org/wiki/Pivotal_Labs). I
built [a new helm chart](https://github.com/crdant/postfacto-chart)
for Postfacto, [original](https://github.com/pivotal/postfacto/tree/master/deployment/helm)
didn't have everything I needed for my own install of Postfacto.
The new chart also helped me externalize some secrets so I
could use [sealed secrets](https://github.com/bitnami-labs/sealed-secrets)
in this repo. 

I've combined a few different features of Replicated and ArgoCD to 
deliver this demo.

* Preflight checks built with [Troubleshoot](https://troubleshoot.sh) 
  to assure my workload cluster is ready to run the application
* A [KOTS application](https://github.com/crdant/postfacto-replicated) 
  that packages the Postfacto Helm chart for distribution with
  Replicated.
* The Replicated private registry with a 
  [custom hostname](https://docs.replicated.com/vendor/packaging-private-registry-cname)
  _(alpha feature)_.
* Replicated support for [installing with Native Helm](https://docs.replicated.com/vendor/helm-installing-native-helm)
  _(alpha feature)_.
* ArgoCD [resource hooks](https://argo-cd.readthedocs.io/en/stable/user-guide/resource_hooks/)
  to assure that my preflight checks run before the application installs.

## Running the demonstration

To run the demonstration, you will need to fork this repository.
I hate having to ask you that, but it's required since my cluster
address and sealed secrets woon't work with your repository. You'll
customize these and commit them to your fork.

You'll also need two Kubernetes clusters, one with ArgoCD installed
and another to deploy the application. I had the clusters already 
configured using [TKG Lab](https://github.com/Tanzu-Solutions-Engineering/tkg-lab) 
and it's [optional ArgoCD lab](https://github.com/Tanzu-Solutions-Engineering/tkg-lab/blob/main/docs/bonus-labs/argocd-kustomize.md).
A later revision will enable setting those clusters up with 
[kurl](https://kurl.sh). You'll also need to add 
[sealed secrets](https://github.com/bitnami-labs/sealed-secrets) to
both of your clusters.

The last thing you'll need is access to the Postfacto application as
a Replicated KOTS application. Contact me for a license to use
my release, or you can use my [chart](https://github.com/crdant/postfacto-chart)
and [application](https://github.com/crdant/postfacto-replicated) to roll with 
your own with a [vendor account](https://vendor.replicated.com) from 
Replicated.

### Steps

At a high-level, you're going to fork this repo, make sure the manifests
refer to your fork, reseal your secrets, and tell Argo to do it's thing.
