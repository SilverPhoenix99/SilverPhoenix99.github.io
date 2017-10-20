---
title:  "Distributed Tree Construction"
date:   2008-11-16 13:11:00 GMT
tags:   protocol "distributed algorithm" "epidemic algorithm" "gossip algorithm" "rumor algorithm" "tree algorithm"
---
During this semester, at university, I had a project on [Epidemic Algorithms](http://en.wikipedia.org/wiki/Gossip_protocol) (a.k.a. Gossip or Rumor Algorithms) on a dynamic system, where new nodes enter and existing nodes leave (or crash) from the system. One challenge was to find the average of the system, and I thought that a distributed tree could solve that problem.

One additional restriction was that each node could not know more than 1% of the total system. This rule is what sets this protocol apart from the other existing (and more common) ones.

According to the protocol I implemented, the parents are responsible for accumulating the information of their children, and by the previous restriction, there is a need for the parent to limit the number of children it has.

The protocol consists on three types of messages:
- Parent Query Message
- Child Accept Message
- Parent Refuse Message

For the protocol to work some requirements come to order:
- Each node needs a unique identifier. A GUID, an IP, or a MAC address should suffice. This will serve as a tie breaker in root choosing. The concept of “better root” is relative and any concept of uniqueness can be used. In the project I chose minimum id.
- Communications don't fail, i.e., the child didn't exit, but was unable to communicate with the parent or vice versa.

Initially all nodes consider themselves as a root. From time to time, a node sends a Parent Query Message, initiating the protocol. This message indicates that the node is searching for a parent (the construction of the tree is bottom-up). If the receiver node is full (no more space for new children) or already contains the sending node as child, then the node discards the message. If it isn't full then, the receiver node will check which root is best, the one it currently has or the one received in the message. In case the root it has is the best it adds the sender as a child and sends back a Child Accept Message. On the other hand, if the root sent in the message is better (and not the same), then it sends back a Parent Query Message, reversing the roles of parent/child.

A Child Accept Message, indicates the node was accepted as a child of a node. It also implies that it already sent a Parent Query Message.

Since some events might have occurred between sending the Parent Query and the Child Accept messages, the node has to confirm if it will link with the new parent. If the receiver (new child) node had already found a better parent (with better root), it will send a Parent Reject Message, else it will register the new parent and new root, if it isn't already (a node initiates the protocol randomly, so it might have sent a Parent Query more than once to the same node).

The Parent Reject Message serves to inform a parent to discard any information about a child that it might have.

Any other protocol (like the average finding one), working over the tree protocol, needs to communicate in both directions, up and down.

When going down, in case the child fails to report to the parent, the parent can assume a Parent Reject Message and remove any stats it has of that child. When going up the node resets itself has a root, resetting any stats it needs, and resumes protocol in Parent Query to link itself to another tree.

Here's some pseudo-code:

```
Fields:
    UID root
    Node parent
    List children // Size <= 1% of system size
    List knownNodes // Size <= 1% of system size

Initialize()
{
    root = getMyID()
    parent = this
    populate(knownNodes)
    children = createList()

    resetStats()
}

Periodically call:
LinkTree()
{
    Node n = random(knownNodes)
    send(n, ParentQuery(root))
}

OnReceive(Node sender, ParentQuery msg)
{
    if(not isFull(children) and not contains(children, sender))
    {
        if(isBetter(msg.Root, root))
        {
            add(children, sender)
            send(sender, ChildAccept(root))
        }
        else if(not equal(msg.Root, root))
        {
            send(sender, ParentQuery(root))
        }
    }
}

OnReceive(Node sender, ChildAccept msg)
{
    if(isBetter(msg.Root, root))
    {
        if(not equal(parent, this))
        {
            send(parent, ParentRefuse())
        }

        parent = sender
        root = msg.Root
    }
    else // I already have a better parent
    {
        send(sender, ParentRefuse())
    }
}

OnReceive(Node sender, ParentRefuse msg)
{
    remove(children, sender)
    removeStats(sender)
}

OnFailure(Node receiver)
{
    remove(knownNodes, receiver)

    if(equal(parent, receiver))
    {
        root = getMyID()
        parent = this
        resetStats()
    }
    else
    {
        remove(children, receiver)
        removeStats(receiver)
    }
}
```
