# Full Node of mainnet

## goal

spins up a geth node for ethereum chain using provisioning tools

### requirements

env vars:

```bash
export TF_VAR_AWS_ACCESS_KEY=""
export TF_VAR_AWS_SECRET_KEY=""
export TF_VAR_SSH_PASSWORD=""  # to be used on logins without ssh keypair
export TF_VAR_REGION=""
```

- aws-cli
- terraform
- ansible
- jq (for parsing json)
- ssh keys (`~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`)

### setup

get an ip address alocated and append to ansible hosts, export the address to env

```bash
aws ec2 allocate-address > /etc/allocated-address
export NODE_IP=$(cat /etc/allocated-address | jq -r ".PublicIp")
export TF_VAR_ALLOCATION_ID=$(cat /etc/allocated-address | jq -r ".AllocationId")
echo $NODE_IP >> /etc/ansible/hosts
```

in case one would prefer to maintain the same address, the setting of the `NODE_IP`
and `TF_VAR_ALLOCATION_ID` variables can be appended to ~/.profile or ~/.bashrc

### usage

start and setup the instance

```bash
cd ./terraform && terraform init && terraform apply
cd ../
ansible-playbook ansible/main.yml
ssh ubuntu@$NODE_IP
```

and

```bash
geth --syncmode light --http
```

then, you can pull data from the chain by calling

```bash
curl http://$NODE_IP:8545/
```
