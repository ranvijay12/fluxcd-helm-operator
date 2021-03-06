<pre>
This is to show you how u can easily install flux with helm operator and use it for GitOps. Steps are taken from official Flux Documentation with added explaination of few steps which I found a bit difficult from the official documentation.This document is only for linux users.

<b>To install flux client :</b>

sudo snap install fluxctl --classic      ## to install flux on linux

export FLUX_FORWARD_NAMESPACE=flux-ns    ## namespace of kube cluster where flux is delpoyed
 
fluxctl list-workloads 
 
fluxctl identity                         ## to get public key of flux ,to put through in repo

<b>Steps to install flux with helm operator: </b>

<b>1) install helm3:</b> 
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 && chmod 700 get_helm.sh &&./get_helm.sh
 
<b>check helm version:</b>
helm version

<b>2) add fluxcd repo in your helm:</b>

helm repo add fluxcd https://charts.fluxcd.io

<b>Pre-requisite:</b>
- create/provide namespace where flux would be deployed inside kube cluster
- create repo where you will store charts and helmrelease files 
   - helmrelease files would be stored in .yaml files 
   - flux when deployed inside kubernetes will watch sync these helmrelease in your repo, inside kube cluster and create/update these helmrelease objects 
   - you will also define charts(helm charts) inside your repo
   - this chart location is also passed in helm release files 
   - when these helmrelease objects gets created/updated ,helm opertor syncs this changes and try to update objects defined in those helm releases by passing their values in chart defined in those helm release itself
   - you can separate the chart repo from helmrelease repo or you can store both in same repo 

<b>command:</b> 
helm upgrade -i flux fluxcd/flux --wait --namespace fluxcd  --set git.url=git@github.com:ranvijay12/fluxcd-helm-operator.git  git-branch=master git-path=releases/dev

<b>arguments available in fluxcd:</b>
 args:
    - --manifest-generation=true
    - --memcached-hostname=weave-flux-memcached.flux-system
    - --memcached-service=
    - --git-poll-interval=1m
    - --sync-interval=1m
    - --ssh-keygen-dir=/var/fluxd/keygen
    - --k8s-allow-namespace=ns
    - --git-url=url
    - --git-branch=branch
    - --git-path=folder
    - --log-format=fmt
    - --registry-ecr-region=region         ##if using ecr
    - --registry-ecr-include-id=accountid  ##if using ecr
    
<b>Note:in flux v1, a flux pod in a namespace can only sync a single path and a single repo</b>

<b>Additional steps, you can ignore: </b>
if you want to receive any notifications from flux regarding your deployment, you can follow below steps:
 
<b>after flux is deployed:</b>
 
update its deployment file with an nginx side car with a ssl cert(note ssl cert is needed to secure flux) 
create an ingress pointing to flux service
use below command to reveive updates about deployment inside a namespace where flux is deployed 
 
curl -v -u fluxctl:"xxxxx" http://<domain-name-assigned-to-nginx-container>/api/flux/v6/services\?namespace\=test | jq '.[] | select(.ID=="test-env:deployment/deployment-name")'

<b>3) kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/1.1.0/deploy/crds.yaml</b>

<b>4) install helm operator for fluxcd</b>

helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace fluxcd \
--set git.ssh.secretName=flux-git-deploy \
--set helm.versions=v3

<b>5) Create chart and helm release file </b>

Create a chart in current repo   ##we have created chart named <b> webservice </b> in this repo
chart -|
       | -> templates
       | -> Chart.yaml
       | -> README.md
       | -> values.yaml
       
Create helm release in the specific ns folder to differnetiate release for every ns

releases -|
          | -> dev -> podinfo.yaml
          | -> stg -> podinfo.yaml
          | -> prd -> podinfo.yaml

Note: charts are being updated 
</pre>
