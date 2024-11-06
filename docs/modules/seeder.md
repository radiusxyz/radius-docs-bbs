# Seeder
Seeder is responsible for managing the sequencer RPC URL. As a simple key-value storage, it stores the sequencer address as a key and corresponding RPC URLs as a value. A sequencer who wishes to register on the Seeder must be registered on Liveness Contract prior to registering on the Seeder.

## Registration
When registering, a sequencer sends a signed message of its address, external RPC URL, cluster RPC URL and the cluster ID it receives as a result of registering on Liveness contract. 

> External RPC URL handles user transactions, whereas cluster RPC URL is only for inter-cluster messages whose signature must be verified.

When Seeder receives the registration request, it first verifies the message signature, proceeds to check if the address is registered on Liveness Contract and finally, checks if the external RPC URL of the requesting sequencer is accessible via '/health' endpoint. Only after these procedures a sequencer address and its RPC URLs are registered on Seeder and become available for other entities such as Secure RPC, Sequencer and Rollups.

## Deregistration
A sequencer sends a signed message of its address of its address and the cluster ID it belongs to. Seeder verifies the message signature and checks if the address is deregistered from Liveness Contract. Seeder simply removes the address of the sequencer, making its RPC URLs unavailable afterwards.