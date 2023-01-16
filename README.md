# HashiCorp Vault on Docker in production mode (for dev and test purposes)

### Requirements:

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

7) Go to http://vault_ip:8200/ and unseal the vault with 3 of the 5 keys

8) Login with the "Initial Root Token" which is provided after the vault initionalization

9) Have fun and stay safe

### File structure:

```
`-- docker-hashicorp-vault
    |-- config
    |   `-- vault.json
    |-- data
    |-- docker-compose.yml
    |-- logs
    |-- polices
    `-- README.m
```
### Noteworthy links:

* https://www.bogotobogo.com/DevOps/Docker/Docker-Vault-Consul.php (used as a start / reference thanks @bogotobogo)
* https://developer.hashicorp.com/vault/docs/concepts/seal

