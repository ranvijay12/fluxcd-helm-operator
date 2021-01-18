# fluxcd-helm-operator
sudo snap install fluxctl --classic #optional
export FLUX_FORWARD_NAMESPACE=<flux-ns> #optional
fluxctl list-workloads #optional
fluxctl identity  //to get public key of flux ,to put through in repo

Steps:
1) install helm3 :

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 && chmod 700 get_helm.sh &&./get_helm.sh

check helm version - helm version

2) install fluxcd :

helm repo add fluxcd https://charts.fluxcd.io

Pre-requisite -
###create below namespace with in kube cluster######
#####path of below repo where ur charts and helmrelease file is stored #######
 
helm upgrade -i flux fluxcd/flux --wait --namespace fluxcd  --set git.url=git@github.com:ranvijay12/fluxcd-helm-operator.git     

after flux is deployed - update its deployment file with an nginx side car with a ssl cert 

### curl -v -u fluxctl:"xxxxx" http://domain-name-assigned-to-nginx-container/api/flux/v6/services\?namespace\=test | jq '.[] | select(.ID=="glp2-nf1:deployment/dep-name")'

use above command to get deployment status .

3) kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/1.1.0/deploy/crds.yaml

4) install helm operator for flucd

helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace fluxcd \
--set git.ssh.secretName=flux-git-deploy \
--set helm.versions=v3

5) Create chart and helm release file 

Create a chart in current repo 
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







