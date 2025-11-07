https://www.youtube.com/watch?v=d7nAGI_NZPk
## What is Paxos
### Paxos is a family of distributed algorithms used to reach consensus 
- **family**: there are vanilla and other variants
- **distributed**: run in a set of computers or nodes
### What is to reach consensus ? 
- consensus is agreeing on one result
- once a majority agrees on a proposal, that is consensus
- the result (the reached consensus) can be eventually known by everyone
![[Pasted image 20250825115709.png]]
### Why need to reach consensus
- Infinite powerful and scalable single computer is not possible 
- So, either [[Leader-replicas schema]]
	- When write value, replica will talk to leader
	- Then the leader will apply on all replicas
	- If the leader is unavailable, nodes must reach consensus to elect a new one
- Or [[peer-to-peer schema]]:
	- Nodes need to reach consensus continuosly to guarantee consistency 