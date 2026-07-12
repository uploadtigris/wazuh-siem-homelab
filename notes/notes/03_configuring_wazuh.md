# Configuring Wazuh in Docker Compose

Verifying distro
```
cat /etc/os-release
```
output:
```
NAME="Ubuntu"
VERSION_ID="26.04"
VERSION="26.04 LTS (Resolute Raccoon)"
...
```

docker is already installed on this homelab so I am moving to setting up the wazuh containers:
Wazuh Manager
Wazuh Indexer
Wazuh 

Increased vm.max_map_count (required for the OpenSearch-based indexer)
Wazuh's indexer needs this kernel setting bumped or it'll crash on startup:
```
sudo sysctl -w vm.max_map_count=262144
```
persist across reboots
```
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
```

clone repo and cd into wazuh repo
```
git clone https://github.com/wazuh/wazuh-docker.git -b v4.14.6
cd wazuh-docker/single-node
```

inspecting the certificate generator configuration

> basically a sanity check to confirm that `generate-indexer-certs.yml` file is set up the way the docs expect for v4.14.6, before I actually run it.

```
ls single-node/
cat single-node/generate-indexer-certs.yml
```
output:
```
README.md  config  docker-compose.yml  generate-indexer-certs.yml
# Wazuh App Copyright (C) 2017, Wazuh Inc. (License GPLv2)
services:
  generator:
    image: wazuh/wazuh-certs-generator:0.0.4
    hostname: wazuh-certs-generator
    environment:
      - CERT_TOOL_VERSION=4.14
    volumes:
      - ./config/wazuh_indexer_ssl_certs/:/certificates/
      - ./config/certs.yml:/config/certs.yml
```

run the cert generator; Generates self-signed CA + node certs for Wazuh Manager/Indexer/Dashboard TLS

```
docker compose -f generate-indexer-certs.yml run --rm generator
ls config/wazuh_indexer_ssl_certs/
```



Changing default memory from 1 GB to 4 GB for Wazuh Indexer in docker-compose.yml

```
- "OPENSEARCH_JAVA_OPTS=-Xms4g -Xmx4g"
```
re-compose
```
docker compose up -d
docker compose ps
```

Since port 443 was mapped in the docker-compose.yml file, we can now access Wazuh from the browser @ https://<homelab laptop's IP>

## Changing Admin Password

step 1: generate password hash
```
docker exec -it single-node-wazuh.indexer-1 bash
export JAVA_HOME=/usr/share/wazuh-indexer/jdk
export PATH=$JAVA_HOME/bin:$PATH
$INSTALLATION_DIR/plugins/opensearch-security/tools/hash.sh
```

step 2: Update internal_users.yml
- exit container & edit file that defines `admin` user
```
exit
- nano config/wazuh_indexer/internal_users.yml
```

step 3: apply the change via security admin
- push updated user config into the running indexer (editing YAML alone doesn't take effect until I run this)
```
docker exec -it single-node-wazuh.indexer-1 bash -c '
export JAVA_HOME=/usr/share/wazuh-indexer/jdk
export PATH=$JAVA_HOME/bin:$PATH
export INSTALLATION_DIR=/usr/share/wazuh-indexer
$INSTALLATION_DIR/plugins/opensearch-security/tools/securityadmin.sh \
  -cd $INSTALLATION_DIR/config/opensearch-security/ \
  -icl -key $INSTALLATION_DIR/config/certs/admin-key.pem \
  -cert $INSTALLATION_DIR/config/certs/admin.pem \
  -cacert $INSTALLATION_DIR/config/certs/root-ca.pem -nhnv
'
```

step 4: also update dashboard/manager stored password
- found INDEXER_PASSWORD in docker-compose.yml for both wazuh.manager and wazuh.dashboard
- then re-composed
```
docker compose up -d
```

now the admin password is reset in the GUI & in the indexer settings!!
