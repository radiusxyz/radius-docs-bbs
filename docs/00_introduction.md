# Radius BBS (Block Building Solution)

## Introduction
Radius block building solution for rollups consists of the following modules:
- Secure RPC
- Distributed Key Generator
- Sequencer
- Seeder
  
We call it module because each does a specific job(s) to achieve our goal: enriching rollup economy and providing a new source of income by reinvesting captured MEV in rollups. To better understand how each module works, we need to view the system from the perspective of a transaction, which we describe as a transaction journey. This guide is not intended to be technical. With all complex implementations under the hood, let's find out how we achieve, alongside our original goal, protection of user transactions (e.g. Harmful MEVs, Sandwich attacks, front-running and etc). 