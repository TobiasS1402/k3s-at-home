<div align="center">

<img src="https://camo.githubusercontent.com/5b298bf6b0596795602bd771c5bddbb963e83e0f/68747470733a2f2f692e696d6775722e636f6d2f7031527a586a512e706e67" align="center" width="144px" height="144px"/>

### My home operations repository

</div>

<br/>

<div align="center">

---

## :book:&nbsp; Overview

This repository _is_ my home Kubernetes cluster in a declarative state.
[Flux](https://github.com/fluxcd/flux2) watches my [cluster](./cluster) directory and makes the changes to my cluster based on the YAML manifests.

This repository is built off the [k8s-at-home/template-cluster-k3s](https://github.com/budimanjojo/home-cluster), which is based on the [k8s-at-home/template-cluster-k3s](https://github.com/k8s-at-home/template-cluster-k3s) repository but reconfigured to not use helm at all.

---
