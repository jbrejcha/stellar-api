# Stellar-API

## Table of Contents

- [Quick Start](#quick-start)
- Getting Started
  - [Config](#config)
  - [Stellar API Notes](#stellar-api-notes)
  - [Resources](#resources)
- [List of All API Endpoints](#endpoints)

## Quick Start

Run Stellar Core and Horizon via [Docker Container](https://hub.docker.com/r/stellar/quickstart/). If you need help read [getting started]($getting-started) for more information.

```
docker run --rm -it -p "8088:8000" -p "5433:5432" --name stellar stellar/quickstart —testnet
```
Save the postgresql password generated and then detach from the container with Control=P then Control-Q to avoid breaking the instance.

Add [config](#config) and make sure the `networkType` parameter value is set to "testnet". This ensures the stellar-sdk instance is set to test network.

Run API
```
git clone git@github.com:ar-to/stellar-api.git
cd stellar-api
npm i
npm start
```

## Getting Started

### Setting Up Docker
Follow these instructions to setup Docker for running a local development stellar-core and horizon in MacOS but it is similar in other OSs. This setup runs a testnet network with the docker container in ephemeral mode.

- install [Docker](https://docs.docker.com/docker-for-mac/install/)
- Run container to pull docker stellar image and initiate container. The command below sets the horizon port to 8088 local and postgresql database to 5433 local. These are the ports used to connect to these services. Read about the [Docker stellar image](https://hub.docker.com/r/stellar/quickstart/) and available options. 

```
docker run --rm -it -p "8088:8000" -p "5433:5432" --name stellar stellar/quickstart —testnet
```
- To access the container internally:

```
docker exec -it stellar /bin/bash
```
- test horizon connection via browser or curl. Command below will return available endpoints and status of the network sync. You can now communicate with the Stellar network!

```
curl http://0.0.0.0:8000/
```


### Testing Horizon locally
Once the docker container is setup you need to wait until the node is synched with the network and the database is updated. You can do can check by accessing the container manually or via a curl command to the localhost and port given in the docker command.

**Manual Check**
```
$ docker exec -it stellar /bin/bash
root@0000000:/#stellar-core -c info
```
**Via Curl**
```
curl http://0.0.0.0:8000/accounts/GDZCM7KFIFMW2SU4RS5YMUZPEKFWUI6WQTHBRB6AHPBRT5UQZJ4WM3II
```


### Config

```
{
  "networks": {
    "connectApi": "local",
    "local":{
      "url": "http://0.0.0.0:8000",
      "networkName": "docker",
      "networkType": "testnet"
    },
    "external":{
      "url": "https://horizon-testnet.stellar.org",
      "networkName": "stellar",
      "networkType": "testnet"
    },
    "mainnet":{}
  }
}
```


## Stellar API Notes

GET `/generate-seed`
Generate a new seed and public key

POST `/create-account`
The `secretSeed` and `startingBalance` are required. Passing on the seed will generate a new seed and create the new account. To simply create a new account from an existing seed, first create the seed via `/generate-seed` and pass the public key as the `destinationPublicKey` parameter.
Request:
```
{
	"secretSeed": "secretSeed",
    "startingBalance": "40"
    "destinationPublicKey": "optional public key"
}
```
Response:
```
{
    "body": {
        "secretSeed": "secretSeed"
    },
    "success": {
        "newSeed": {
            "publicKey": "publicKey",
            "seed": "seed"
        },
        "sourceCanSign": true,
        "sourcePublicKey": "sourcePublicKey",
        "tx": {
        ...
        }
    }
}
```

POST `/payment`
Send Lumens between accounts
Request:
```
{
	"secretSeed": "secretSeed",
	"amount": "40",
	"destinationPublicKey": "destinationPublicKey"
}
```

## Resources

Stellar testnet server: https://horizon-testnet.stellar.org/
Run this in the browser to see what methods are available and the ledger history. For example, to check a transaction:
```
https://horizon-testnet.stellar.org/transactions/074847e1fd022d7084e69ca4b767af9c7c3ed473bea82047810bea0f1ae10966
```
To check an account:
```
https://horizon-testnet.stellar.org/accounts/GDZCM7KFIFMW2SU4RS5YMUZPEKFWUI6WQTHBRB6AHPBRT5UQZJ4WM3II
```


## Endpoints

```
[
    {
        "path": "/",
        "methods": [
            "GET"
        ]
    },
    {
        "path": "/api",
        "methods": [
            "GET",
            "POST"
        ]
    },
    {
        "path": "/api/network",
        "methods": [
            "GET"
        ]
    },
    {
        "path": "/api/get-balance/:address",
        "methods": [
            "GET"
        ]
    },
    {
        "path": "/api-endpoints",
        "methods": [
            "GET"
        ]
    }
]
```