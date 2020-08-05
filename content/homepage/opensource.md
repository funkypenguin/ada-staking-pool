---
title: 'Open Source'
weight: 4
header_menu: true
---
The pool is developed "in the open", and all the components are publically available, in the following repositories:

1. [Docker image](https://github.com/geek-cookbook/cardano-node-docker) for running the nodes, forked from [abracadaniel/cardano-node-docker](https://github.com/abracadaniel/cardano-node-docker) and tweaked for better Kubernetes behaviour
2. [Helm chart](https://github.com/geek-cookbook/helm-ada-stake-pool) for deploying the pool
3. [Single-page site for pool information](https://github.com/funkypenguin/ada-staking-pool), built using Hugo and the [hugo-scroll theme](https://github.com/janraasch/hugo-scroll/)
4. GitHub actions to deploy updates to the pool when changes to the repo are made