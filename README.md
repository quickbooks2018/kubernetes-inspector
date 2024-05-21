# Kubernetes Inspector Gadget

- https://inspektor-gadget.io/

- Installation

- https://inspektor-gadget.io/docs/v0.28.1/getting-started/install-kubernetes/

- Pre-requisites

- Install krew https://krew.sigs.k8s.io/docs/user-guide/setup/install/
```bash
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)


export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"

kubectl krew 
```

- Install inspektor-gadget
```bash
kubectl krew install gadget
```

- Deploy inspektor-gadget
```bash
kubectl gadget deploy
```

- Important commands & help
```bash
kubectl gadget --help
kubectl gadget trace --help
```

- For example, we can ask Inspektor Gadget to monitor network traffic in the a-team namespace and store the output in network.json file

```bash
kubectl gadget advise network-policy monitor --namespace a-team \
    --output network.log &

pkill kubectl

cat network.log | jq .
```

- Another interesting feature is snapshot process which, in a way, is equivalent to the ps command, except that it looks for processes inside containers wrapped in Pods.

```bash
kubectl gadget snapshot process --namespace a-team
```

- We can also execute top command which, just as in Linux, shows which processes use the most resources, except that, this time, we’ll limit those to eBPF.
    
```bash
kubectl gadget top ebpf
```

- Network bites use by the container
```bash
kubectl gadget top file --namespace argocd
kubectl gadget top file -A
```

- Note: kubescape uses at the backend uses some of the features of inspektor-gadget