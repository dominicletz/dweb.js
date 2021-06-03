# dweb.js

Scratch pad for a decentralizd web app javascript API 

## Usage Examples

```javascript
const DWeb = require('dweb');

// type could be any of 'gun', 'braid', 'ipfs', 'hyper', ... plus needed options
let dweb = new DWeb('com.mywebapp:<documentid>', {type: 'diode', network: 'prenet'});


console.log(dweb);
> {
    peers: ... ,
    data: ... ,
    utils: ...,
    ...
}
```

## Peers

```javascript
# Peers
let contacts = dweb.peers.getContacts()
let online = dweb.peers.getOnlineContacts()

# Handshaking ?
let ref = dweb.peers.createMeetingPoint()
# On another node:
dweb.peers.joinMeetingPoint(ref)

# Broadcast Channels
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

let db = dweb.data.sql.create("<documentid>")

db.

```


# Background - Data

## Sync modes

In a decentralized system there a couple of different possiblities to implement sync and data sharing modes:

* Leader based - One of the peers is effectively data owner and all modifications have to reach him, reads though can be cached. Potentially the whole data set can be cached.
* Merge - Any peer can make modifications and submit them to other peers, on conflicting modifications a conflict resolution algorithm is used.
* CRDT - Modifications are strict delta changes, that can be synced conflict free. 

## Authenticity

For direct peer 2 peer communication authenticity depends on the checks during the handshake, but for gossiping protocols there needs to be some form of message signature mechanism in order to proof which client did send what message.

## Stores

### SQLite 

This storage is compatible with the leader based sync mode and can potentially be read synchronized to all clients. There could also be a mode where different transaction sets are tried to be merged, but this would neccesarily result in a certain number of conflicts. That need conflict resolution approaches

### Braid / Gun Trees

These storages with CRDT mode

https://gun.eco/
https://datatracker.ietf.org/doc/html/draft-toomim-httpbis-braid-http

