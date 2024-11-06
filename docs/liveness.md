# Liveness
Radius Liveness is a decentralization protocol followed by sequencers in a given [cluster](cluster.md) to provide highly available block-building solution.

## Decentralization
The purpose of Liveness is to provide decentralized block-building service to rollup executors in the same cluster. The cryptographic approach employed by the system ensures a trustless environment for the sequencer operation. However, the architecture still faces the challenge of a Single Point of Failure (SPOF). To mitigate this risk, Radius adopts a distributed sequencer network. This network comprises multiple sequencers operating concurrently to ensure system reliability and continuity.

In the event of a sequencer failure, the distributed nature of the network allows the remaining sequencers to continue to build blocks without interruption. This redundancy enhances the robustness of the system against individual node failures.

Implemented with Smart Contract on Ethereum, Radius uses Ethereum as a [service discovery](https://glossary.cncf.io/service-discovery/). Sequencers are required to register on Liveness Contract and [Seeder](modules/seeder.md) with a cluster ID to be able to build and suggest a block for rollup executors in the same cluster.

## Leader Selection
There are two arguments to selecting a leader:
- L1 Block Number: Provides access to the instance of the proposer list stored on Ethereum for a given block number.
- Rollup Block Number: The number of the rollup's next block.
Rollup

The leader index is computed as following modular operation:
index = rollup block number % number of sequencers in the list stored on Ethereum for a given block number.

It is important to note that rollup executors must provide these arguments as a signed message and send to any of the sequencer in the cluster prior to getting a block proposed by the leader. This is called "build_block" request. After message signature verification, the message gets propagated to other sequencers in the cluster. This has the same effect of leader election.

### Safety Margin
Another important aspect of Liveness Contract is ***Safety Margin***. The danger of using the latest block is that sequencers may not sync properly. While it is safer to use the previous block number, there are chances that rollup executors may not update L1 block number arguments, resulting in the sequencer list remaining the same. This is why Radius Liveness protocol implemented Safety Margin within which rollup executors must pick previous L1 block number.

## Syncing
### Normal Operation
A well functioning leader proposer consistently shares its state with the followers in a concurrent manner, who then synchronize their states to match that of the leader. This synchronization process ensures consistency across the network.

### Failover Operation
When the leader proposer becomes unresponsive or fails, the following process ensures continuity and proper synchronization within the system:

- Leader Recovery and Request: Upon recovery, the leader proposer makes a request to all other proposers to check if any of them have received the "build block" request from the rollup.

- No Build Block Request Received: If no other proposer has received the "build block" request from the rollup, the leader proposer resumes its leadership role.

- Build Block Request Received by Follower: If another proposer has received the build block request, the recovered proposer will synchronize its block height with that of the new leader proposer, as the leadership role has been transferred to it.

This process ensures that the leader proposer can either continue its role seamlessly or synchronize appropriately with the follower to maintain the integrity and continuity of the system.

This mechanism ensures that the network remains robust and consistent, aligning the states of all nodes with the selected leader's state, whether it includes the latest transactions and commitments or necessitates a rollback to maintain network integrity.

## Advantages
- Encourages Participation: Every sequencer gets a chance to become a leader at some point in a round-robin fashion and earn rewards.

- Promotes Decentralization: Enhances decentralized sequencing even with a leader-based system. Although a single entity orders transactions for each block, the frequent rotation of leaders reduces centralization.

- Ensures Consistent Leader Selection: Eliminates the need for voting or trust among nodes. Since the contract block number and rollup block number are shared with all nodes, the calculation of the next leader will consistently produce the same result for everyone.

## Disadvantages
- Round-Robin Leader Selection: Because each sequencer becomes a leader in a round-robin fashion, unresponsive but still registered node will persistently yield an empty block every time it becomes the leader. To prevent this problem, we are planning to implement a voting mechanism to evict consistently malfunctioning sequencer on the contract level.
- Fixed Leader For a Given Rollup Block: When the leader sequencer fails, no other sequencer can assume the role of the leader. Therefore, user transactions coming in after the point of failure are not included in the block.