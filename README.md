# HashiCorp Vault on Docker in production mode (standalone mode, for dev and test purposes only)

For the cluster mode readme click [here](https://github.com/cisolutions-nl/docker-hashicorp-vault#hashicorp-vault-on-docker-in-production-mode-cluster-mode).

### Requirements:

* Virtual machine
* Docker
* Docker-Compose
* (sudo) rights
* Certificates

### Instructions:

1) Clone the project
```
git clone https://github.com/Nenodema/docker-hashicorp-vault.git
```
2) Enter the project directory and start the container with docker-compose
```
cd docker-hashicorp-vault/standalone
(sudo) docker-compose up -d
```
4) Enter container and initilize vault (save the unseal keys on a secure location)
```
(sudo) docker exec -it vault_vault-server_1 /bin/sh
vault operator init
```
![image](https://user-images.githubusercontent.com/33698556/212346090-229f6778-811a-46ee-8cf0-1688685cf548.png)

7) Go to http://VAULT_IP:8200/ and unseal the vault with 3 of the 5 keys

8) Login with the "Initial Root Token" which is provided after the vault initionalization

9) Have fun and stay safe

### File structure:

```
standalone
├── config
│   └── vault.json
└── docker-compose.yml
```
### Noteworthy links:

* https://www.bogotobogo.com/DevOps/Docker/Docker-Vault-Consul.php
* https://developer.hashicorp.com/vault/docs/concepts/seal


------------------------------------------------------------------------------------------------------------


# HashiCorp Vault on Docker in production mode (cluster mode)

### Requirements:

* Virtual machine (minimum of three)
* Docker
* Docker-Compose
* (sudo) rights
* Certificates

### Instructions:

1) Clone the project
```
git clone https://github.com/Nenodema/docker-hashicorp-vault.git
```
2) Change variables in the following files:
* .env
* config/vault.json
* certs/generate_CA.sh
* certs/generate_certificate.sh
* certs/generate_certificate_request.sh
3) Generate CA, copy and rename CA file, move vault-server-ca.crt to the config directory
```
cd certs
chmod 766 * (make sure that you are in the certs directory!)
./certs/generate_CA.sh
cp vault-server-ca.pem ../vault-server-ca.crt
```
4) Generarte Certificate request per machine
```
./certs/generate_certificate_request.sh
```
5) Generarte Certificate per machine
```
./certs/generate_certificate.sh
```
6) Copy the "cert.pem" and the "key.pm" to the config directory
```
cp cert.pem key.pem ../config
```
7) Enter the project directory and start the container with docker-compose
```
verify that you are in the following directory: docker-hashicorp-vault/cluster
(sudo) docker-compose up -d --build
```
8) Enter container and initilize vault (save the unseal keys on a secure location)
```
(sudo) docker exec -it vault_vault-server_1 /bin/sh
vault operator init
```
![image](https://user-images.githubusercontent.com/33698556/212346090-229f6778-811a-46ee-8cf0-1688685cf548.png)

9) Go to http://VAULT_IP:8200/ and unseal the vault with 3 of the 5 keys

10) Login with the "Initial Root Token" which is provided after the vault initionalization

11) Execute steps 1-10 for the other two nodes

12) Join the new two nodes to the first node
```
(sudo) docker exec -it vault_vault-server_1 /bin/sh
vault operator raft join "https://$PRIMARY_NODE:8200"
vault operator unseal (3 times)
```
13) check the status of the cluster
```
vault login (use the "Initial Root Token" which is provided after the vault initionalization
vault operator raft list-peers
Node Address State Voter
 — — — — — — — — — — — — — — — — — — — — — — — — -
vault_node_1 10.99.99.10:8201 leader true
vault_node_2 10.99.99.11:8201 follower true
vault_node_3 10.99.99.12:8201 follower true
```
14) Go to http://VAULT_IP:8200/ of one of the nodes

15) Login with the "Initial Root Token" which is provided after the vault initionalization

16) Have fun and stay safe

### File structure:

```
cluster
├── Dockerfile
├── certs
│   ├── generate_CA.sh
│   ├── generate_certificate.sh
│   └── generate_certificate_request.sh
├── config
│   └── vault.json
└── docker-compose.yml

```
### Noteworthy links:

* https://fongyang.medium.com/hashicorp-vault-on-docker-for-secrets-management-f579867dd1ca
* https://www.velotio.com/engineering-blog/how-to-setup-hashicorp-vault-ha-cluster-with-integrated-storage-raft
