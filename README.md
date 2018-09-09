# Crosschain transfers through general L2

![Logo](https://docs.google.com/drawings/d/e/2PACX-1vSv_Sm2XqYnVWspCo2HapRMg5Xllucv5_IgZIA6_3by8QvQWldqdir5jHAaFBcEwWQil2cFSgeV8jJd/pub?w=1101&h=741)

</br>


### What is this all about?
Imagine the general-purpose Layer-2 network that provides ability to send `tokens` / `assets` / `IOU` / `binary data` / _etc_ to (almost) anyone participant of the network.  
How should it be developed? **We know it**. 

</br>

### What must be done in general?
We already have a general purpose state channels network - [GEO Protocol](https://github.com/GEO-Protocol). All we need are middleware "connectors" for it to various environments. The role of the connector - atomic mirroring of the state from the network channels to the related environment.

</br>

### What was done during the hackaton?
This thing
![Logo](https://docs.google.com/drawings/d/e/2PACX-1vSuVYEdRFirC7gED-V49oOqk9RExN4VrTQ5A4ANZFsGrAGzLB--wnJ6lYmwHeFXkxjtf7yX9puD2nhM/pub?w=1101&h=741)

</br>

The goal of this _dirty hacked **prototype**_ is to provide PoC of L2 state channels,
that are covered by different blockchain ecosystems (common Layer 2 for various blockchain ecosystems, (BTC/ETH/etc)).


### How does it works?
![arch](https://docs.google.com/drawings/d/e/2PACX-1vTFcz9UwCJsB2pFgusM4ImbYEwHvJ51OTWHIxe7NFQiakubgFdpqTMFOjlC1pqeqDva1FvJWXGZRSbd/pub?w=1101&h=741)

* [Architecure overview / ETH Contract](https://github.com/Koroqe/geo-state-channel-eth);
* [Middleware implementation](https://github.com/Koroqe/geo-ethereum-middleware);
* Forked and hacked [GEO node repo](https://github.com/HaySayCheese/EthGEOProtoHack_ethberlin) ([Specs](https://github.com/GEO-Protocol/specs-protocol));
* [Some slides for high-level explanation](https://docs.google.com/presentation/d/1HNhj0JxOa-g0GmBpJx7jy8rIV23onR6hpLEGkyVwil4/edit?usp=sharing);


### How can I check it?
Please, join:

```
ip: 104.248.47.57
ssh-port: 2277
user: ethBerlin
pass: berlin2018
```

```
ssh ethBerlin@104.248.47.57 -p 2277
```

##### Instructions how to prepare the environment
1. Build and deploy the [contract]((https://github.com/Koroqe/geo-state-channel-eth)) to the net.
2. Create 2 nodes. First node created would be "Alice", and second one would be "Bob".

```
curl -X POST private.localhost:2000/api/v1/nodes
curl -X POST private.localhost:2000/api/v1/nodes
```

This Requests would return you UUID of the nodes created.  
You should remember which UUID belongs to which node.  
In case if nodes are present - you may use already present pair of nodes.

3. For Bob in `/home/nodes_handler/io/<bob_uuid>/` create file `middleware.port` with `5000` in it.
4. For Alice in `/home/nodes_handler/io/<alice_uuid>/` create file `middleware.port` with **`5001`** in it.
5. Stop both Alice and Bob middlewares:
```
killall -r "GeoEth*"
```
6. Change corresponding `contract->address`, account address and PK in middlewares configurations (if needed)
Alice:
```
nano middleware/ubuntu-x64-5001/publish/appsettings.json
```

Bob:
```
nano middleware/ubuntu-x64-5000/publish/appsettings.json
```
7. Start corresponding middlewares:
```
~/middleware/ubuntu-x64-5000/publish$ nohup > /dev/null ./GeoEthereumMiddleware &
```

```
~/middleware/ubuntu-x64-5001/publish$ nohup > /dev/null ./GeoEthereumMiddleware &
```

8. Update (if needed) ethereum address to the Alice and Bob config (conf.json) in `/home/nodes_handler/io/<node_uuid>/`.
Example of result config:
```
{
  "gateway": [1],
  "network": {
    "interface": "0.0.0.0",
    "port": 8090
  },

  "uuid2address": {
    "host": "0.0.0.0",
    "port": 1500
  },

  "node": {
    "uuid": "13e5cf8c-5834-4e52-b65b-f9281dd1ff90",
    "ethereum": "234wrew534tgerger4eegrthrt"
  }
}
```

9. Start nodes handler:
```
sudo systemctl start handler.service
```

10. Create TL from Bob to Alice
```
curl -X PUT private.localhost:2000/api/v1/nodes/{bob_uuid}/contractors/{alice_uuid}/trust-lines/1/?amount=500000&state-channel=1
```

11. [optional] Create TL from Alice to Bob
```
curl -X PUT private.localhost:2000/api/v1/nodes/{alice_uuid}/contractors/{bob_uuid}/trust-lines/1/?amount=400000
```

12. Send several payments in boths directions:
```
curl -X PUT private.localhost:2000/api/v1/nodes/{bob_uuid}/contractors/{alice_uuid}/transactions/1/?amount=500
...
curl -X PUT private.localhost:2000/api/v1/nodes/{alice_uuid}/contractors/{bob_uuid}/transactions/1/?amount=500
```

13. Close channel via:
```
curl -X POST private.localhost:2000/api/v1/nodes/{alice_uuid}/contractors/{bob_uuid}/trust-lines-close-channel/1/
```
On the Bob's side channel would be closed utomatically.

14. Check nodes eth. wallets.

15. [Optional] nodes logs are accessible in `/home/nodes_handler/io/<node_uuid>/operations.log`.


### Licence - MIT
Copyright 2018 ETH Berlin Hackaton

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


------

#### Hacked with <3 to 
![eth](https://github.com/ethberlin-hackathon/media-assets/raw/master/ETHBerlin%20logo%20-%20horizontal%20transparent%20-%20small.png)
