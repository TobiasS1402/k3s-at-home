#### Creating serviceaccount for remote management
```
$ cat admin-sa.yml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: k3sadmin
  namespace: kube-system

$ kubectl apply -f admin-sa.yml

$ cat admin-rbac.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: k3sadmin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: k3sadmin
    namespace: kube-system

$ kubectl apply -f admin-rbac.yml

$ kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep k3sadmin | awk '{print $1}')
```

#### Connecting to the cluster
```
$ kubectl config set-cluster kubernetes --server=https://192.168.0.110:6443 --insecure-skip-tls-verify=true
$ kubectl config set-context k3s --cluster=k3s
$ kubectl config set-credentials k3sadmin --token=<token from setup>
$ kubectl config set-context k3s --user=k3sadmin
$ kubectl config use-context k3s

```

#### Bootstrapping Flux
```sh
flux bootstrap github --owner=TobiasS1402 --repository=k3s-at-home --branch=main --path=./cluster/base --personal
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
kubectl create secret generic sops-age --namespace=flux-system --from-file=keys.txt
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

### Decrypt SOPS secrets like this
```
sops --decrypt --in-place .\cluster\base\cluster-secret-vars.yaml
```

### Add secret source
```
flux create source git my-secrets --url=https://github.com/TobiasS1402/k3s-at-home --branch=main 

flux create kustomization my-secrets --source=my-secrets --path=./cluster--prune=true --interval=10m --decryption-provider=sops --decryption-secret=sops-age 
```