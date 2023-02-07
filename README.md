Prereq

1. kubectx
2. helm
3. kubectl
4. jq

Steps for installation

1. Create a namespace first for vault
   `kubectl create namespace vault`
2. Add the Hashicorp helm repo
   `helm repo add hashicorp https://helm.releases.hashicorp.com`
3. Install the chart finally
   `helm install vault hashicorp/vault --namespace vault --values override-values.yml`
4. If upgrading
   `helm upgrade --install vault hashicorp/vault --namespace vault --values override-values.yml`

Steps for unsealing

1. `kubectl exec vault-0 -- vault operator init -key-shares=1 -key-threshold=1 -format=json > cluster-keys.json`
2. `VAULT_UNSEAL_KEY=$(cat cluster-keys.json | jq -r ".unseal_keys_b64[]")`
3. `kubectl exec vault-0 -- vault operator unseal $VAULT_UNSEAL_KEY`
4. Repeat above steps for the other pods

Vault UI

1. Get the root token - stored in cluster-keys.json
2. Enable port forward for UI
   `kubectl port-forward vault-0 8200:8200`
