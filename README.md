# Tempura Docker node

The Dockerfile creates a Docker Image with a running Tempura node. \
Requires Docker installed on your machine.

### Changes

* download binaries from lorenz10/peercoin
* `RUN make` instead of `RUN make -j4` to avoid compiler killed for memory problems

### Setup a node

1. Clone this repo, move into `/0.10.3` folder and run `docker build -t tempura .` to compile a **Docker Image**. \
If you already have created an Image, then load it with `docker load < my-image.tar`.

You can manually export and share your Docker Image with `docker save tempura > my-image.tar`.

2. Initialize and configure a **Docker Container** using:

```sh
$ docker run -p 9903:9903 -p 9904:9904 --name testnet-tempura -d tempura \
  -rpcallowip=0.0.0.0/0 \
  -rpcpassword=any_password \
  -rpcuser=any_username \
  -testnet=1 \
  -maxtipage=99999999999
```

(Riferimento altra pagina per elenco parametri di peercoind e chainparams...)

*Set "maxtipage" parameter to a very high value only in the case you want to avoid the initial blocks syncronization, that would get the node stucked if there is no other node from which downloading new blocks.*

3. Check if you can reach the defult RPC port (9904) running this command:

```sh
curl --user any_username:any_password --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getblockchaininfo", "params": [] }'  -H 'content-type: text/plain;' localhost:9904/
```

and you should obtain something like this:

> {"result":{"chain":"main","blocks":457576,"headers":457576,"bestblockhash":"17a24a8073c8f6bc422fc4f6fe8c76da892d0693d0ad1aa499e4b9b2c047fe2b","difficulty":1710444103.933884,"mediantime":1571034759,"verificationprogress":0.9999997034325266,"initialblockdownload":false,"chainwork":"00000000000000000000000000000000000000000000000000336b3807456f56","size_on_disk":700956211,"pruned":false,"warnings":""},"error":null,"id":"curltest"}

4. Once the server is running, you can interact with the node using peercoin-cli from the host machine, or, from the Container itself, opening Docker Desktop clicking on the "CLI" button next to the corresponding Container.

### References

* [How to create a Docker Image](https://www.linux.com/training-tutorials/how-create-docker-image/?utm_source=pocket_mylist)
* [How to share Docker Images with others](https://www.cloudsavvyit.com/12326/how-to-share-docker-images-with-others/?utm_source=pocket_mylist)
