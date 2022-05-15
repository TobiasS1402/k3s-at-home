#### Bootstrapping Flux
```
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=k3s-at-home\
  --branch=main \
  --path=./cluster/base \
  --personal
```

#### Setting up age secrets
```
age-keygen -o age.agekey

mkdir -p ~/.config/sops/age
mv age.agekey ~/.config/sops/age/keys.txt

export SOPS_AGE_KEY_FILE=~/.config/sops/age/keys.txt
source ~/.bashrc
```

#### Import the secret into the actual cluster
```
kubectl create secret generic sops-age --namespace=flux-system --from-file=age.agekey=keys.txt
```

#### Create SOPS YAML
```
creation_rules:
- encrypted_regex: '^(data|stringData)$'
  path_regex: '.*cluster.*\.yaml$'
  age: >-
    age_public_key
```

### Encrypt SOPS secrets like this
```
sops --encrypt --in-place .\cluster\base\cluster-secret-vars.yaml

sops --encrypt --in-place ./cluster/base/cluster-secret-vars.yaml
```