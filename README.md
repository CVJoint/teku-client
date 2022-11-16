# Teku Client - Docker

This project uses the official Teku client [consensys/teku](https://hub.docker.com/r/consensys/teku) which includes the relevant configuration for the `gnosis` network.

Pulling the latest Teku beacon client would look like this: 

```
docker pull consensys/teku:latest
```

## Teku reference

- General https://docs.teku.consensys.net/en/latest/
- CLI Reference https://docs.teku.consensys.net/en/latest/Reference/CLI/CLI-Syntax/

# Starting Teku on mainnet

1. Create your folder structure:

```
mkdir -p /home/$USER/gnosis/el-client
mkdir -p /home/$USER/gnosis/cl-client/beacon
mkdir -p /home/$USER/gnosis/cl-client/validator/keys
mkdir /home/$USER/gnosis/cl-client/validator/passwords
mkdir /home/$USER/gnosis/cl-client/validator/slashprotection
mkdir /home/$USER/gnosis/jwtsecret
```

2. Add an `.env` file with your WAN IP (`curl https://ipinfo.io/ip`), fee recepient (your gnosis address), graffiti, and checkpoint url `/home/$USER/gnosis/.env`.

```
WAN_IP:123.456.789.012
FEE_RECIPIENT=0x0000000000000000000000000000000000000000
GRAFFITI=gnosischain/teku
CHECKPOINT_URL=https://rpc-gbc.gnosischain.com/
```

3. Add your keystores in `/home/$USER/gnosis/cl-client/validator/keys` and their password in a file `/home/$USER/gnosis/cl-client/validator/passwords` to get this file structure:

```
/home/$USER/gnosis/
├── docker-compose.yml
├── .env
├── jwtsecret/
├── el-client/
└── cl-client/
    ├── beacon/
    └── validators/
        ├── keys/
        │   ├── keystore-001.json
        │   └── keystore-002.json
        ├── passwords/
        │   └── password.txt
        └── slashprotection/
```

4. Create a new `./jwtsecret` token:

```
openssl rand -hex 32 | tr -d "\n" > /home/$USER/gnosis/jwtsecret/jwtsecret
```

5. Import your validators:

When the Teku `cl-client` container starts, it will search the directories for keystores and passwords, and import them automatically.

6. Start `docker-compose.yml` services from this repository

```
cd /home/$USER/gnosis
docker-compose up -d
```

7. Check your logs for each service (`el-client` or `cl-client`) with:

```
docker logs -f --tail 500 <service>
```

8. To update, just pull the new images, then stop and restart your docker-compose file:
```
cd /home/$USER/gnosis
docker-compose pull
docker-compose stop
docker-compose up -d
```
