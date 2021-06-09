# dweb.js

Scratchpad for a decentralized web app javascript API 

**TODO**

- Check and get inspired by
    - https://github.com/yjs/yjs
    - https://news.ycombinator.com/item?id=27424496 
    - https://github.com/yousefed/reactive-crdt
    - https://github.com/pubkey/rxdb


## Usage Examples

```javascript
const DWeb = require('dweb');

// type could be any of 'gun', 'braid', 'ipfs', 'hyper', ... plus needed options
let dweb = new DWeb('com.mywebapp:<documentid>', {type: 'diode', network: 'prenet'});


console.log(dweb);
> {
    self,
    peers: ... ,
    data: ... ,
    utils: ...,
    ...
}
```

## Peers

```javascript
// Peers
let contacts = dweb.peers.getContacts()
let online = dweb.peers.getOnlineContacts()

// Handshaking ?
let ref = dweb.peers.createMeetingPoint()
# On another node:
dweb.peers.joinMeetingPoint(ref)

// Broadcast Channels
let channel = dweb.peers.joinChannel("cursor")
window.on("mousemove", (event) => {
  channel.send({type: "mouse", x: event.x, y: event.y})
})
channel.on("mouse", (peer, event) => {
  drawMouseOnScreen(peerColor(peer), event.x, event.y)
})
```

## Data

```javascript

// Object tree
let data = dweb.data.attachTree("<documentid>", {mergeStrategy: "callback"})

data.on("merge", (peer, changeset) => {
  if (has_document_write_access(peer)) return true;
  else return false;
}

// Sql 
let sql = await dweb.data.attachSql("<documentid>", {mergeStrategy: "leader"})
if (sql.leader == dweb.self) {
   ...
} else {
   ...
} 

```

**TODO**: Read one braid and gun and add an example of making a change and listening to that change, same on the SQL side of things... 


# Background - Data

## Sync modes

In a decentralized system there a couple of different possibilities to implement sync and data sharing modes:

* Leader-based - One of the peers is effectively data owner and all modifications have to reach him, reads though can be cached. Potentially the whole data set can be cached.
* Merge - Any peer can make modifications and submit them to other peers, on conflicting modifications a conflict resolution algorithm is used.
* CRDT - Modifications are strict delta changes, that can be synced conflict-free. 

## Authenticity

For direct peer-2-peer communication, authenticity depends on the checks during the handshake, but for gossiping protocols, there needs to be some form of message signature mechanism to prove which client did send what message.

## Stores

### SQLite 

This storage is compatible with leader-based sync mode and can potentially be read synchronized to all clients. There could also be a mode where different transaction sets are tried to be merged, but this would necessarily result in a certain number of conflicts. That need conflict resolution approaches

### Braid / Gun Trees

These storages with CRDT mode

* https://gun.eco/
* https://datatracker.ietf.org/doc/html/draft-toomim-httpbis-braid-http

