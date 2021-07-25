# Tempura Docker node

The Dockerfile has been edited for creating a Docker Image with a running Tempura node.

### Changes

* download tempura binaries instead of peercoin ones
* `RUN make` instead of `RUN make -j4` to avoid compiler killed for having too many threads

### Setup a node  :whale:

1. Clone this repo, move into `/0.10.3` folder and run `docker build -t tempura .` to compile a new **Docker Image** for tempura. 

You can manually export and share your Docker Images with `docker save tempura > my-image.tar` and you can load them on another machine using `docker load < my-image.tar`.


2. Initialize and configure a **Docker Container** using:

```sh
docker run -p 9903:9903 -p 9904:9904 --name testnet-tempura -d tempura \
  -rpcallowip=0.0.0.0/0 \
  -rpcpassword=any_password \
  -rpcuser=any_username \
  -testnet=1
```

(Riferimento al repo principale per elenco parametri di peercoind e chainparams...)

3. **Check** if you can reach the defult RPC port (9904 for testnet) running this command:

```sh
curl --user any_username:any_password --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getblockchaininfo", "params": [] }'  -H 'content-type: text/plain;' localhost:9904/
```

and you should obtain something like this:

> {"result":{"chain":"main","blocks":457576,"headers":457576,"bestblockhash":"17a24a8073c8f6bc422fc4f6fe8c76da892d0693d0ad1aa499e4b9b2c047fe2b","difficulty":1710444103.933884,"mediantime":1571034759,"verificationprogress":0.9999997034325266,"initialblockdownload":false,"chainwork":"00000000000000000000000000000000000000000000000000336b3807456f56","size_on_disk":700956211,"pruned":false,"warnings":""},"error":null,"id":"curltest"}

4. Once the server is running, you can interact with the node using **peercoin-cli** from the host machine, since is performed port-forwarding of the guest OS, or, from the Container itself, opening Docker Desktop and clicking on the "CLI" button next to the corresponding Container.

### References :books:

* Forked from: [peercoin/docker-peercoind](https://github.com/peercoin/docker-peercoind.git)
* [How to create a Docker Image](https://www.linux.com/training-tutorials/how-create-docker-image/?utm_source=pocket_mylist)
* [How to share Docker Images with others](https://www.cloudsavvyit.com/12326/how-to-share-docker-images-with-others/?utm_source=pocket_mylist)

