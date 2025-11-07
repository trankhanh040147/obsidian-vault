- [ ] Push-based vs Pull-based Gossip protocol 
- [ ] How a peer propagate message to all other peers ?

---
### Membership list
- Each node periodically selects a few random peers and shares its current membership list( list that contains identifiers per node like IP addresses or unique IDs and meta-data such as timestamps showing when each node was last seen or talked to.) with them. These peers then propagate the membership information further to other nodes in the network.
---
### Epidemic Algorithm
Epidemic algorithms, often used in the context of gossip protocols, are inspired by the spread of diseases in populations. In distributed systems, epidemic algorithms involve nodes randomly exchanging information with their peers, similar to how individuals in a population spread a contagious disease.
- ****Random Exchange of Information****:
    - In gossip protocols, each node periodically selects a few random peers to share its current state or information with.
    - These selected peers then propagate the information further to other nodes in the network.
- ****Iterative Process****:
    - The exchange of information occurs iteratively, with nodes continuously gossiping with their peers.
    - Over time, this decentralized exchange of information ensures that all nodes in the network eventually converge to a consistent view of the distributed data or system state.
- ****Achieving Eventual Consistency****:
    - By leveraging epidemic algorithms, gossip protocols ensure eventual consistency in distributed systems.
    - Despite the randomness and probabilistic nature of information dissemination, all nodes eventually reach a common understanding of the system's state.
- ****Fault Tolerance and Scalability****:
    - Epidemic algorithms play a crucial role in achieving fault tolerance and scalability in gossip protocols.
    - The decentralized and probabilistic nature of epidemic algorithms allows gossip protocols to efficiently disseminate information, even in large-scale and dynamic environments where nodes can join or leave the network unpredictably.
- ****Adaptability****:
    - Gossip protocols dynamically adapt to changes in the network topology or membership.
    - New nodes joining the network can quickly disseminate their information to other nodes, and departing nodes are eventually removed from the communication pool.

---
###
