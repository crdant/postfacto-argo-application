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
