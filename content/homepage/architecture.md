---
title: 'Architecture'
weight: 3
header_menu: true
---
Let's get to it. Why Kubernetes? What's CI/CD? Are you some sort of sucker for punishment?

Here's how the pool is setup:

![Kubernetes-based staking pool design](images/ada-staking-pool-architecture.png)

Let's review the components:

1. We run 2 x relays in a statefulset. Relays can take a _long_ time to restart, and without a relay, the block producer can't produce, so we want to ensure we always have at least one relay. Statefulsets support rolling updates, meaning any change to config / container image applied by CI/CD will be applied to one of the two relays, and only when that relay is fully synced again, will the change be applied to the _other_ relay.
2. Similarly, we run our block producer as a statefulset. The stateful set isn't strictly necessary, since we only have a single block producer, but it keeps the helm chart simpler to use statefulsets for both the producer and the relays. If there were every a way to run multiple producers (I understand that's forbidden ATM), then switching to an HA design would simply be a matter of updating `replicas: 1` to `replicas: 2` and pushing a helm update.
3. In Kubernetes' microservices architecures, pods/containers rarely talk directly to other pods/containers. Inter-microservice communication is facilitated using **services**. The service is a simple round-robin load balancer, allowing you to scale up various components without having to alter other components. The relay nodes communicate with the producer node via the service. 
4. Similarly, the relay nodes are only accessed via their own service. The producer node's topology config only permits it to connect to a single host, being the relay service. The service takes care of ensuring the incoming connection is passed to one of the two pods in the relay statefulset, and uses readiness probes to ensure that only fully-synced pods are available as backend endpoints
5. The relay service is "exposed" as a LoadBalancer service, meaning it's assigned a public IP by DigitalOcean. All incoming connections ingress the load balancer, then hit the service, and then are directed to the applicable pod