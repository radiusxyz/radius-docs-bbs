# Cluster
A cluster is a unit of Radius Block Building Service initiated by a rollup executor. Once initiated, both sequencer and rollup executor can join a cluster with the corresponding cluster ID and sequencers in the cluster serve rollup executors with the same version of Liveness and Validation protocols defined by the cluster configuration. Rollup executors can belong to at most one cluster for obvious reason that it cannot build the same block with different protocols. However, sequencers can opt-in to multiple clusters at the same time. Currently, there's only a hardware limit (CPU and Memory) on how many cluster a sequencer can join at the same time.

## Liveness
Refer to the [Liveness](liveness.md) documentation for more details.

## Validation
We support two validation protocols:
- [EigenLayer](https://docs.eigenlayer.xyz/)
- [Symbiotic](https://docs.symbiotic.fi/)

Rollup executors get to decide which Liveness/Validation protocols to use. At any point an executor can initiate the cluster with  It is acceptable to have Liveness without Validation. However, Validation without Liveness is not allowed because the block validation loses its meaning if there's no other sequencer to validate the block proposed by the leader.