# Weibuddies IaC

The makefile in the root project has some commands but here are some more helpful ones.

```bash
# See which k8s cluster you're using (i.e. the one skaffold is going to use)
kubectl config current-context

# Find the loadbalancer IP
kubectl -n ingress-nginx get services -o wide -w ingress-nginx-controller

# If you need to install ingress-nginx
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace

# If you want to use helm for sealedSecrets
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets

# you can create an encrypted secrets file using this
kubectl create secret generic mysecret --dry-run=client --from-literal foo=bar --output json | kubeseal | tee mysecret.yaml

# To do a git pull on the submodule and grab the commit
git pull --recurse-submodules

# To grab the latest changes from a git submodule
cd weibuddies-iac && git pull origin main
git submodule update --remote --merge # shorthand
```