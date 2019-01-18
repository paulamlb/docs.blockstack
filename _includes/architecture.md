
![Blockstack Architecture](/common/images/architecture.png)

Blockchains require consensus among large numbers of people, so they can be slow. Additionally, a blockchain is not designed to hold a lot of data. This means using a blockchain for every bit of data a user might write and store is expensive. For example, image if an application were storing every tweet in the chain.

Blockstack addresses blockchain performance problems by taking a layered approach. At the base of the system is a blockchain and the Blockstack Naming System(BNS). The blockchain  governs ownership of names (identities)  in the system, names such as domain names, username, and applications names.

Names in Blockstack correspond to routing data in the OSI stack. The routing data is stored in the Atlas Peer Network, the second layer. Every core node that joins the Blockstack Network is able to obtain an entire copy of this routing data. Blockstack uses the routing data to associate names (usernames, domains, application names) with a particular storage location.

The final layer is the Gaia Storage System. Gaia works by hosting a _Gaia hub_ in one or more existing storage systems.
These storage systems typically belong to storage providers such as Dropbox,
Amazon EC2, and so forth. Blockstack currently has driver support for S3 and
Azure Blob Storage, but the driver model allows for other backend support as
well.

Because Gaia stores application and user data off the blockchain, Blockstack
DApp is typically more performant than DApps created on other blockchains.
Moreover, users choose where their data lives, and Gaia enables applications
to access that data via a uniform API. When the user logs in,
the authentication process gives the application the URL of a Gaia hub, which
then does writes on behalf of that user.
