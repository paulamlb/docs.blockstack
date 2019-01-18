---
layout: storage
permalink: /:collection/:path.html
---
# A decentralized storage architecture

The Blockstack Network stores application data using a storage service called
Gaia. Transactional metadata is stored on the Blockstack blockchain and user
application data is stored in Gaia hub. Storing data off of the blockchain
ensures that Blockstack applications can provide users with high-performance and
high availability for data reads and writes without introducing central trust
parties.

* TOC
{:toc}

##  Understand Gaia in the Blockstack architecture

The following diagram depicts the Blockstack architecture and Gaia's place in it:

{% include architecture.md %}

## User control or how is Gaia decentralized?

Within the provider's storage system a Gaia hub runs as a service. This service
authenticates writes to a location by requiring a valid authentication token from a requestor.

![Gai storage](/storage/images/gaia-storage.png)

Gaia's approach to decentralization focuses on user control of data and storage.
If a user can choose which storage provider to store data with, then that is all the decentralization required to enable user-controlled
applications.

The control of user data lies in the way that user data is accessed.
When an application fetches a file `data.txt` for a given user `alice.id`, the
lookup will follow these steps:

1. Fetch the `zonefile` for `alice.id`
2. Read her profile URL from her `zonefile`.
3. Fetch Alice's profile.
4.  _Verify_ that the profile is signed by `alice.id`'s key
5. Read the application root URL (e.g. `https://gaia.alice.org/`) out of the profile
6. Fetch file from `https://gaia.alice.org/data.txt`.

Because `alice.id` controls her zonefile, she can change where her profile is
stored. For example, she may do this if the current storage of the profile is
compromised. Similarly, if Alice wishes to change her Gaia provider, or run her
own Gaia node, she can change the entry in her profile.

Applications writing directly on behalf of Alice, do not need to perform this
lookup. Instead, the Blockstack <a
href="http://blockstack.github.io/blockstack.js/index.html"
target="\_blank">authentication flow</a>  provides Alice's chosen application
root URL to the application. This authentication flow _is also_ within Alice's
control because Alice's browser _must_ generate the authentication response.


## Understand data storage

A Gaia storage hub stores the written data _exactly_ as given. It offers minimal
guarantees about the data. It does not ensure that data is validly formatted,
contains valid signatures, or is encrypted. Rather, the design philosophy is
that these concerns are client-side concerns.

Client libraries (such as `blockstack.js`) are capable of providing these
guarantees. Blockstack used a liberal definition of the end-to-end principle to
guide this design decision.


## Gaia versus other storage systems

Here's how Gaia stacks up against other decentralized storage systems.  Features
that are common to all storage systems are omitted for brevity.

<table class="uk-table uk-table-striped">
<thead>
  <tr>
    <th>Features</th>
    <th>Gaia</th>
    <th><a href="https://sia.tech/" target="\_blank">Sia</a></th>
    <th><a href="https://storj.io/" target="\_blank">Storj</a></th>
    <th><a href="https://ipfs.io/" target="\_blank">IPFS</a></th>
    <th><a href="https://datproject.org/" target="\_blank">DAT</a></th>
    <th><a href="https://www.scuttlebutt.nz/" target="\_blank">SSB</a></th>
  </tr>
  </thead>
  <tr>
    <td>User controls where data is hosted</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Data can be viewed in a normal Web browser</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Data is read/write</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td>Data can be deleted</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td>Data can be listed</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td>Deleted data space is reclaimed</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Data lookups have predictable performance</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Writes permission can be delegated</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Listing permission can be delegated</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Supports multiple backends natively</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Data is globally addressable</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>Needs a cryptocurrency to work</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>Data is content-addressed</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
</table>
