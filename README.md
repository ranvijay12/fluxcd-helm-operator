# fluxcd-helm-operator
sudo snap install fluxctl --classic
export FLUX_FORWARD_NAMESPACE=<flux-ns>
fluxctl list-workloads
fluctl identity  //to get public key of flux ,to put through in repo

helm repo add fluxcd https://charts.fluxcd.io

install fluxcd 
helm upgrade -i flux fluxcd/flux --wait \
--namespace fluxcd \
--set git.url=git@github.com:ranvijay12/fluxcd-helm-operator.git

kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/1.1.0/deploy/crds.yaml

helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace fluxcd \
--set git.ssh.secretName=flux-git-deploy \
--set helm.versions=v3

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








