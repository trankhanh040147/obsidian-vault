### 2.3. Tính bảo mật dữ liệu
- Tại sao trong hệ thống public blockchain không có sự bảo mật dữ liệu ?
- Phương án mà Fabric áp dụng để giải quyết vấn đề bảo mật dữ liệu: Kiến trúc channel và Private Data.

Prompt: 
- Hãy giúp viết tài liệu giới thiệu về tính bảo mật dữ liệu trong Hyberledger Fabric.
- Viết ngắn gọn và đúng trọng tâm. 
- Với mục đích là giới thiệu người đọc về Fabric. 
- Người đọc nắm đươc các khái niệm cơ bản trong Blockchain, và chưa biết về Hyberledger Fabric. 
- Có thể dùng cái thuật ngữ trong Software Development và Fabric
- Hạn chế sử dụng ví dụ và tưởng tượng
---
### 2.4. Cơ chế đồng thuận

Prompt: 
- Tương tự, hãy viết giới thiệu về cơ chế đồng thuận trong Fabric. Giải thích dự trên hình
- Viết ngắn gọn và đúng trọng tâm. 
- Với mục đích là giới thiệu người đọc về Fabric. 
- Người đọc nắm đươc các khái niệm cơ bản trong Blockchain, và chưa biết về Hyberledger Fabric. 
- Có thể dùng cái thuật ngữ trong Software Development và Fabric
- Hạn chế sử dụng ví dụ và tưởng tượng

---
X.509 là gì ?
- An X.509 certificate binds an identity to a public key using a digital signature. A certificate contains an identity (a hostname, or an organization, or an individual) and a public key ([RSA](https://en.wikipedia.org/wiki/RSA_\(cryptosystem\) "RSA (cryptosystem)"), [DSA](https://en.wikipedia.org/wiki/DSA_\(cryptography\) "DSA (cryptography)"), [ECDSA](https://en.wikipedia.org/wiki/ECDSA "ECDSA"), [ed25519](https://en.wikipedia.org/wiki/Ed25519 "Ed25519"), etc.), and is either signed by a certificate authority or is self-signed. When a certificate is signed by a trusted certificate authority, or validated by other means, someone holding that certificate can use the public key it contains to establish secure communications with another party, or validate documents [digitally signed](https://en.wikipedia.org/wiki/Digital_signature "Digital signature") by the corresponding [private key](https://en.wikipedia.org/wiki/Private_key "Private key").
- X.509 also defines [certificate revocation lists](https://en.wikipedia.org/wiki/Certificate_revocation_list "Certificate revocation list"), which are a means to distribute information about certificates that have been deemed invalid by a signing authority, as well as a [certification path validation algorithm](https://en.wikipedia.org/wiki/Certification_path_validation_algorithm "Certification path validation algorithm"), which allows for certificates to be signed by intermediate CA certificates, which are, in turn, signed by other certificates, eventually reaching a [trust anchor](https://en.wikipedia.org/wiki/Trust_anchor "Trust anchor").

 X.509 key được dùng ở đâu ?

 Cấu trúc của một X.509

 Định dạng của X.509


Prompt: 
- Hãy viết tài liệu về X.509
- Viết ngắn gọn và đúng trọng tâm. 
- Có thể dùng cái thuật ngữ trong Software Development
---
# Quy trình tạo org thứ 3 và tham gia vào mạng ntn ? Có phải trước tạo channel cần phải làm các bước như: tạo chứng chỉ và CCP (CCP là gì ?)

```
#!/usr/bin/env bash  
#  
# Copyright IBM Corp All Rights Reserved  
#  
# SPDX-License-Identifier: Apache-2.0  
#  
  
# This script extends the Hyperledger Fabric test network by adding  
# adding a third organization to the network  
#  
  
# prepending $PWD/../bin to PATH to ensure we are picking up the correct binaries  
# this may be commented out to resolve installed version of tools if desired  
export PATH=${PWD}/../../bin:${PWD}:$PATH  
export FABRIC_CFG_PATH=${PWD}/../../config  
export VERBOSE=false  
  
. ../scripts/utils.sh  
  
: ${CONTAINER_CLI:="docker"}  
if command -v ${CONTAINER_CLI}-compose > /dev/null 2>&1; then  
    : ${CONTAINER_CLI_COMPOSE:="${CONTAINER_CLI}-compose"}  
else  
    : ${CONTAINER_CLI_COMPOSE:="${CONTAINER_CLI} compose"}  
fi  
infoln "Using ${CONTAINER_CLI} and ${CONTAINER_CLI_COMPOSE}"  
  
  
# Print the usage message  
function printHelp () {  
  echo "Usage: "  
  echo "  addOrg3.sh up|down|generate [-c <channel name>] [-t <timeout>] [-d <delay>] [-f <docker-compose-file>] [-s <dbtype>]"  
  echo "  addOrg3.sh -h|--help (print this message)"  
  echo "    <mode> - one of 'up', 'down', or 'generate'"  
  echo "      - 'up' - add org3 to the sample network. You need to bring up the test network and create a channel first."  
  echo "      - 'down' - bring down the test network and org3 nodes"  
  echo "      - 'generate' - generate required certificates and org definition"  
  echo "    -c <channel name> - test network channel name (defaults to \"mychannel\")"  
  echo "    -ca <use CA> -  Use a CA to generate the crypto material"  
  echo "    -t <timeout> - CLI timeout duration in seconds (defaults to 10)"  
  echo "    -d <delay> - delay duration in seconds (defaults to 3)"  
  echo "    -s <dbtype> - the database backend to use: goleveldb (default) or couchdb"  
  echo "    -verbose - verbose mode"  
  echo  
  echo "Typically, one would first generate the required certificates and "  
  echo "genesis block, then bring up the network. e.g.:"  
  echo  
  echo "  addOrg3.sh generate"  
  echo "  addOrg3.sh up"  
  echo "  addOrg3.sh up -c mychannel -s couchdb"  
  echo "  addOrg3.sh down"  
  echo  
  echo "Taking all defaults:"  
  echo "  addOrg3.sh up"  
  echo "  addOrg3.sh down"  
}  
  
# We use the cryptogen tool to generate the cryptographic material  
# (x509 certs) for the new org.  After we run the tool, the certs will  
# be put in the organizations folder with org1 and org2  
  
# Create Organziation crypto material using cryptogen or CAs  
function generateOrg3() {  
  # Create crypto material using cryptogen  
  if [ "$CRYPTO" == "cryptogen" ]; then  
    which cryptogen  
    if [ "$?" -ne 0 ]; then  
      fatalln "cryptogen tool not found. exiting"  
    fi  
    infoln "Generating certificates using cryptogen tool"  
  
    infoln "Creating Org3 Identities"  
  
    set -x  
    cryptogen generate --config=org3-crypto.yaml --output="../organizations"  
    res=$?  
    { set +x; } 2>/dev/null  
    if [ $res -ne 0 ]; then  
      fatalln "Failed to generate certificates..."  
    fi  
  
  fi  
  # Create crypto material using Fabric CA  
  if [ "$CRYPTO" == "Certificate Authorities" ]; then  
    fabric-ca-client version > /dev/null 2>&1  
    if [[ $? -ne 0 ]]; then  
      echo "ERROR! fabric-ca-client binary not found.."  
      echo  
      echo "Follow the instructions in the Fabric docs to install the Fabric Binaries:"  
      echo "https://hyperledger-fabric.readthedocs.io/en/latest/install.html"  
      exit 1  
    fi  
  
    infoln "Generating certificates using Fabric CA"  
    ${CONTAINER_CLI_COMPOSE} -f ${COMPOSE_FILE_CA_BASE} -f $COMPOSE_FILE_CA_ORG3 up -d 2>&1  
  
    . fabric-ca/registerEnroll.sh  
  
    sleep 10  
  
    infoln "Creating Org3 Identities"  
    createOrg3  
  
  fi  
  
  infoln "Generating CCP files for Org3"  
  ./ccp-generate.sh  
}  
  
# Generate channel configuration transaction  
function generateOrg3Definition() {  
  which configtxgen  
  if [ "$?" -ne 0 ]; then  
    fatalln "configtxgen tool not found. exiting"  
  fi  
  infoln "Generating Org3 organization definition"  
  export FABRIC_CFG_PATH=$PWD  
  set -x  
  configtxgen -printOrg Org3MSP > ../organizations/peerOrganizations/org3.example.com/org3.json  
  res=$?  
  { set +x; } 2>/dev/null  
  if [ $res -ne 0 ]; then  
    fatalln "Failed to generate Org3 organization definition..."  
  fi  
}  
  
function Org3Up () {  
  # start org3 nodes  
  
  if [ "$CONTAINER_CLI" == "podman" ]; then  
    cp ../podman/core.yaml ../../organizations/peerOrganizations/org3.example.com/peers/peer0.org3.example.com/  
  fi  
  
  if [ "${DATABASE}" == "couchdb" ]; then  
    DOCKER_SOCK=${DOCKER_SOCK} ${CONTAINER_CLI_COMPOSE} -f ${COMPOSE_FILE_BASE} -f $COMPOSE_FILE_ORG3 -f ${COMPOSE_FILE_COUCH_BASE} -f $COMPOSE_FILE_COUCH_ORG3 up -d 2>&1  
  else  
    DOCKER_SOCK=${DOCKER_SOCK} ${CONTAINER_CLI_COMPOSE} -f ${COMPOSE_FILE_BASE} -f $COMPOSE_FILE_ORG3 up -d 2>&1  
  fi  
  if [ $? -ne 0 ]; then  
    fatalln "ERROR !!!! Unable to start Org3 network"  
  fi  
}  
  
# Generate the needed certificates, the genesis block and start the network.  
function addOrg3 () {  
  # If the test network is not up, abort  
  if [ ! -d ../organizations/ordererOrganizations ]; then  
    fatalln "ERROR: Please, run ./network.sh up createChannel first."  
  fi  
  
  # generate artifacts if they don't exist  
  if [ ! -d "../organizations/peerOrganizations/org3.example.com" ]; then  
    generateOrg3  
    generateOrg3Definition  fi  
  
  infoln "Bringing up Org3 peer"  
  Org3Up  
  
  # Create the configuration transaction needed to add  
  # Org3 to the network  infoln "Generating and submitting config tx to add Org3"  
  export FABRIC_CFG_PATH=${PWD}/../../config  
  . ../scripts/org3-scripts/updateChannelConfig.sh $CHANNEL_NAME $CLI_DELAY $CLI_TIMEOUT $VERBOSE  
  if [ $? -ne 0 ]; then  
    fatalln "ERROR !!!! Unable to create config tx"  
  fi  
  
  infoln "Joining Org3 peers to network"  
  . ../scripts/org3-scripts/joinChannel.sh $CHANNEL_NAME $CLI_DELAY $CLI_TIMEOUT $VERBOSE  
  if [ $? -ne 0 ]; then  
    fatalln "ERROR !!!! Unable to join Org3 peers to network"  
  fi  
}  
  
# Tear down running network  
function networkDown () {  
    cd ..  
    ./network.sh down  
}  
  
# Using crpto vs CA. default is cryptogen  
CRYPTO="cryptogen"  
# timeout duration - the duration the CLI should wait for a response from  
# another container before giving up  
CLI_TIMEOUT=10  
#default for delay  
CLI_DELAY=3  
# channel name defaults to "mychannel"  
CHANNEL_NAME="mychannel"  
# use this as the docker compose couch file  
COMPOSE_FILE_COUCH_BASE=compose/compose-couch-org3.yaml  
COMPOSE_FILE_COUCH_ORG3=compose/${CONTAINER_CLI}/docker-compose-couch-org3.yaml  
# use this as the default docker-compose yaml definition  
COMPOSE_FILE_BASE=compose/compose-org3.yaml  
COMPOSE_FILE_ORG3=compose/${CONTAINER_CLI}/docker-compose-org3.yaml  
# certificate authorities compose file  
COMPOSE_FILE_CA_BASE=compose/compose-ca-org3.yaml  
COMPOSE_FILE_CA_ORG3=compose/${CONTAINER_CLI}/docker-compose-ca-org3.yaml  
# database  
DATABASE="leveldb"  
  
# Get docker sock path from environment variable  
SOCK="${DOCKER_HOST:-/var/run/docker.sock}"  
DOCKER_SOCK="${SOCK##unix://}"  
  
# Parse commandline args  
  
## Parse mode  
if [[ $# -lt 1 ]] ; then  
  printHelp  
  exit 0  
else  
  MODE=$1  
  shift  
fi  
  
# parse flags  
  
while [[ $# -ge 1 ]] ; do  
  key="$1"  
  case $key in  
  -h )  
    printHelp  
    exit 0  
    ;;  
  -c )  
    CHANNEL_NAME="$2"  
    shift  
    ;;  
  -ca )  
    CRYPTO="Certificate Authorities"  
    ;;  
  -t )  
    CLI_TIMEOUT="$2"  
    shift  
    ;;  
  -d )  
    CLI_DELAY="$2"  
    shift  
    ;;  
  -s )  
    DATABASE="$2"  
    shift  
    ;;  
  -verbose )  
    VERBOSE=true  
    ;;  
  * )  
    errorln "Unknown flag: $key"  
    printHelp  
    exit 1  
    ;;  
  esac  shift  
done  
  
  
# Determine whether starting, stopping, restarting or generating for announce  
if [ "$MODE" == "up" ]; then  
  infoln "Adding org3 to channel '${CHANNEL_NAME}' with '${CLI_TIMEOUT}' seconds and CLI delay of '${CLI_DELAY}' seconds and using database '${DATABASE}'"  
  echo  
elif [ "$MODE" == "down" ]; then  
  EXPMODE="Stopping network"  
elif [ "$MODE" == "generate" ]; then  
  EXPMODE="Generating certs and organization definition for Org3"  
else  
  printHelp  
  exit 1  
fi  
  
#Create the network using docker compose  
if [ "${MODE}" == "up" ]; then  
  addOrg3  
elif [ "${MODE}" == "down" ]; then ## Clear the network  
  networkDown  
elif [ "${MODE}" == "generate" ]; then ## Generate Artifacts  
  generateOrg3  
  generateOrg3Definitionelse  
  printHelp  
  exit 1  
fi
```
Đúng vậy, bạn cần phải thực hiện các bước như tạo chứng chỉ và tệp CCP **cho Org3** trước khi có thể thêm tổ chức này vào channel. Tuy nhiên, việc này được thực hiện **sau khi** channel ban đầu với Org1 và Org2 đã được tạo.

Dựa trên kịch bản `addOrg3.sh` bạn cung cấp, đây là quy trình chuẩn để thêm một tổ chức mới (Org3) vào một mạng Fabric hiện có.

---

### ## Quy trình thêm Org3 vào Mạng hiện có

Quy trình này được chia thành các bước riêng biệt, tất cả đều được tự động hóa bởi kịch bản `addOrg3.sh`.

#### **1. Tạo định danh (Generate Certificates)**

- **Mục đích:** Mỗi tổ chức, peer, và người dùng trong Fabric cần một danh tính kỹ thuật số dưới dạng chứng chỉ x.509 để tham gia mạng.
    
- **Hành động:** Kịch bản chạy công cụ `cryptogen` (hoặc Fabric CA) để tạo ra toàn bộ chứng chỉ và khóa riêng tư cần thiết cho Org3. Các tệp này được lưu trong thư mục `organizations/peerOrganizations/org3.example.com`.
    

#### **2. Tạo tệp CCP (Generate CCP)**

- **Mục đích:** Tạo tệp cấu hình kết nối cho các ứng dụng client.
    
- **Hành động:** Kịch bản chạy `ccp-generate.sh` để tạo ra tệp JSON/YAML chứa thông tin kết nối cho Org3.
    

#### **3. Tạo Định nghĩa Org (Generate Org Definition)**

- **Mục đích:** Tạo một tệp JSON định nghĩa chính thức Org3. Tệp này chứa thông tin về MSP ID, chính sách, và đặc biệt là địa chỉ của **anchor peer**, giúp các peer của tổ chức khác có thể khám phá ra peer của Org3.
    
- **Hành động:** Kịch bản chạy `configtxgen -printOrg` để tạo tệp `org3.json`.
    

#### **4. Khởi chạy Nodes của Org3 (Start Org3's Nodes)**

- **Mục đích:** Khởi động các peer và database (nếu có) của Org3.
    
- **Hành động:** Kịch bản sử dụng `docker-compose` để chạy các container cho Org3.
    

#### **5. Cập nhật Cấu hình Channel 🤝**

- **Mục đích:** Đây là bước quan trọng nhất. Để Org3 chính thức trở thành thành viên của channel, cấu hình của channel phải được cập nhật và được sự đồng thuận của các thành viên hiện tại (Org1 và Org2).
    
- **Hành động:** Kịch bản sẽ tạo một giao dịch cập nhật cấu hình (config update transaction) bao gồm định nghĩa của Org3 (từ tệp `org3.json`). Giao dịch này sau đó phải được ký bởi các quản trị viên của Org1 và Org2 trước khi gửi lên orderer.
    

#### **6. Join Peer vào Channel 🚀**

- **Mục đích:** Sau khi channel đã được cập nhật để "biết" về Org3, các peer của Org3 giờ đây có thể tham gia vào channel.
    
- **Hành động:** Kịch bản chạy lệnh `peer channel join` cho từng peer của Org3.
    

---

### ## Giải đáp các câu hỏi của bạn

#### **CCP là gì?**

**CCP** là viết tắt của **Common Connection Profile** (Hồ sơ kết nối chung).

Nó là một tệp JSON hoặc YAML hoạt động giống như một **cuốn danh bạ** 📖 cho ứng dụng client của bạn. Thay vì phải lập trình cứng tất cả địa chỉ của các peer và orderer, ứng dụng của bạn chỉ cần đọc tệp CCP này. Nó chứa tất cả thông tin cần thiết để kết nối và tương tác với mạng Fabric, bao gồm:

- Địa chỉ của các **Peers** trong mỗi tổ chức.
    
- Địa chỉ của **Ordering Service**.
    
- Địa chỉ của **Certificate Authority (CA)** của tổ chức.
    
- Đường dẫn đến các chứng chỉ TLS cần thiết để giao tiếp an toàn.
    

Việc sử dụng CCP giúp ứng dụng của bạn trở nên linh hoạt và dễ quản lý hơn rất nhiều.

#### **Có cần làm các bước này trước khi tạo channel không?**

**Không.** Quy trình này dành cho việc thêm một tổ chức mới vào một channel **đã tồn tại**.

Mạng Fabric ban đầu với Org1 và Org2 phải được khởi tạo và channel (`mychannel`) phải được tạo trước. Kịch bản `addOrg3.sh` đã kiểm tra điều này:

Bash

```
# Nếu mạng ban đầu chưa hoạt động, sẽ báo lỗi
if [ ! -d ../organizations/ordererOrganizations ]; then
  fatalln "ERROR: Please, run ./network.sh up createChannel first."
fi
```
---
# Lỗi deploy org3 peers:
```

2025-07-28 03:18:42.568 UTC 0001 ERRO [main] InitCmd -> Cannot run peer because cannot init crypto, specified path "/etc/hyperledger/fabric/msp" does not exist or cannot be accessed: stat /etc/hyperledger/fabric/msp: no such file or directory
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3$ ls
addOrg3.sh*  ccp-generate.sh*  ccp-template.json  ccp-template.yaml  compose/  configtx.yaml  fabric-ca/  log.txt  org3-crypto.yaml  organizations/  README.md
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3$ cd ..
vessel@vessel-pc:~/src/fabric-demo/test-network$ ls
addOrg3/     bin/       CHAINCODE_AS_A_SERVICE_TUTORIAL.md  compose/  configtx/           log.txt            network.config  organizations/       README.md  setOrgEnv.sh*
bft-config/  builders/  channel-artifacts/                  config/   install-fabric.sh*  monitordocker.sh*  network.sh*     prometheus-grafana/  scripts/
vessel@vessel-pc:~/src/fabric-demo/test-network$ cd organizations/
vessel@vessel-pc:~/src/fabric-demo/test-network/organizations$ ls -R
.:
ccp-generate.sh*  ccp-template.json*  ccp-template.yaml*  cfssl/  cryptogen/  fabric-ca/  ordererOrganizations/  peerOrganizations/

./cfssl:
admin-csr-template.json  ca-orderer.json  ca-peer.json  cert-signing-config.json  client-csr-template.json  orderer-csr-template.json  peer-csr-template.json  registerEnroll.sh*

./cryptogen:
crypto-config-orderer.yaml*  crypto-config-org1.yaml*  crypto-config-org2.yaml*

./fabric-ca:
registerEnroll.sh*

./ordererOrganizations:
example.com/

./ordererOrganizations/example.com:
ca/  msp/  orderers/  tlsca/  users/

./ordererOrganizations/example.com/ca:
ca.example.com-cert.pem  priv_sk

./ordererOrganizations/example.com/msp:
admincerts/  cacerts/  config.yaml  tlscacerts/

./ordererOrganizations/example.com/msp/admincerts:

./ordererOrganizations/example.com/msp/cacerts:
ca.example.com-cert.pem

./ordererOrganizations/example.com/msp/tlscacerts:
tlsca.example.com-cert.pem

./ordererOrganizations/example.com/orderers:
orderer2.example.com/  orderer3.example.com/  orderer4.example.com/  orderer.example.com/

./ordererOrganizations/example.com/orderers/orderer2.example.com:
msp/  tls/

./ordererOrganizations/example.com/orderers/orderer2.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./ordererOrganizations/example.com/orderers/orderer2.example.com/msp/admincerts:

./ordererOrganizations/example.com/orderers/orderer2.example.com/msp/cacerts:
ca.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer2.example.com/msp/keystore:
priv_sk

./ordererOrganizations/example.com/orderers/orderer2.example.com/msp/signcerts:
orderer2.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer2.example.com/msp/tlscacerts:
tlsca.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer2.example.com/tls:
ca.crt  server.crt  server.key

./ordererOrganizations/example.com/orderers/orderer3.example.com:
msp/  tls/

./ordererOrganizations/example.com/orderers/orderer3.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./ordererOrganizations/example.com/orderers/orderer3.example.com/msp/admincerts:

./ordererOrganizations/example.com/orderers/orderer3.example.com/msp/cacerts:
ca.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer3.example.com/msp/keystore:
priv_sk

./ordererOrganizations/example.com/orderers/orderer3.example.com/msp/signcerts:
orderer3.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer3.example.com/msp/tlscacerts:
tlsca.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer3.example.com/tls:
ca.crt  server.crt  server.key

./ordererOrganizations/example.com/orderers/orderer4.example.com:
msp/  tls/

./ordererOrganizations/example.com/orderers/orderer4.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./ordererOrganizations/example.com/orderers/orderer4.example.com/msp/admincerts:

./ordererOrganizations/example.com/orderers/orderer4.example.com/msp/cacerts:
ca.example.com-cert.pemt

./ordererOrganizations/example.com/orderers/orderer4.example.com/msp/keystore:
priv_sk

./ordererOrganizations/example.com/orderers/orderer4.example.com/msp/signcerts:
orderer4.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer4.example.com/msp/tlscacerts:
tlsca.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer4.example.com/tls:
ca.crt  server.crt  server.key

./ordererOrganizations/example.com/orderers/orderer.example.com:
msp/  tls/

./ordererOrganizations/example.com/orderers/orderer.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./ordererOrganizations/example.com/orderers/orderer.example.com/msp/admincerts:

./ordererOrganizations/example.com/orderers/orderer.example.com/msp/cacerts:
ca.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer.example.com/msp/keystore:
priv_sk

./ordererOrganizations/example.com/orderers/orderer.example.com/msp/signcerts:
orderer.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts:
tlsca.example.com-cert.pem

./ordererOrganizations/example.com/orderers/orderer.example.com/tls:
ca.crt  server.crt  server.key

./ordererOrganizations/example.com/tlsca:
priv_sk  tlsca.example.com-cert.pem

./ordererOrganizations/example.com/users:
'Admin@example.com'/

./ordererOrganizations/example.com/users/Admin@example.com:
msp/  tls/

./ordererOrganizations/example.com/users/Admin@example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./ordererOrganizations/example.com/users/Admin@example.com/msp/admincerts:

./ordererOrganizations/example.com/users/Admin@example.com/msp/cacerts:
ca.example.com-cert.pem

./ordererOrganizations/example.com/users/Admin@example.com/msp/keystore:
priv_sk

./ordererOrganizations/example.com/users/Admin@example.com/msp/signcerts:
'Admin@example.com-cert.pem'

./ordererOrganizations/example.com/users/Admin@example.com/msp/tlscacerts:
tlsca.example.com-cert.pem

./ordererOrganizations/example.com/users/Admin@example.com/tls:
ca.crt  client.crt  client.key

./peerOrganizations:
org1.example.com/  org2.example.com/  org3.example.com/

./peerOrganizations/org1.example.com:
ca/  connection-org1.json  connection-org1.yaml  msp/  peers/  tlsca/  users/

./peerOrganizations/org1.example.com/ca:
ca.org1.example.com-cert.pem  priv_sk

./peerOrganizations/org1.example.com/msp:
admincerts/  cacerts/  config.yaml  tlscacerts/

./peerOrganizations/org1.example.com/msp/admincerts:

./peerOrganizations/org1.example.com/msp/cacerts:
ca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/msp/tlscacerts:
tlsca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers:
peer0.org1.example.com/  peer1.org1.example.com/  peer2.org1.example.com/

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com:
msp/  tls/

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/admincerts:

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/cacerts:
ca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/keystore:
priv_sk

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/signcerts:
peer0.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/tlscacerts:
tlsca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com:
msp/  tls/

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/admincerts:

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/cacerts:
ca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/keystore:
priv_sk

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/signcerts:
peer1.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/tlscacerts:
tlsca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com:
msp/  tls/

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/admincerts:

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/cacerts:
ca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/keystore:
priv_sk

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/signcerts:
peer2.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/tlscacerts:
tlsca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/peers/peer2.org1.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org1.example.com/tlsca:
priv_sk  tlsca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/users:
'Admin@org1.example.com'/  'User1@org1.example.com'/

./peerOrganizations/org1.example.com/users/Admin@org1.example.com:
msp/  tls/

./peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/admincerts:

./peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/cacerts:
ca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/keystore:
priv_sk

./peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/signcerts:
'Admin@org1.example.com-cert.pem'

./peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/tlscacerts:
tlsca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/users/Admin@org1.example.com/tls:
ca.crt  client.crt  client.key

./peerOrganizations/org1.example.com/users/User1@org1.example.com:
msp/  tls/

./peerOrganizations/org1.example.com/users/User1@org1.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/admincerts:

./peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/cacerts:
ca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/keystore:
priv_sk

./peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/signcerts:
'User1@org1.example.com-cert.pem'

./peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/tlscacerts:
tlsca.org1.example.com-cert.pem

./peerOrganizations/org1.example.com/users/User1@org1.example.com/tls:
ca.crt  client.crt  client.key

./peerOrganizations/org2.example.com:
ca/  connection-org2.json  connection-org2.yaml  msp/  peers/  tlsca/  users/

./peerOrganizations/org2.example.com/ca:
ca.org2.example.com-cert.pem  priv_sk

./peerOrganizations/org2.example.com/msp:
admincerts/  cacerts/  config.yaml  tlscacerts/

./peerOrganizations/org2.example.com/msp/admincerts:

./peerOrganizations/org2.example.com/msp/cacerts:
ca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/msp/tlscacerts:
tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers:
peer0.org2.example.com/  peer1.org2.example.com/  peer2.org2.example.com/

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com:
msp/  tls/

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/admincerts:

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/cacerts:
ca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/keystore:
priv_sk

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/signcerts:
peer0.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/tlscacerts:
tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com:
msp/  tls/

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp/admincerts:

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp/cacerts:
ca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp/keystore:
priv_sk

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp/signcerts:
peer1.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com/msp/tlscacerts:
tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com:
msp/  tls/

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com/msp/admincerts:

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com/msp/cacerts:
ca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com/msp/keystore:
priv_sk

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com/msp/signcerts:
peer2.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com/msp/tlscacerts:
tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/peers/peer2.org2.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org2.example.com/tlsca:
priv_sk  tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/users:
'Admin@org2.example.com'/  'User1@org2.example.com'/  'User2@org2.example.com'/

./peerOrganizations/org2.example.com/users/Admin@org2.example.com:
msp/  tls/

./peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/admincerts:

./peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/cacerts:
ca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/keystore:
priv_sk

./peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/signcerts:
'Admin@org2.example.com-cert.pem'

./peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/tlscacerts:
tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/users/Admin@org2.example.com/tls:
ca.crt  client.crt  client.key

./peerOrganizations/org2.example.com/users/User1@org2.example.com:
msp/  tls/

./peerOrganizations/org2.example.com/users/User1@org2.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/admincerts:

./peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/cacerts:
ca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/keystore:
priv_sk

./peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/signcerts:
'User1@org2.example.com-cert.pem'

./peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/tlscacerts:
tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/users/User1@org2.example.com/tls:
ca.crt  client.crt  client.key

./peerOrganizations/org2.example.com/users/User2@org2.example.com:
msp/  tls/

./peerOrganizations/org2.example.com/users/User2@org2.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org2.example.com/users/User2@org2.example.com/msp/admincerts:

./peerOrganizations/org2.example.com/users/User2@org2.example.com/msp/cacerts:
ca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/users/User2@org2.example.com/msp/keystore:
priv_sk

./peerOrganizations/org2.example.com/users/User2@org2.example.com/msp/signcerts:
'User2@org2.example.com-cert.pem'

./peerOrganizations/org2.example.com/users/User2@org2.example.com/msp/tlscacerts:
tlsca.org2.example.com-cert.pem

./peerOrganizations/org2.example.com/users/User2@org2.example.com/tls:
ca.crt  client.crt  client.key

./peerOrganizations/org3.example.com:
ca/  connection-org3.json  connection-org3.yaml  msp/  org3.json  peers/  tlsca/  users/

./peerOrganizations/org3.example.com/ca:
ca.org3.example.com-cert.pem  priv_sk

./peerOrganizations/org3.example.com/msp:
admincerts/  cacerts/  config.yaml  tlscacerts/

./peerOrganizations/org3.example.com/msp/admincerts:

./peerOrganizations/org3.example.com/msp/cacerts:
ca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/msp/tlscacerts:
tlsca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers:
peer0.org3.example.com/  peer1.org3.example.com/  peer2.org3.example.com/

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com:
msp/  tls/

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com/msp/admincerts:

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com/msp/cacerts:
ca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com/msp/keystore:
priv_sk

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com/msp/signcerts:
peer0.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com/msp/tlscacerts:
tlsca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer0.org3.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com:
msp/  tls/

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com/msp/admincerts:

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com/msp/cacerts:
ca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com/msp/keystore:
priv_sk

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com/msp/signcerts:
peer1.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com/msp/tlscacerts:
tlsca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer1.org3.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com:
msp/  tls/

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com/msp/admincerts:

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com/msp/cacerts:
ca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com/msp/keystore:
priv_sk

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com/msp/signcerts:
peer2.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com/msp/tlscacerts:
tlsca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/peers/peer2.org3.example.com/tls:
ca.crt  server.crt  server.key

./peerOrganizations/org3.example.com/tlsca:
priv_sk  tlsca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/users:
'Admin@org3.example.com'/  'User1@org3.example.com'/

./peerOrganizations/org3.example.com/users/Admin@org3.example.com:
msp/  tls/

./peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp/admincerts:

./peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp/cacerts:
ca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp/keystore:
priv_sk

./peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp/signcerts:
'Admin@org3.example.com-cert.pem'

./peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp/tlscacerts:
tlsca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/users/Admin@org3.example.com/tls:
ca.crt  client.crt  client.key

./peerOrganizations/org3.example.com/users/User1@org3.example.com:
msp/  tls/

./peerOrganizations/org3.example.com/users/User1@org3.example.com/msp:
admincerts/  cacerts/  config.yaml  keystore/  signcerts/  tlscacerts/

./peerOrganizations/org3.example.com/users/User1@org3.example.com/msp/admincerts:

./peerOrganizations/org3.example.com/users/User1@org3.example.com/msp/cacerts:
ca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/users/User1@org3.example.com/msp/keystore:
priv_sk

./peerOrganizations/org3.example.com/users/User1@org3.example.com/msp/signcerts:
'User1@org3.example.com-cert.pem'

./peerOrganizations/org3.example.com/users/User1@org3.example.com/msp/tlscacerts:
tlsca.org3.example.com-cert.pem

./peerOrganizations/org3.example.com/users/User1@org3.example.com/tls:
ca.crt  client.crt  client.key
vessel@vessel-pc:~/src/fabric-demo/test-network/organizations$ ls
ccp-generate.sh*  ccp-template.json*  ccp-template.yaml*  cfssl/  cryptogen/  fabric-ca/  ordererOrganizations/  peerOrganizations/
vessel@vessel-pc:~/src/fabric-demo/test-network/organizations$ z ../
addOrg3/            bin/                channel-artifacts/  config/             organizations/      scripts/
bft-config/         builders/           compose/            configtx/           prometheus-grafana/
vessel@vessel-pc:~/src/fabric-demo/test-network/organizations$ z ../addOrg3/
compose/       fabric-ca/     organizations/
vessel@vessel-pc:~/src/fabric-demo/test-network/organizations$ z ../addOrg3/compose/
docker/ podman/
vessel@vessel-pc:~/src/fabric-demo/test-network/organizations$ z ../addOrg3/compose/
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3/compose$ ls
compose-ca-org3.yaml  compose-couch-org3.yaml  compose-org3.yaml  docker/  podman/
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3/compose$ cat compose-org3.yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

version: '3.7'

volumes:
  peer0.org3.example.com:
  peer1.org3.example.com:
  peer2.org3.example.com:

networks:
  test:
    name: fabric_test

services:

  peer0.org3.example.com:
    container_name: peer0.org3.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_PROFILE_ENABLED=false
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # Peer specific variables
      - CORE_PEER_ID=peer0.org3.example.com
      - CORE_PEER_ADDRESS=peer0.org3.example.com:8200
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp
      - CORE_PEER_LISTENADDRESS=0.0.0.0:8200
      - CORE_PEER_CHAINCODEADDRESS=peer0.org3.example.com:8201
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:8201
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org3.example.com:8200
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org3.example.com:8200
      - CORE_PEER_LOCALMSPID=Org3MSP
      - CORE_OPERATIONS_LISTENADDRESS=peer0.org3.example.com:8205
      - CORE_METRICS_PROVIDER=prometheus
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer0org3"}
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s
    volumes:
      - ../organizations/peerOrganizations/org3.example.com/peers/peer0.org3.example.com:/etc/hyperledger/fabric
      - peer0.org3.example.com:/var/hyperledger/production
    working_dir: /root
    command: peer node start
    ports:
      - 8200:8200
      - 8205:8205
    networks:
      - test

  peer1.org3.example.com:
    container_name: peer1.org3.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_PROFILE_ENABLED=false
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # Peer specific variables
      - CORE_PEER_ID=peer1.org3.example.com
      - CORE_PEER_ADDRESS=peer1.org3.example.com:8220
      - CORE_PEER_LISTENADDRESS=0.0.0.0:8220
      - CORE_PEER_CHAINCODEADDRESS=peer1.org3.example.com:8221
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:8221
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.org3.example.com:8220
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org3.example.com:8200
      - CORE_PEER_LOCALMSPID=Org3MSP
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp
      - CORE_OPERATIONS_LISTENADDRESS=peer1.org3.example.com:8225
      - CORE_METRICS_PROVIDER=prometheus
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer1org3"}
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s
    volumes:
      - ../organizations/peerOrganizations/org3.example.com/peers/peer1.org3.example.com:/etc/hyperledger/fabric
      - peer1.org3.example.com:/var/hyperledger/production
    working_dir: /root
    command: peer node start
    ports:
      - 8220:8220
      - 8225:8225
    networks:
      - test

  peer2.org3.example.com:
    container_name: peer2.org3.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg
      - FABRIC_LOGGING_SPEC=INFO
      #- FABRIC_LOGGING_SPEC=DEBUG
      - CORE_PEER_TLS_ENABLED=true
      - CORE_PEER_PROFILE_ENABLED=false
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
      # Peer specific variables
      - CORE_PEER_ID=peer2.org3.example.com
      - CORE_PEER_ADDRESS=peer2.org3.example.com:8240
      - CORE_PEER_LISTENADDRESS=0.0.0.0:8240
      - CORE_PEER_CHAINCODEADDRESS=peer2.org3.example.com:8241
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:8241
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer2.org3.example.com:8240
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org3.example.com:8200
      - CORE_PEER_LOCALMSPID=Org3MSP
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp
      - CORE_OPERATIONS_LISTENADDRESS=peer2.org3.example.com:8245
      - CORE_METRICS_PROVIDER=prometheus
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer2org3"}
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s
    volumes:
      - ../organizations/peerOrganizations/org3.example.com/peers/peer2.org3.example.com:/etc/hyperledger/fabric
      - peer2.org3.example.com:/var/hyperledger/production
    working_dir: /root
    command: peer node start
    ports:
      - 8240:8240
      - 8245:8245
    networks:
      - test
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3/compose$ cat
compose-ca-org3.yaml     compose-couch-org3.yaml  compose-org3.yaml        docker/                  podman/
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3/compose$ cat docker/
docker-compose-ca-org3.yaml     docker-compose-couch-org3.yaml  docker-compose-org3.yaml        peercfg/
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3/compose$ cat docker/docker-compose-org3.yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

version: '3.7'

networks:
  test:
    name: fabric_test

services:

  peer0.org3.example.com:
    container_name: peer0.org3.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      #Generic peer variables
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=fabric_test
    volumes:
      - ./docker/peercfg:/etc/hyperledger/peercfg
      - ${DOCKER_SOCK}:/host/var/run/docker.sock

  peer1.org3.example.com:
    container_name: peer1.org3.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      #Generic peer variables
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=fabric_test
    volumes:
      - ./docker/peercfg:/etc/hyperledger/peercfg
      - ${DOCKER_SOCK}:/host/var/run/docker.sock

  peer2.org3.example.com:
    container_name: peer2.org3.example.com
    image: hyperledger/fabric-peer:latest
    labels:
      service: hyperledger-fabric
    environment:
      #Generic peer variables
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=fabric_test
    volumes:
      - ./docker/peercfg:/etc/hyperledger/peercfg
      - ${DOCKER_SOCK}:/host/var/run/docker.sock
vessel@vessel-pc:~/src/fabric-demo/test-network/addOrg3/compose$

```

---
# Chỉnh sửa deployCC để có thể tùy biến deploy trên 2 hoặc 3 org
script deployCC.sh hiện tại chỉ deploy lên các peers của org1, org2 mà ko deploy trên org3, hãy chỉnh sửa để có thể deploy trên org2 hoặc org3 tùy theo số lượng org
```
#!/usr/bin/env bash  
  
source scripts/utils.sh  
  
# --- Parameter Defaults ---  
CHANNEL_NAME=${1:-"mychannel"}  
CC_NAME=${2}  
CC_SRC_PATH=${3}  
CC_SRC_LANGUAGE=${4}  
CC_VERSION=${5:-"1.0"}  
CC_SEQUENCE=${6:-"1"}  
CC_INIT_FCN=${7:-"NA"}  
CC_END_POLICY=${8:-"NA"}  
CC_COLL_CONFIG=${9:-"NA"}  
DELAY=${10:-"3"}  
MAX_RETRY=${11:-"5"}  
VERBOSE=${12:-"false"}  
  
# --- Print Parameters ---  
println "executing with the following"  
println "- CHANNEL_NAME: ${C_GREEN}${CHANNEL_NAME}${C_RESET}"  
println "- CC_NAME: ${C_GREEN}${CC_NAME}${C_RESET}"  
println "- CC_SRC_PATH: ${C_GREEN}${CC_SRC_PATH}${C_RESET}"  
println "- CC_SRC_LANGUAGE: ${C_GREEN}${CC_SRC_LANGUAGE}${C_RESET}"  
println "- CC_VERSION: ${C_GREEN}${CC_VERSION}${C_RESET}"  
println "- CC_SEQUENCE: ${C_GREEN}${CC_SEQUENCE}${C_RESET}"  
println "- CC_END_POLICY: ${C_GREEN}${CC_END_POLICY}${C_RESET}"  
println "- CC_COLL_CONFIG: ${C_GREEN}${CC_COLL_CONFIG}${C_RESET}"  
println "- CC_INIT_FCN: ${C_GREEN}${CC_INIT_FCN}${C_RESET}"  
println "- DELAY: ${C_GREEN}${DELAY}${C_RESET}"  
println "- MAX_RETRY: ${C_GREEN}${MAX_RETRY}${C_RESET}"  
println "- VERBOSE: ${C_GREEN}${VERBOSE}${C_RESET}"  
  
# --- Argument Parsing for Flags ---  
INIT_REQUIRED="--init-required"  
if [ "$CC_INIT_FCN" = "NA" ]; then  
  INIT_REQUIRED=""  
fi  
  
if [ "$CC_END_POLICY" = "NA" ]; then  
  CC_END_POLICY=""  
else  
  CC_END_POLICY="--signature-policy $CC_END_POLICY"  
fi  
  
if [ "$CC_COLL_CONFIG" = "NA" ]; then  
  CC_COLL_CONFIG=""  
else  
  CC_COLL_CONFIG="--collections-config $CC_COLL_CONFIG"  
fi  
  
FABRIC_CFG_PATH=$PWD/../config/  
  
# --- Import helpers ---  
. scripts/envVar.sh  
. scripts/ccutils.sh  
  
function checkPrereqs() {  
  jq --version > /dev/null 2>&1  
  
  if [[ $? -ne 0 ]]; then  
    errorln "jq command not found..."  
    errorln  
    errorln "Follow the instructions in the Fabric docs to install the prereqs"  
    errorln "https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html"  
    exit 1  
  fi  
}  
  
#check for prerequisites  
checkPrereqs  
  
# --- Main Deployment Logic ---  
  
## 1. Package the chaincode  
infoln "Packaging chaincode..."  
./scripts/packageCC.sh "$CC_NAME" "$CC_SRC_PATH" "$CC_SRC_LANGUAGE" "$CC_VERSION"  
  
infoln "Calculating new chaincode package ID..."  
export PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid ${CC_NAME}.tar.gz)  
println "Package ID is: ${C_GREEN}${PACKAGE_ID}${C_RESET}"  
  
## 2. Install chaincode on all peers  
infoln "Installing chaincode on Org1 peers..."  
installChaincode 1 0  
installChaincode 1 1  
installChaincode 1 2  
  
infoln "Installing chaincode on Org2 peers..."  
installChaincode 2 0  
installChaincode 2 1  
installChaincode 2 2  
  
resolveSequence  
  
### query whether the chaincode is installed  
#queryInstalled 1  
  
## 3. Approve the definition for each organization  
infoln "Approving chaincode definition for Org1..."  
approveForMyOrg 1  
  
infoln "Approving chaincode definition for Org2..."  
approveForMyOrg 2  
  
## 4. Check commit readiness. This is the only check needed.  
infoln "Checking if chaincode definition is ready to be committed..."  
checkCommitReadiness 1 "\"Org1MSP\": true" "\"Org2MSP\": true"  
checkCommitReadiness 2 "\"Org1MSP\": true" "\"Org2MSP\": true"  
  
## 5. Commit the chaincode definition  
infoln "Committing chaincode definition..."  
commitChaincodeDefinition 1 2  
  
## 6. Query the committed chaincode definition on all orgs  
infoln "Querying committed definition on Org1..."  
queryCommitted 1  
infoln "Querying committed definition on Org2..."  
queryCommitted 2  
  
## 7. Initialize the chaincode if required  
if [ "$CC_INIT_FCN" != "NA" ]; then  
  infoln "Initializing chaincode..."  
  chaincodeInvokeInit 1 2  
else  
  infoln "Chaincode initialization is not required."  
fi  
  
println "✅ Chaincode deployment successful."exit 0
```
---
# Sửa script deployCC.sh để deployCC cho mỗi org3 (kịch bản thêm org3 vào channel và cần cài đặt chaincode lên các peers của org3)
deployCC.sh
```
#!/usr/bin/env bash  
  
## Exit on first error  
#set -e  
  
source scripts/utils.sh  
  
# --- Parameter Defaults ---  
CHANNEL_NAME=${1:-"mychannel"}  
CC_NAME=${2}  
CC_SRC_PATH=${3}  
CC_SRC_LANGUAGE=${4}  
CC_VERSION=${5:-"1.0"}  
CC_SEQUENCE=${6:-"1"}  
CC_INIT_FCN=${7:-"NA"}  
CC_END_POLICY=${8:-"NA"}  
CC_COLL_CONFIG=${9:-"NA"}  
DELAY=${10:-"3"}  
MAX_RETRY=${11:-"5"}  
VERBOSE=${12:-"false"}  
# Thêm tham số mới để xác định số lượng tổ chức, mặc định là 2  
NUM_ORGS=${13:-2}  
  
# --- Print Parameters ---  
println "executing with the following"  
println "- CHANNEL_NAME: ${C_GREEN}${CHANNEL_NAME}${C_RESET}"  
println "- CC_NAME: ${C_GREEN}${CC_NAME}${C_RESET}"  
println "- CC_SRC_PATH: ${C_GREEN}${CC_SRC_PATH}${C_RESET}"  
println "- CC_SRC_LANGUAGE: ${C_GREEN}${CC_SRC_LANGUAGE}${C_RESET}"  
println "- CC_VERSION: ${C_GREEN}${CC_VERSION}${C_RESET}"  
println "- CC_SEQUENCE: ${C_GREEN}${CC_SEQUENCE}${C_RESET}"  
println "- CC_END_POLICY: ${C_GREEN}${CC_END_POLICY}${C_RESET}"  
println "- CC_COLL_CONFIG: ${C_GREEN}${CC_COLL_CONFIG}${C_RESET}"  
println "- CC_INIT_FCN: ${C_GREEN}${CC_INIT_FCN}${C_RESET}"  
println "- DELAY: ${C_GREEN}${DELAY}${C_RESET}"  
println "- MAX_RETRY: ${C_GREEN}${MAX_RETRY}${C_RESET}"  
println "- VERBOSE: ${C_GREEN}${VERBOSE}${C_RESET}"  
println "- NUM_ORGS: ${C_GREEN}${NUM_ORGS}${C_RESET}" # In ra số lượng org  
  
# --- Argument Parsing for Flags ---  
INIT_REQUIRED="--init-required"  
if [ "$CC_INIT_FCN" = "NA" ]; then  
  INIT_REQUIRED=""  
fi  
  
if [ "$CC_END_POLICY" = "NA" ]; then  
  CC_END_POLICY=""  
else  
  CC_END_POLICY="--signature-policy $CC_END_POLICY"  
fi  
  
if [ "$CC_COLL_CONFIG" = "NA" ]; then  
  CC_COLL_CONFIG=""  
else  
  CC_COLL_CONFIG="--collections-config $CC_COLL_CONFIG"  
fi  
  
FABRIC_CFG_PATH=$PWD/../config/  
  
# --- Import helpers ---  
. scripts/envVar.sh  
. scripts/ccutils.sh  
  
function checkPrereqs() {  
  jq --version > /dev/null 2>&1  
  
  if [[ $? -ne 0 ]]; then  
    errorln "jq command not found..."  
    errorln  
    errorln "Follow the instructions in the Fabric docs to install the prereqs"  
    errorln "https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html"  
    exit 1  
  fi  
}  
  
#check for prerequisites  
checkPrereqs  
  
# --- Main Deployment Logic ---  
  
## 1. Package the chaincode  
infoln "Packaging chaincode..."  
./scripts/packageCC.sh "$CC_NAME" "$CC_SRC_PATH" "$CC_SRC_LANGUAGE" "$CC_VERSION"  
  
infoln "Calculating new chaincode package ID..."  
export PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid ${CC_NAME}.tar.gz)  
println "Package ID is: ${C_GREEN}${PACKAGE_ID}${C_RESET}"  
  
## 2. Install chaincode on all peers  
infoln "Installing chaincode on Org1 peers..."  
installChaincode 1 0  
installChaincode 1 1  
installChaincode 1 2  
  
infoln "Installing chaincode on Org2 peers..."  
installChaincode 2 0  
installChaincode 2 1  
installChaincode 2 2  
  
# Chỉ cài đặt cho Org3 nếu số lượng tổ chức là 3  
if [ "$NUM_ORGS" -ge 3 ]; then  
  infoln "Installing chaincode on Org3 peers..."  
  installChaincode 3 0  
  installChaincode 3 1  
  installChaincode 3 2  
fi  
  
resolveSequence  
  
## 3. Approve the definition for each organization  
infoln "Approving chaincode definition for Org1..."  
approveForMyOrg 1  
  
infoln "Approving chaincode definition for Org2..."  
approveForMyOrg 2  
  
# Chỉ approve cho Org3 nếu số lượng tổ chức là 3  
if [ "$NUM_ORGS" -ge 3 ]; then  
  infoln "Approving chaincode definition for Org3..."  
  approveForMyOrg 3  
fi  
  
  
## 4. Check commit readiness and Commit the chaincode definition  
if [ "$NUM_ORGS" -ge 3 ]; then  
  # Logic cho 3 tổ chức  
  infoln "Checking if chaincode definition is ready for 3 Orgs..."  
  checkCommitReadiness 1 "\"Org1MSP\": true" "\"Org2MSP\": true" "\"Org3MSP\": true"  
  checkCommitReadiness 2 "\"Org1MSP\": true" "\"Org2MSP\": true" "\"Org3MSP\": true"  
  checkCommitReadiness 3 "\"Org1MSP\": true" "\"Org2MSP\": true" "\"Org3MSP\": true"  
  
  infoln "Committing chaincode definition for 3 Orgs..."  
  commitChaincodeDefinition 1 2 3  
else  
  # Logic cho 2 tổ chức (mặc định)  
  infoln "Checking if chaincode definition is ready for 2 Orgs..."  
  checkCommitReadiness 1 "\"Org1MSP\": true" "\"Org2MSP\": true"  
  checkCommitReadiness 2 "\"Org1MSP\": true" "\"Org2MSP\": true"  
  
  infoln "Committing chaincode definition for 2 Orgs..."  
  commitChaincodeDefinition 1 2  
fi  
  
  
## 5. Query the committed chaincode definition on all orgs  
infoln "Querying committed definition on Org1..."  
queryCommitted 1  
infoln "Querying committed definition on Org2..."  
queryCommitted 2  
  
# Chỉ query cho Org3 nếu số lượng tổ chức là 3  
if [ "$NUM_ORGS" -ge 3 ]; then  
  infoln "Querying committed definition on Org3..."  
  queryCommitted 3  
fi  
  
## 6. Initialize the chaincode if required  
if [ "$CC_INIT_FCN" != "NA" ]; then  
  infoln "Initializing chaincode..."  
  if [ "$NUM_ORGS" -ge 3 ]; then  
    chaincodeInvokeInit 1 2 3  
  else  
    chaincodeInvokeInit 1 2  
  fi  
else  
  infoln "Chaincode initialization is not required."  
fi  
  
println "✅ Chaincode deployment successful."exit 0
```
---
# Khi các peers của tổ chức 3 tham gia vào mạng, nó không được cài sẵn các chaincode đã có trên channel, phải làm sao để cài được các chaincode đã có ?
-  **Cách 1**: cài đặt và approve chaincode đã có sẵn (sau khi cài đặt chaincode lên các peers của org3, org3 có thể tham gia vào quá trình endorse) (done)
	`./network.sh deployCCOrg3 -c mychannel -ccn basic -ccl go -ccp ../asset-transfer-basic/chaincode-go`

- **Cách 2**: nâng cấp chaincode --> bước này sẽ cài đặt chaincode trên cả 3 orgs, đồng thời cập nhật lại policy tương ứng (tức Org3 có thể tham gia ký):
	`./network.sh deployCC -c mychannel -ccn basic -ccl go -ccp ../asset-transfer-basic/chaincode-go -norgs 3` (lệnh deploy cũng chính là lệnh để upgrade?)
Nhận định này có đúng không ? 
Và giúp tôi thực thi cách 2

---
# Version và Sequuence của chaincode dùng để làm gì ? Có thể revert chaincode bằng chúng được không ?

**Version** và **Sequence** là hai khái niệm cốt lõi dùng để quản lý và quản trị vòng đời của chaincode một cách an toàn và có kiểm soát.

Không, bạn **không thể revert** (quay lại) một phiên bản chaincode cũ một cách trực tiếp bằng chúng. Thay vào đó, bạn phải thực hiện một quy trình **nâng cấp (upgrade)** mới bằng cách sử dụng lại mã nguồn cũ.

---

### ## Mục đích của Version và Sequence

#### ### Version (Phiên bản - `v1.0`, `v1.1`, `v2.0`)

- **Mục đích:** Dùng để **đánh dấu phiên bản của mã nguồn (business logic)**. Nó mang ý nghĩa cho con người, giúp các nhà phát triển và quản trị viên theo dõi sự thay đổi trong logic của smart contract.
    
- **Khi nào thay đổi:** Bạn nên thay đổi `Version` khi bạn sửa đổi mã nguồn của chaincode, ví dụ như thêm hàm mới, sửa lỗi, hoặc thay đổi cách xử lý giao dịch.
    
- **Ví dụ:** Bạn có chaincode `basic` phiên bản `v1.0`. Sau đó, bạn sửa một lỗi và deploy lại, bạn sẽ đặt tên phiên bản mới là `v1.1`.
    

#### ### Sequence (Số thứ tự - `1`, `2`, `3`)

- **Mục đích:** Dùng để **quản trị định nghĩa chaincode trên channel**. Mỗi khi bạn muốn thay đổi bất kỳ quy tắc nào của chaincode đã được commit lên channel (như Endorsement Policy), bạn phải tăng `Sequence` lên 1.
    
- **Khi nào thay đổi:** Bạn phải tăng `Sequence` mỗi khi bạn chạy lệnh `peer lifecycle chaincode commit` để áp dụng một định nghĩa mới. Điều này áp dụng cho cả việc nâng cấp mã nguồn (thay đổi `Version`) và chỉ thay đổi cấu hình (ví dụ như cập nhật Endorsement Policy mà không thay đổi mã nguồn).
    
- **Ví dụ:** Chaincode `basic` đang ở `sequence: 1`. Khi bạn muốn thêm Org3 vào Endorsement Policy, bạn phải `approve` và `commit` một định nghĩa mới với `sequence: 2`.
    

**Tóm lại:** **Version** dành cho mã nguồn, **Sequence** dành cho quản trị trên channel.

---

### ## Tại sao không thể Revert? 🤔

Hyperledger Fabric được thiết kế với tính bất biến (immutability). Một khi một định nghĩa chaincode đã được commit lên channel ở một `sequence` nhất định, nó không thể bị "xóa" hay "quay lại". Điều này đảm bảo rằng tất cả các thành viên đều có một lịch sử quản trị nhất quán và không thể bị thay đổi.

#### ### Cách "Revert" đúng quy trình 롤백

Nếu bạn muốn quay lại sử dụng mã nguồn của phiên bản `v1.0` sau khi đã nâng cấp lên `v1.1`, bạn phải thực hiện một quy trình **nâng cấp mới**:

1. **Đóng gói (Package):** Lấy mã nguồn của phiên bản `v1.0` và đóng gói lại. Bạn có thể đặt một `label` mới để phân biệt, ví dụ `basic_1.0_revert`.
    
2. **Cài đặt (Install):** Cài đặt package mới này lên các peer cần thiết.
    
3. **Phê duyệt & Commit (Approve & Commit):** Thực hiện quy trình nâng cấp như bình thường, nhưng với:
    
    - **Package ID:** Của package mã nguồn `v1.0` bạn vừa cài đặt.
        
    - **Sequence:** Tăng lên một số mới (ví dụ, nếu hiện tại là `2`, bạn phải dùng `3`).
        
    - **Version:** Bạn có thể giữ là `v1.0` hoặc đặt một tên mới như `v1.0.1` để cho thấy đây là một lần deploy lại.
        

Bằng cách này, bạn không "quay ngược thời gian" mà là "tiến về phía trước" bằng cách deploy lại mã nguồn cũ như một phiên bản hợp lệ mới trên channel.

---
# Làm thế nào để xóa chaincode trên một channel ?

Trong Hyperledger Fabric, bạn **không thể "xóa" hoàn toàn** một chaincode đã được commit ra khỏi lịch sử của channel do tính bất biến của blockchain. Tuy nhiên, bạn có thể **vô hiệu hóa** nó để không ai có thể sử dụng được nữa và sau đó dọn dẹp các tài nguyên liên quan.

---

### ## Quy trình Vô hiệu hóa và Dọn dẹp Chaincode

Quá trình này bao gồm hai bước chính: làm cho chaincode không thể thực thi trên channel, và sau đó xóa các tài nguyên (container, image) khỏi các peer.

#### ### Bước 1: Vô hiệu hóa Chaincode trên Channel (Quan trọng nhất) 🚫

Cách tốt nhất để vô hiệu hóa một chaincode là nâng cấp nó với một **chính sách chứng thực (Endorsement Policy) không thể nào thỏa mãn được**. Khi đó, không một giao dịch nào có thể thu thập đủ chữ ký hợp lệ và chaincode sẽ trở nên vô dụng.

Ví dụ, nếu bạn có 3 tổ chức, bạn có thể đặt chính sách yêu cầu chữ ký của 4 tổ chức:

```
"OutOf(4, 'Org1MSP.peer', 'Org2MSP.peer', 'Org3MSP.peer')"
```

Vì chỉ có 3 tổ chức nên không bao giờ có thể thu thập đủ 4 chữ ký, do đó mọi giao dịch sẽ thất bại.

Cách thực hiện:

Bạn sử dụng lại kịch bản deployCC để nâng cấp chaincode với sequence mới và chính sách không thể thỏa mãn.

Bash

```
# Giả sử sequence hiện tại là 2, chúng ta sẽ nâng cấp lên 3
./network.sh deployCC \
    -c mychannel \
    -ccn basic \
    -ccl go \
    -ccp ../asset-transfer-basic/chaincode-go \
    -ccv 1.0 \
    -ccs 3 \
    -ccep "OutOf(4, 'Org1MSP.peer', 'Org2MSP.peer', 'Org3MSP.peer')" \
    -norgs 3
```

Sau khi lệnh này thành công, chaincode `basic` đã bị vô hiệu hóa trên channel `mychannel`.

#### ### Bước 2: Dọn dẹp Tài nguyên trên các Peer 🧹

Sau khi chaincode đã bị vô hiệu hóa, bạn có thể dọn dẹp các tài nguyên mà nó đang chiếm dụng trên các máy chủ peer để giải phóng dung lượng.

1. Dừng và Xóa Container Chaincode:
    
    Mỗi khi chaincode được thực thi, peer sẽ khởi chạy một container Docker riêng cho nó (tên thường bắt đầu bằng dev-peer...).
    
    - Tìm tên các container:
        
        Bash
        
        ```
        docker ps | grep dev-peer
        ```
        
    - Dừng và xóa chúng:
        
        Bash
        
        ```
        docker stop <container_name_1> <container_name_2>
        docker rm <container_name_1> <container_name_2>
        ```
        
2. Xóa Image Chaincode:
    
    Peer cũng tạo ra một image Docker cho chaincode.
    
    - Tìm tên image:
        
        Bash
        
        ```
        docker images | grep dev-peer
        ```
        
    - Xóa image đó:
        
        Bash
        
        ```
        docker rmi <image_name>
        ```
        

---

### ## Tóm lại

Bạn không "xóa" chaincode khỏi sổ cái, mà bạn **vô hiệu hóa** nó bằng cách cập nhật chính sách và sau đó **dọn dẹp** các tài nguyên vật lý (container, image) mà nó đang sử dụng. Lịch sử của tất cả các giao dịch đã được thực hiện bởi chaincode đó sẽ vẫn còn mãi mãi trên blockchain.

---
# Viết script deployCCWithPath 
```
### Organizations install different chaincode packages[¶](https://hyperledger-fabric.readthedocs.io/en/release-2.5/chaincode_lifecycle.html#organizations-install-different-chaincode-packages "Link to this heading")

Each organization can use a different packageID when they approve a chaincode definition. This allows channel members to install different chaincode binaries that use the same endorsement policy and read and write to data in the same chaincode namespace.

Organizations can use this capability to install smart contracts that contain business logic that is specific to their organization. Each organization’s smart contract could contain additional validation that the organization requires before their peers endorse a transaction. Each organization can also write code that helps integrate the smart contract with data from their existing systems.

![Using different chaincode binaries](https://hyperledger-fabric.readthedocs.io/en/release-2.5/_images/Lifecycle-binaries.png)

_Org1 and Org2 each install versions of the MYCC chaincode containing business logic that is specific to their organization._
```
-- Tài liệu Fabric
Việc này có nghĩa là cùng 1 chaincode (name, version, sequence), mỗi tổ chức có thể cài các chaincode khác nhau ? (source khác nhau)

Hãy giúp tôi viết file deployCCWithPath.sh để có thể:
```
./network.sh deployCCWithPath -c mychannel -ccn basic -ccl go -ccp ../asset-transfer-basic/chaincode-go-org1 -ccp ../asset-transfer-basic/chaincode-go-org2 -ccp ../asset-transfer-basic/chaincode-go-org3 -norgs 3
```
file deployCC.sh hiện tại:
```bash
#!/usr/bin/env bash  
  
## Exit on first error  
#set -e  
  
source scripts/utils.sh  
  
# --- Parameter Defaults ---  
CHANNEL_NAME=${1:-"mychannel"}  
CC_NAME=${2}  
CC_SRC_PATH=${3}  
CC_SRC_LANGUAGE=${4}  
CC_VERSION=${5:-"1.0"}  
CC_SEQUENCE=${6:-"1"}  
CC_INIT_FCN=${7:-"NA"}  
CC_END_POLICY=${8:-"NA"}  
CC_COLL_CONFIG=${9:-"NA"}  
DELAY=${10:-"3"}  
MAX_RETRY=${11:-"5"}  
VERBOSE=${12:-"false"}  
# Thêm tham số mới để xác định số lượng tổ chức, mặc định là 2  
NUM_ORGS=${13:-2}  
  
# --- Print Parameters ---  
println "executing with the following"  
println "- CHANNEL_NAME: ${C_GREEN}${CHANNEL_NAME}${C_RESET}"  
println "- CC_NAME: ${C_GREEN}${CC_NAME}${C_RESET}"  
println "- CC_SRC_PATH: ${C_GREEN}${CC_SRC_PATH}${C_RESET}"  
println "- CC_SRC_LANGUAGE: ${C_GREEN}${CC_SRC_LANGUAGE}${C_RESET}"  
println "- CC_VERSION: ${C_GREEN}${CC_VERSION}${C_RESET}"  
println "- CC_SEQUENCE: ${C_GREEN}${CC_SEQUENCE}${C_RESET}"  
println "- CC_END_POLICY: ${C_GREEN}${CC_END_POLICY}${C_RESET}"  
println "- CC_COLL_CONFIG: ${C_GREEN}${CC_COLL_CONFIG}${C_RESET}"  
println "- CC_INIT_FCN: ${C_GREEN}${CC_INIT_FCN}${C_RESET}"  
println "- DELAY: ${C_GREEN}${DELAY}${C_RESET}"  
println "- MAX_RETRY: ${C_GREEN}${MAX_RETRY}${C_RESET}"  
println "- VERBOSE: ${C_GREEN}${VERBOSE}${C_RESET}"  
println "- NUM_ORGS: ${C_GREEN}${NUM_ORGS}${C_RESET}" # In ra số lượng org  
  
# --- Argument Parsing for Flags ---  
INIT_REQUIRED="--init-required"  
if [ "$CC_INIT_FCN" = "NA" ]; then  
  INIT_REQUIRED=""  
fi  
  
if [ "$CC_END_POLICY" = "NA" ]; then  
  CC_END_POLICY=""  
else  
  CC_END_POLICY="--signature-policy $CC_END_POLICY"  
fi  
  
if [ "$CC_COLL_CONFIG" = "NA" ]; then  
  CC_COLL_CONFIG=""  
else  
  CC_COLL_CONFIG="--collections-config $CC_COLL_CONFIG"  
fi  
  
FABRIC_CFG_PATH=$PWD/../config/  
  
# --- Import helpers ---  
. scripts/envVar.sh  
. scripts/ccutils.sh  
  
function checkPrereqs() {  
  jq --version > /dev/null 2>&1  
  
  if [[ $? -ne 0 ]]; then  
    errorln "jq command not found..."  
    errorln  
    errorln "Follow the instructions in the Fabric docs to install the prereqs"  
    errorln "https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html"  
    exit 1  
  fi  
}  
  
#check for prerequisites  
checkPrereqs  
  
# --- Main Deployment Logic ---  
  
## 1. Package the chaincode  
infoln "Packaging chaincode..."  
./scripts/packageCC.sh "$CC_NAME" "$CC_SRC_PATH" "$CC_SRC_LANGUAGE" "$CC_VERSION"  
  
infoln "Calculating new chaincode package ID..."  
export PACKAGE_ID=$(peer lifecycle chaincode calculatepackageid ${CC_NAME}.tar.gz)  
println "Package ID is: ${C_GREEN}${PACKAGE_ID}${C_RESET}"  
  
## 2. Install chaincode on all peers  
infoln "Installing chaincode on Org1 peers..."  
installChaincode 1 0  
installChaincode 1 1  
installChaincode 1 2  
  
infoln "Installing chaincode on Org2 peers..."  
installChaincode 2 0  
installChaincode 2 1  
installChaincode 2 2  
  
# Chỉ cài đặt cho Org3 nếu số lượng tổ chức là 3  
if [ "$NUM_ORGS" -ge 3 ]; then  
  infoln "Installing chaincode on Org3 peers..."  
  installChaincode 3 0  
  installChaincode 3 1  
  installChaincode 3 2  
fi  
  
resolveSequence  
  
## 3. Approve the definition for each organization  
infoln "Approving chaincode definition for Org1..."  
approveForMyOrg 1  
  
infoln "Approving chaincode definition for Org2..."  
approveForMyOrg 2  
  
# Chỉ approve cho Org3 nếu số lượng tổ chức là 3  
if [ "$NUM_ORGS" -ge 3 ]; then  
  infoln "Approving chaincode definition for Org3..."  
  approveForMyOrg 3  
fi  
  
  
## 4. Check commit readiness and Commit the chaincode definition  
if [ "$NUM_ORGS" -ge 3 ]; then  
  # Logic cho 3 tổ chức  
  infoln "Checking if chaincode definition is ready for 3 Orgs..."  
  checkCommitReadiness 1 "\"Org1MSP\": true" "\"Org2MSP\": true" "\"Org3MSP\": true"  
  checkCommitReadiness 2 "\"Org1MSP\": true" "\"Org2MSP\": true" "\"Org3MSP\": true"  
  checkCommitReadiness 3 "\"Org1MSP\": true" "\"Org2MSP\": true" "\"Org3MSP\": true"  
  
  infoln "Committing chaincode definition for 3 Orgs..."  
  commitChaincodeDefinition 1 2 3  
else  
  # Logic cho 2 tổ chức (mặc định)  
  infoln "Checking if chaincode definition is ready for 2 Orgs..."  
  checkCommitReadiness 1 "\"Org1MSP\": true" "\"Org2MSP\": true"  
  checkCommitReadiness 2 "\"Org1MSP\": true" "\"Org2MSP\": true"  
  
  infoln "Committing chaincode definition for 2 Orgs..."  
  commitChaincodeDefinition 1 2  
fi  
  
  
## 5. Query the committed chaincode definition on all orgs  
infoln "Querying committed definition on Org1..."  
queryCommitted 1  
infoln "Querying committed definition on Org2..."  
queryCommitted 2  
  
# Chỉ query cho Org3 nếu số lượng tổ chức là 3  
if [ "$NUM_ORGS" -ge 3 ]; then  
  infoln "Querying committed definition on Org3..."  
  queryCommitted 3  
fi  
  
## 6. Initialize the chaincode if required  
if [ "$CC_INIT_FCN" != "NA" ]; then  
  infoln "Initializing chaincode..."  
  if [ "$NUM_ORGS" -ge 3 ]; then  
    chaincodeInvokeInit 1 2 3  
  else  
    chaincodeInvokeInit 1 2  
  fi  
else  
  infoln "Chaincode initialization is not required."  
fi  
  
println "✅ Chaincode deployment successful."exit 0
```

---
# Triển khai Hyperledger transaction lifecycle manually 
Tôi muốn thực hiện tạo transaction trong mạng Fabric một cách manually bằng cách:
- Đầu tiên, client gọi chaincode cần thực thi, sau đó client sẽ nhận được transaction_id (đã làm được)
- Các peer sẽ thực hiện ký thủ công transaction đó (dịch vụ rest-api-peer) 
- Thực hiện các bước tiếp theo trong quy trình bằng cái gọi rest api tới peer...
Làm cách nào để viết một dịch vụ rest-api (golang) có thể kết nối đến một peer và thực hiện cái api như Endorse, SubmitToOrder...

```go
package transaction  
  
import (  
    "github.com/gofiber/fiber/v2"  
  
    "stask-api/api/serializers"    "stask-api/common/configure"    "stask-api/common/response"    respErr "stask-api/common/response/error"  
)  
  
var (  
    cfg = configure.GetConfig()  
)  
  
type Controller interface {  
    Endorse(ctx *fiber.Ctx) (err error)  
}  
  
type controller struct {  
    service serviceInterface  
}  
  
func New() Controller {  
    return &controller{  
       service: newService(),  
    }  
}  
  
func (c *controller) Endorse(ctx *fiber.Ctx) error {  
    var requestBody serializers.TransactionCreateBodyValidate  
    if err := ctx.BodyParser(&requestBody); err != nil {  
       return response.New(ctx, response.Options{Code: fiber.StatusBadRequest, Data: respErr.ErrFieldWrongType})  
    }  
    if err := requestBody.Validate(); err != nil {  
       return err  
    }  
  
    // init connection  
    cryptoPath := "../../test-network/organizations/peerOrganizations/org2.example.com"  
    orgConfig := OrgSetup{  
       OrgName:      "Org2",  
       MSPID:        "Org2MSP",  
       CertPath:     cryptoPath + "/users/User1@org2.example.com/msp/signcerts/User1@org2.example.com-cert.pem",  
       KeyPath:      cryptoPath + "/users/User1@org2.example.com/msp/keystore/",  
       TLSCertPath:  cryptoPath + "/peers/peer0.org2.example.com/tls/ca.crt",  
       PeerEndpoint: "dns:///localhost:8100",  
       GatewayPeer:  "peer0.org2.example.com",  
    }  
  
    orgSetup, err := Initialize(orgConfig)  
    if err != nil {  
       return err  
    }  
  
    network := orgSetup.Gateway.GetNetwork(cfg.ChannelId)  
    contract := network.GetContract(requestBody.ChaincodeName)  
  
    // TODO: get proposal from transaction id  
  
    // TODO: endorse proposal  
  
    return nil  
}
```

```go
package transaction  
  
import (  
    "crypto/x509"  
    "fmt"    "log"    "os"    "path"    "time"  
    "github.com/hyperledger/fabric-gateway/pkg/client"    "github.com/hyperledger/fabric-gateway/pkg/hash"    "github.com/hyperledger/fabric-gateway/pkg/identity"    "google.golang.org/grpc"    "google.golang.org/grpc/credentials")  
  
type serviceInterface interface {  
}  
  
type service struct{}  
  
func newService() serviceInterface {  
    return &service{}  
}  
  
type OrgSetup struct {  
    OrgName      string  
    MSPID        string  
    CryptoPath   string  
    CertPath     string  
    KeyPath      string  
    TLSCertPath  string  
    PeerEndpoint string  
    GatewayPeer  string  
    Gateway      client.Gateway  
}  
  
// Initialize the setup for the organization.func Initialize(setup OrgSetup) (*OrgSetup, error) {  
    log.Printf("Initializing connection for %s...\n", setup.OrgName)  
    clientConnection := setup.newGrpcConnection()  
    id := setup.newIdentity()  
    sign := setup.newSign()  
  
    gateway, err := client.Connect(  
       id,  
       client.WithSign(sign),  
       client.WithHash(hash.SHA256),  
       client.WithClientConnection(clientConnection),  
       client.WithEvaluateTimeout(5*time.Second),  
       client.WithEndorseTimeout(15*time.Second),  
       client.WithSubmitTimeout(5*time.Second),  
       client.WithCommitStatusTimeout(1*time.Minute),  
    )  
    if err != nil {  
       panic(err)  
    }  
    setup.Gateway = *gateway  
    log.Println("Initialization complete")  
    return &setup, nil  
}  
  
// newGrpcConnection creates a gRPC connection to the Gateway server.func (setup OrgSetup) newGrpcConnection() *grpc.ClientConn {  
    certificate, err := loadCertificate(setup.TLSCertPath)  
    if err != nil {  
       panic(err)  
    }  
  
    certPool := x509.NewCertPool()  
    certPool.AddCert(certificate)  
    transportCredentials := credentials.NewClientTLSFromCert(certPool, setup.GatewayPeer)  
  
    connection, err := grpc.NewClient(setup.PeerEndpoint, grpc.WithTransportCredentials(transportCredentials))  
    if err != nil {  
       panic(fmt.Errorf("failed to create gRPC connection: %w", err))  
    }  
  
    return connection  
}  
  
// newIdentity creates a client identity for this Gateway connection using an X.509 certificate.func (setup OrgSetup) newIdentity() *identity.X509Identity {  
    certificate, err := loadCertificate(setup.CertPath)  
    if err != nil {  
       panic(err)  
    }  
  
    id, err := identity.NewX509Identity(setup.MSPID, certificate)  
    if err != nil {  
       panic(err)  
    }  
  
    return id  
}  
  
// newSign creates a function that generates a digital signature from a message digest using a private key.func (setup OrgSetup) newSign() identity.Sign {  
    files, err := os.ReadDir(setup.KeyPath)  
    if err != nil {  
       panic(fmt.Errorf("failed to read private key directory: %w", err))  
    }  
    privateKeyPEM, err := os.ReadFile(path.Join(setup.KeyPath, files[0].Name()))  
  
    if err != nil {  
       panic(fmt.Errorf("failed to read private key file: %w", err))  
    }  
  
    privateKey, err := identity.PrivateKeyFromPEM(privateKeyPEM)  
    if err != nil {  
       panic(err)  
    }  
  
    sign, err := identity.NewPrivateKeySign(privateKey)  
    if err != nil {  
       panic(err)  
    }  
  
    return sign  
}  
  
func loadCertificate(filename string) (*x509.Certificate, error) {  
    certificatePEM, err := os.ReadFile(filename)  
    if err != nil {  
       return nil, fmt.Errorf("failed to read certificate file: %w", err)  
    }  
    return identity.CertificateFromPEM(certificatePEM)  
}
```
- Hãy giúp tôi xử lý logic lấy proposal từ transaction id 
- và có cách nào gọi endorse chỉ từ peer của một tổ chức (chỉ lấy chữ ký từ tổ chức 1) mà không phải gọi endorse để lây chữ ký từ tất cả các peers trong mạng không ?
---
# Vậy có thể sử dụng fabric-sdk-go để thực hiện endorse transaction từ vị trí một peer một cách thủ công không ? Hãy viết đoạn code cho kịch bản này

---
# Triển khai các dịch vụ để tạo và endorse, commit transaction thủ công: 1 dịch vụ client để tạo proposal
---
# Hãy vẽ giúp tôi hình để trình bày quy trình của một transaction trong Fabric
---
# Làm sao mà Hyperledger Fabric có thể phù hợp với use case quản lý danh tính (Identity Management)

Vẽ lại hình này bằng tiếng Việt cho tôi
---
# Ai sẽ commit chanincode ?

- Bước 4: Commit (Cam kết) định nghĩa lên channel
	- Sau khi có đủ số lượng phê duyệt, một tổ chức có thể commit để CC chính thức được đưa vào sử dụng.
	- Trường hợp các tổ chức đưa ra định nghĩa khác nhau (ví dụ khác sequence) thì bước commit sẽ thất bại.
> Q: Tại sao chỉ có một tổ chức commit định nghĩa ? Tổ chức nào trong kênh cũng commit được ? Tổ chức không có trong policy duyệt CC có commit được hông ?
---
# Kiểm tra cách peers validate endorsements ở bước cuối bằng cách bỏ qua bước verify endorsements ở client
Đây là transaction flow trong Fabric:

- Client send a proposal (1) --> Gateway send proposal for endorsement (2) --> Endorsing peers endorse transaction and send endorsements to client through Gateway (3) --> Client (or Gateway? idk) verify endorsements (4) --> Client send endorsements to Order through Gateway (5) --> Orderer orders endorsements and send to peers for validation (6) --> Peers validate endorsements and commit (7)

Q: 
- Sẽ ra sao nếu bỏ qua bước (4), tức sẽ bỏ qua bước kiểm tra endorsements và gửi thẳng đến Orderer và các peer sẽ validate ở bước cuối ?
- Làm sao để có thể tái hiện case này ? Mục đích để là để xem ở bước cuối các peers có validate lại endorsements không và validate bằng cách nào ?
---
# Convert application from fabric-gateway to fabric-sdk-go
Ứng dụng hiện tại đang sử thực thi chaincode bằng fabric-gateway, tôi muốn thêm route để có thể thực thi mà không thông qua Gateway

```go

vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1$ ls
go.mod  go.sum  main.go  README.md  web/
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1$ cat main.go 
package main

import (
        "fmt"

        "rest-api-go/web"
)

// org1
func main() {
        //Initialize setup for Org1
        cryptoPath := "../../test-network/organizations/peerOrganizations/org1.example.com"
        orgConfig := web.OrgSetup{
                OrgName:      "Org1",
                MSPID:        "Org1MSP",
                CertPath:     cryptoPath + "/users/User1@org1.example.com/msp/signcerts/User1@org1.example.com-cert.pem",
                KeyPath:      cryptoPath + "/users/User1@org1.example.com/msp/keystore/",
                TLSCertPath:  cryptoPath + "/peers/peer1.org1.example.com/tls/ca.crt",
                PeerEndpoint: "dns:///localhost:7051",
                GatewayPeer:  "peer0.org1.example.com",
        }

        orgSetup, err := web.Initialize(orgConfig)
        if err != nil {
                fmt.Println("Error initializing setup for Org1: ", err)
        }
        web.Serve(web.OrgSetup(*orgSetup))
}
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1$ ls
go.mod  go.sum  main.go  README.md  web/
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1$ z web
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ ls
app.go  initialize.go  invoke.go  query.go
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ cat app.go 
package web

import (
        "fmt"
        "net/http"

        "github.com/hyperledger/fabric-gateway/pkg/client"
        "github.com/hyperledger/fabric-sdk-go/pkg/fabsdk"
)

// OrgSetup contains organization's config to interact with the network.
type OrgSetup struct {
        OrgName      string
        MSPID        string
        CryptoPath   string
        CertPath     string
        KeyPath      string
        TLSCertPath  string
        PeerEndpoint string
        GatewayPeer  string
        Gateway      client.Gateway
        SDK          fabsdk.FabricSDK
}

// Serve starts http web server.
func Serve(setups OrgSetup) {
        http.HandleFunc("/query", setups.Query)
        http.HandleFunc("/invoke", setups.Invoke)
        fmt.Println("Listening (http://localhost:3000/)...")
        if err := http.ListenAndServe(":3000", nil); err != nil {
                fmt.Println(err)
        }
}
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ cat in
initialize.go  invoke.go      
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ cat initialize.go 
package web

import (
        "crypto/x509"
        "fmt"
        "log"
        "os"
        "path"
        "time"

        "github.com/hyperledger/fabric-gateway/pkg/client"
        "github.com/hyperledger/fabric-gateway/pkg/hash"
        "github.com/hyperledger/fabric-gateway/pkg/identity"
        "google.golang.org/grpc"
        "google.golang.org/grpc/credentials"
)

// Initialize the setup for the organization.
func Initialize(setup OrgSetup) (*OrgSetup, error) {
        log.Printf("Initializing connection for %s...\n", setup.OrgName)
        clientConnection := setup.newGrpcConnection()
        id := setup.newIdentity()
        sign := setup.newSign()

        gateway, err := client.Connect(
                id,
                client.WithSign(sign),
                client.WithHash(hash.SHA256),
                client.WithClientConnection(clientConnection),
                client.WithEvaluateTimeout(5*time.Second),
                client.WithEndorseTimeout(15*time.Second),
                client.WithSubmitTimeout(5*time.Second),
                client.WithCommitStatusTimeout(1*time.Minute),
        )
        if err != nil {
                panic(err)
        }
        setup.Gateway = *gateway
        log.Println("Initialization complete")
        return &setup, nil
}

// newGrpcConnection creates a gRPC connection to the Gateway server.
func (setup OrgSetup) newGrpcConnection() *grpc.ClientConn {
        certificate, err := loadCertificate(setup.TLSCertPath)
        if err != nil {
                panic(err)
        }

        certPool := x509.NewCertPool()
        certPool.AddCert(certificate)
        transportCredentials := credentials.NewClientTLSFromCert(certPool, setup.GatewayPeer)

        connection, err := grpc.NewClient(setup.PeerEndpoint, grpc.WithTransportCredentials(transportCredentials))
        if err != nil {
                panic(fmt.Errorf("failed to create gRPC connection: %w", err))
        }

        return connection
}

// newIdentity creates a client identity for this Gateway connection using an X.509 certificate.
func (setup OrgSetup) newIdentity() *identity.X509Identity {
        certificate, err := loadCertificate(setup.CertPath)
        if err != nil {
                panic(err)
        }

        id, err := identity.NewX509Identity(setup.MSPID, certificate)
        if err != nil {
                panic(err)
        }

        return id
}

// newSign creates a function that generates a digital signature from a message digest using a private key.
func (setup OrgSetup) newSign() identity.Sign {
        files, err := os.ReadDir(setup.KeyPath)
        if err != nil {
                panic(fmt.Errorf("failed to read private key directory: %w", err))
        }
        privateKeyPEM, err := os.ReadFile(path.Join(setup.KeyPath, files[0].Name()))

        if err != nil {
                panic(fmt.Errorf("failed to read private key file: %w", err))
        }

        privateKey, err := identity.PrivateKeyFromPEM(privateKeyPEM)
        if err != nil {
                panic(err)
        }

        sign, err := identity.NewPrivateKeySign(privateKey)
        if err != nil {
                panic(err)
        }

        return sign
}

func loadCertificate(filename string) (*x509.Certificate, error) {
        certificatePEM, err := os.ReadFile(filename)
        if err != nil {
                return nil, fmt.Errorf("failed to read certificate file: %w", err)
        }
        return identity.CertificateFromPEM(certificatePEM)
}
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ ls
app.go  initialize.go  invoke.go  query.go
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ cat invoke.go 
package web

import (
    "fmt"
    "net/http"

    "github.com/hyperledger/fabric-gateway/pkg/client"
)

// Invoke handles chaincode invoke requests.
func (setup *OrgSetup) Invoke(w http.ResponseWriter, r *http.Request) {
    fmt.Println("Received Invoke request")
    if err := r.ParseForm(); err != nil {
        fmt.Fprintf(w, "ParseForm() err: %s", err)
        return
    }
    chainCodeName := r.FormValue("chaincodeid")
    channelID := r.FormValue("channelid")
    function := r.FormValue("function")
    args := r.Form["args"]
    fmt.Printf("channel: %s, chaincode: %s, function: %s, args: %s\n", channelID, chainCodeName, function, args)
    network := setup.Gateway.GetNetwork(channelID)
    contract := network.GetContract(chainCodeName)
    txn_proposal, err := contract.NewProposal(function, client.WithArguments(args...))
    if err != nil {
        fmt.Fprintf(w, "Error creating txn proposal: %s", err)
        return
    }
    txn_endorsed, err := txn_proposal.Endorse()
    // if endorsements not match, it return err here, and the txn_endorsed = nil, how to config to make it ignore errors and return the txn endorsed ?
    if err != nil {
        fmt.Fprintf(w, "Error endorsing txn: %s", err)
        return
    }
    txn_committed, err := txn_endorsed.Submit()
    if err != nil {
        fmt.Fprintf(w, "Error submitting transaction: %s", err)
        return
    }
    fmt.Fprintf(w, "Transaction ID : %s Response: %s", txn_committed.TransactionID(), txn_endorsed.Result())
}
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ ls
app.go  initialize.go  invoke.go  query.go
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ cat query.go 
package web

import (
        "fmt"
        "net/http"
)

// Query handles chaincode query requests.
func (setup OrgSetup) Query(w http.ResponseWriter, r *http.Request) {
        fmt.Println("Received Query request")
        queryParams := r.URL.Query()
        chainCodeName := queryParams.Get("chaincodeid")
        channelID := queryParams.Get("channelid")
        function := queryParams.Get("function")
        args := r.URL.Query()["args"]
        fmt.Printf("channel: %s, chaincode: %s, function: %s, args: %s\n", channelID, chainCodeName, function, args)
        network := setup.Gateway.GetNetwork(channelID)
        contract := network.GetContract(chainCodeName)
        evaluateResponse, err := contract.EvaluateTransaction(function, args...)
        if err != nil {
                fmt.Fprintf(w, "Error: %s", err)
                return
        }
        w.Header().Set("Content-Type", "application/json")
        w.Write(evaluateResponse)
        //fmt.Fprintf(w, "Response: %s", evaluateResponse)
}
vessel@vessel-pc:~/src/fabric-demo/asset-transfer-basic/rest-api-go-org1/web$ 

```

---
# Fix SDK config
The problem might because I deploy with 2 orgs and 3 peers each, and this config.yaml is just for org1 ? 

compose.yaml: 
```
# Copyright IBM Corp. All Rights Reserved.  
#  
# SPDX-License-Identifier: Apache-2.0  
#  
  
volumes:  
  orderer.example.com:  
  peer0.org1.example.com:  
  peer1.org1.example.com:  
  peer2.org1.example.com:  
  peer0.org2.example.com:  
  peer1.org2.example.com:  
  peer2.org2.example.com:  
  
networks:  
  test:  
    name: fabric_test  
  
services:  
  
  orderer.example.com:  
    container_name: orderer.example.com  
    image: hyperledger/fabric-orderer:2.5.13  
    labels:  
      service: hyperledger-fabric  
    environment:  
      - FABRIC_LOGGING_SPEC=INFO  
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0  
      - ORDERER_GENERAL_LISTENPORT=7050  
      - ORDERER_GENERAL_LOCALMSPID=OrdererMSP  
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp  
      # enabled TLS  
      - ORDERER_GENERAL_TLS_ENABLED=true  
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key  
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt  
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]  
      - ORDERER_GENERAL_CLUSTER_CLIENTCERTIFICATE=/var/hyperledger/orderer/tls/server.crt  
      - ORDERER_GENERAL_CLUSTER_CLIENTPRIVATEKEY=/var/hyperledger/orderer/tls/server.key  
      - ORDERER_GENERAL_CLUSTER_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]  
      - ORDERER_GENERAL_BOOTSTRAPMETHOD=none  
      - ORDERER_CHANNELPARTICIPATION_ENABLED=true  
      - ORDERER_ADMIN_TLS_ENABLED=true  
      - ORDERER_ADMIN_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt  
      - ORDERER_ADMIN_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key  
      - ORDERER_ADMIN_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]  
      - ORDERER_ADMIN_TLS_CLIENTROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]  
      - ORDERER_ADMIN_LISTENADDRESS=0.0.0.0:7053  
      - ORDERER_OPERATIONS_LISTENADDRESS=0.0.0.0:9443  
      - ORDERER_METRICS_PROVIDER=prometheus  
    working_dir: /root  
    command: orderer  
    volumes:  
      - ../organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp:/var/hyperledger/orderer/msp  
      - ../organizations/ordererOrganizations/example.com/orderers/orderer.example.com/tls/:/var/hyperledger/orderer/tls  
      - orderer.example.com:/var/hyperledger/production/orderer  
    ports:  
      - 7050:7050  
      - 7053:7053  
      - 9443:9443  
    networks:  
      - test  
  
  peer0.org1.example.com:  
    container_name: peer0.org1.example.com  
    image: hyperledger/fabric-peer:2.5.13  
    labels:  
      service: hyperledger-fabric  
    environment:  
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg  
      - FABRIC_LOGGING_SPEC=INFO  
      - CORE_PEER_TLS_ENABLED=true  
      - CORE_PEER_PROFILE_ENABLED=false  
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt  
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key  
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt  
      # Peer specific variables  
      - CORE_PEER_ID=peer0.org1.example.com  
      - CORE_PEER_ADDRESS=peer0.org1.example.com:7051  
      - CORE_PEER_LISTENADDRESS=0.0.0.0:7051  
      - CORE_PEER_CHAINCODEADDRESS=peer0.org1.example.com:7052  
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:7052  
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org1.example.com:7051  
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org1.example.com:7051  
      - CORE_PEER_LOCALMSPID=Org1MSP  
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp  
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:9444  
      - CORE_METRICS_PROVIDER=prometheus  
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer0org1"}  
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s  
    volumes:  
      - ../organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com:/etc/hyperledger/fabric  
      - peer0.org1.example.com:/var/hyperledger/production  
    working_dir: /root  
    command: peer node start  
    ports:  
      - 7051:7051  
      - 9444:9444  
    networks:  
      - test  
  
  peer1.org1.example.com:  
    container_name: peer1.org1.example.com  
    image: hyperledger/fabric-peer:2.5.13  
    labels:  
      service: hyperledger-fabric  
    environment:  
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg  
      - FABRIC_LOGGING_SPEC=INFO  
      - CORE_PEER_TLS_ENABLED=true  
      - CORE_PEER_PROFILE_ENABLED=false  
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt  
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key  
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt  
      # Peer specific variables  
      - CORE_PEER_ID=peer1.org1.example.com  
      - CORE_PEER_ADDRESS=peer1.org1.example.com:9051  
      - CORE_PEER_LISTENADDRESS=0.0.0.0:9051  
      - CORE_PEER_CHAINCODEADDRESS=peer1.org1.example.com:9052  
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:9052  
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.org1.example.com:9051  
      # Point to the bootstrap peer of org1  
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org1.example.com:7051  
      - CORE_PEER_LOCALMSPID=Org1MSP  
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp  
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:9445  
      - CORE_METRICS_PROVIDER=prometheus  
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer1org1"}  
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s  
    volumes:  
      - ../organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com:/etc/hyperledger/fabric  
      - peer1.org1.example.com:/var/hyperledger/production  
    working_dir: /root  
    command: peer node start  
    ports:  
      - 9051:9051  
      - 9445:9445  
    networks:  
      - test  
  
  peer2.org1.example.com:  
    container_name: peer2.org1.example.com  
    image: hyperledger/fabric-peer:2.5.13  
    labels:  
      service: hyperledger-fabric  
    environment:  
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg  
      - FABRIC_LOGGING_SPEC=INFO  
      - CORE_PEER_TLS_ENABLED=true  
      - CORE_PEER_PROFILE_ENABLED=false  
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt  
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key  
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt  
      # Peer specific variables  
      - CORE_PEER_ID=peer2.org1.example.com  
      - CORE_PEER_ADDRESS=peer2.org1.example.com:9151  
      - CORE_PEER_LISTENADDRESS=0.0.0.0:9151  
      - CORE_PEER_CHAINCODEADDRESS=peer2.org1.example.com:9152  
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:9152  
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer2.org1.example.com:9151  
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org1.example.com:7051  
      - CORE_PEER_LOCALMSPID=Org1MSP  
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp  
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:9545  
      - CORE_METRICS_PROVIDER=prometheus  
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer2org1"}  
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s  
    volumes:  
      - ../organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com:/etc/hyperledger/fabric  
      - peer2.org1.example.com:/var/hyperledger/production  
    working_dir: /root  
    command: peer node start  
    ports:  
      - 9151:9151  
      - 9545:9545  
    networks:  
      - test  
  
  peer0.org2.example.com:  
    container_name: peer0.org2.example.com  
    image: hyperledger/fabric-peer:2.5.13  
    labels:  
      service: hyperledger-fabric  
    environment:  
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg  
      - FABRIC_LOGGING_SPEC=INFO  
      - CORE_PEER_TLS_ENABLED=true  
      - CORE_PEER_PROFILE_ENABLED=false  
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt  
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key  
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt  
      # Peer specific variables  
      - CORE_PEER_ID=peer0.org2.example.com  
      - CORE_PEER_ADDRESS=peer0.org2.example.com:8100  
      - CORE_PEER_LISTENADDRESS=0.0.0.0:8100  
      - CORE_PEER_CHAINCODEADDRESS=peer0.org2.example.com:8101  
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:8101  
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.org2.example.com:8100  
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org2.example.com:8100  
      - CORE_PEER_LOCALMSPID=Org2MSP  
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp  
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:8105  
      - CORE_METRICS_PROVIDER=prometheus  
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer0org2"}  
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s  
    volumes:  
      - ../organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com:/etc/hyperledger/fabric  
      - peer0.org2.example.com:/var/hyperledger/production  
    working_dir: /root  
    command: peer node start  
    ports:  
      - 8100:8100  
      - 8105:8105  
    networks:  
      - test  
  
  peer1.org2.example.com:  
    container_name: peer1.org2.example.com  
    image: hyperledger/fabric-peer:2.5.13  
    labels:  
      service: hyperledger-fabric  
    environment:  
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg  
      - FABRIC_LOGGING_SPEC=INFO  
      - CORE_PEER_TLS_ENABLED=true  
      - CORE_PEER_PROFILE_ENABLED=false  
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt  
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key  
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt  
      # Peer specific variables  
      - CORE_PEER_ID=peer1.org2.example.com  
      - CORE_PEER_ADDRESS=peer1.org2.example.com:8120  
      - CORE_PEER_LISTENADDRESS=0.0.0.0:8120  
      - CORE_PEER_CHAINCODEADDRESS=peer1.org2.example.com:8121  
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:8121  
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.org2.example.com:8120  
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org2.example.com:8100  
      - CORE_PEER_LOCALMSPID=Org2MSP  
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp  
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:8125  
      - CORE_METRICS_PROVIDER=prometheus  
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer1org2"}  
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s  
    volumes:  
      - ../organizations/peerOrganizations/org2.example.com/peers/peer1.org2.example.com:/etc/hyperledger/fabric  
      - peer1.org2.example.com:/var/hyperledger/production  
    working_dir: /root  
    command: peer node start  
    ports:  
      - 8120:8120  
      - 8125:8125  
    networks:  
      - test  
  
  peer2.org2.example.com:  
    container_name: peer2.org2.example.com  
    image: hyperledger/fabric-peer:2.5.13  
    labels:  
      service: hyperledger-fabric  
    environment:  
      - FABRIC_CFG_PATH=/etc/hyperledger/peercfg  
      - FABRIC_LOGGING_SPEC=INFO  
      - CORE_PEER_TLS_ENABLED=true  
      - CORE_PEER_PROFILE_ENABLED=false  
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt  
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key  
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt  
      # Peer specific variables  
      - CORE_PEER_ID=peer2.org2.example.com  
      - CORE_PEER_ADDRESS=peer2.org2.example.com:8140  
      - CORE_PEER_LISTENADDRESS=0.0.0.0:8140  
      - CORE_PEER_CHAINCODEADDRESS=peer2.org2.example.com:8141  
      - CORE_PEER_CHAINCODELISTENADDRESS=0.0.0.0:8141  
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer2.org2.example.com:8140  
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.org2.example.com:8100  
      - CORE_PEER_LOCALMSPID=Org2MSP  
      - CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/fabric/msp  
      - CORE_OPERATIONS_LISTENADDRESS=0.0.0.0:8145  
      - CORE_METRICS_PROVIDER=prometheus  
      - CHAINCODE_AS_A_SERVICE_BUILDER_CONFIG={"peername":"peer2org2"}  
      - CORE_CHAINCODE_EXECUTETIMEOUT=300s  
    volumes:  
      - ../organizations/peerOrganizations/org2.example.com/peers/peer2.org2.example.com:/etc/hyperledger/fabric  
      - peer2.org2.example.com:/var/hyperledger/production  
    working_dir: /root  
    command: peer node start  
    ports:  
      - 8140:8140  
      - 8145:8145  
    networks:  
      - test
```

---
# Deploy Fabric với CA
Giải thích quá trình deploy với CA này:
```

vessel@vessel-pc:~/src/fabric-demo/test-network$ ./network.sh up -s couchdb -ca
Using docker and docker compose
Starting nodes with CLI timeout of '5' tries and CLI delay of '3' seconds and using database 'couchdb' with crypto from 'Certificate Authorities'
LOCAL_VERSION=v2.5.12
DOCKER_IMAGE_VERSION=v2.5.12
CA_LOCAL_VERSION=v1.5.15
CA_DOCKER_IMAGE_VERSION=v1.5.15
Generating certificates using Fabric CA
WARN[0000] /home/vessel/src/fabric-demo/test-network/compose/compose-ca.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
WARN[0000] /home/vessel/src/fabric-demo/test-network/compose/docker/docker-compose-ca.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 4/4
 ✔ Network fabric_test   Created                                                                                                                                                                              0.1s
 ✔ Container ca_orderer  Started                                                                                                                                                                              0.4s
 ✔ Container ca_org1     Started                                                                                                                                                                              0.3s
 ✔ Container ca_org2     Started                                                                                                                                                                              0.4s
+ fabric-ca-client getcainfo -u https://admin:adminpw@localhost:7054 --caname ca-org1 --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:14 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:14 [INFO] TLS Enabled
2025/08/07 17:29:14 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/msp/cacerts/localhost-7054-ca-org1.pem
2025/08/07 17:29:14 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/msp/IssuerPublicKey
2025/08/07 17:29:14 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/msp/IssuerRevocationPublicKey
+ res=0
Creating Org1 Identities
Enrolling the CA admin
+ fabric-ca-client enroll -u https://admin:adminpw@localhost:7054 --caname ca-org1 --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:14 [INFO] Created a default configuration file at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:14 [INFO] TLS Enabled
2025/08/07 17:29:14 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:14 [INFO] encoded CSR
2025/08/07 17:29:14 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:14 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/msp/cacerts/localhost-7054-ca-org1.pem
2025/08/07 17:29:14 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/msp/IssuerPublicKey
2025/08/07 17:29:14 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/msp/IssuerRevocationPublicKey
Registering peer0
+ fabric-ca-client register --caname ca-org1 --id.name peer0 --id.secret peer0pw --id.type peer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:14 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:14 [INFO] TLS Enabled
2025/08/07 17:29:14 [INFO] TLS Enabled
Password: peer0pw
Registering peer1
+ fabric-ca-client register --caname ca-org1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:14 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:14 [INFO] TLS Enabled
2025/08/07 17:29:14 [INFO] TLS Enabled
Password: peer1pw
Registering peer2
+ fabric-ca-client register --caname ca-org1 --id.name peer2 --id.secret peer2pw --id.type peer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] TLS Enabled
Password: peer2pw
Registering user
+ fabric-ca-client register --caname ca-org1 --id.name user1 --id.secret user1pw --id.type client --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] TLS Enabled
Password: user1pw
Registering the org admin
+ fabric-ca-client register --caname ca-org1 --id.name org1admin --id.secret org1adminpw --id.type admin --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] TLS Enabled
Password: org1adminpw
Generating the peer0 msp
+ fabric-ca-client enroll -u https://peer0:peer0pw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:15 [INFO] encoded CSR
2025/08/07 17:29:15 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:15 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/cacerts/localhost-7054-ca-org1.pem
2025/08/07 17:29:15 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/IssuerPublicKey
2025/08/07 17:29:15 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/msp/IssuerRevocationPublicKey
Generating the peer1 msp
+ fabric-ca-client enroll -u https://peer1:peer1pw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:15 [INFO] encoded CSR
2025/08/07 17:29:15 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:15 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/cacerts/localhost-7054-ca-org1.pem
2025/08/07 17:29:15 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/IssuerPublicKey
2025/08/07 17:29:15 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/msp/IssuerRevocationPublicKey
Generating the peer2 msp
+ fabric-ca-client enroll -u https://peer2:peer2pw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:15 [INFO] encoded CSR
2025/08/07 17:29:15 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:15 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/cacerts/localhost-7054-ca-org1.pem
2025/08/07 17:29:15 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/IssuerPublicKey
2025/08/07 17:29:15 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/msp/IssuerRevocationPublicKey
Generating the peer0-tls certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://peer0:peer0pw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls --enrollment.profile tls --csr.hosts peer0.org1.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:15 [INFO] encoded CSR
2025/08/07 17:29:15 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:15 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/tlscacerts/tls-localhost-7054-ca-org1.pem
2025/08/07 17:29:15 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/IssuerPublicKey
2025/08/07 17:29:15 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/IssuerRevocationPublicKey
Generating the peer1-tls certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://peer1:peer1pw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls --enrollment.profile tls --csr.hosts peer1.org1.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:15 [INFO] encoded CSR
2025/08/07 17:29:15 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:15 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/tlscacerts/tls-localhost-7054-ca-org1.pem
2025/08/07 17:29:15 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/IssuerPublicKey
2025/08/07 17:29:15 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/IssuerRevocationPublicKey
Generating the peer2-tls certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://peer2:peer2pw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/tls --enrollment.profile tls --csr.hosts peer2.org1.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:15 [INFO] TLS Enabled
2025/08/07 17:29:15 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:15 [INFO] encoded CSR
2025/08/07 17:29:15 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:15 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/tls/tlscacerts/tls-localhost-7054-ca-org1.pem
2025/08/07 17:29:15 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/tls/IssuerPublicKey
2025/08/07 17:29:15 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/peers/peer2.org1.example.com/tls/IssuerRevocationPublicKey
Generating the user msp
+ fabric-ca-client enroll -u https://user1:user1pw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:16 [INFO] encoded CSR
2025/08/07 17:29:16 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:16 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/cacerts/localhost-7054-ca-org1.pem
2025/08/07 17:29:16 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/IssuerPublicKey
2025/08/07 17:29:16 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/IssuerRevocationPublicKey
Generating the org admin msp
+ fabric-ca-client enroll -u https://org1admin:org1adminpw@localhost:7054 --caname ca-org1 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org1/ca-cert.pem
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:16 [INFO] encoded CSR
2025/08/07 17:29:16 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:16 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/cacerts/localhost-7054-ca-org1.pem
2025/08/07 17:29:16 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/IssuerPublicKey
2025/08/07 17:29:16 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp/IssuerRevocationPublicKey
Creating Org2 Identities
Enrolling the CA admin
+ fabric-ca-client enroll -u https://admin:adminpw@localhost:8054 --caname ca-org2 --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:16 [INFO] Created a default configuration file at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:16 [INFO] encoded CSR
2025/08/07 17:29:16 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:16 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/msp/cacerts/localhost-8054-ca-org2.pem
2025/08/07 17:29:16 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/msp/IssuerPublicKey
2025/08/07 17:29:16 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/msp/IssuerRevocationPublicKey
Registering peer0
+ fabric-ca-client register --caname ca-org2 --id.name peer0 --id.secret peer0pw --id.type peer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:16 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] TLS Enabled
Password: peer0pw
Registering user
+ fabric-ca-client register --caname ca-org2 --id.name user1 --id.secret user1pw --id.type client --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:16 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] TLS Enabled
Password: user1pw
Registering the org admin
+ fabric-ca-client register --caname ca-org2 --id.name org2admin --id.secret org2adminpw --id.type admin --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:16 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] TLS Enabled
Password: org2adminpw
Generating the peer0 msp
+ fabric-ca-client enroll -u https://peer0:peer0pw@localhost:8054 --caname ca-org2 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:16 [INFO] encoded CSR
2025/08/07 17:29:16 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:16 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/cacerts/localhost-8054-ca-org2.pem
2025/08/07 17:29:16 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/IssuerPublicKey
2025/08/07 17:29:16 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/msp/IssuerRevocationPublicKey
Generating the peer0-tls certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://peer0:peer0pw@localhost:8054 --caname ca-org2 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls --enrollment.profile tls --csr.hosts peer0.org2.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:16 [INFO] encoded CSR
2025/08/07 17:29:16 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:16 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/tlscacerts/tls-localhost-8054-ca-org2.pem
2025/08/07 17:29:16 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/IssuerPublicKey
2025/08/07 17:29:16 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/IssuerRevocationPublicKey
Generating the user msp
+ fabric-ca-client enroll -u https://user1:user1pw@localhost:8054 --caname ca-org2 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/User1@org2.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:16 [INFO] TLS Enabled
2025/08/07 17:29:16 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:16 [INFO] encoded CSR
2025/08/07 17:29:17 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:17 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/cacerts/localhost-8054-ca-org2.pem
2025/08/07 17:29:17 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/IssuerPublicKey
2025/08/07 17:29:17 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/User1@org2.example.com/msp/IssuerRevocationPublicKey
Generating the org admin msp
+ fabric-ca-client enroll -u https://org2admin:org2adminpw@localhost:8054 --caname ca-org2 -M /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/org2/ca-cert.pem
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:17 [INFO] encoded CSR
2025/08/07 17:29:17 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:17 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/cacerts/localhost-8054-ca-org2.pem
2025/08/07 17:29:17 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/IssuerPublicKey
2025/08/07 17:29:17 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp/IssuerRevocationPublicKey
Creating Orderer Org Identities
Enrolling the CA admin
+ fabric-ca-client enroll -u https://admin:adminpw@localhost:9054 --caname ca-orderer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:17 [INFO] Created a default configuration file at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:17 [INFO] encoded CSR
2025/08/07 17:29:17 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/msp/signcerts/cert.pem
2025/08/07 17:29:17 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/msp/cacerts/localhost-9054-ca-orderer.pem
2025/08/07 17:29:17 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/msp/IssuerPublicKey
2025/08/07 17:29:17 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/msp/IssuerRevocationPublicKey
Registering orderer
+ fabric-ca-client register --caname ca-orderer --id.name orderer --id.secret ordererpw --id.type orderer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:17 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] TLS Enabled
Password: ordererpw
Generating the orderer MSP
+ fabric-ca-client enroll -u https://orderer:ordererpw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:17 [INFO] encoded CSR
2025/08/07 17:29:17 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:17 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/cacerts/localhost-9054-ca-orderer.pem
2025/08/07 17:29:17 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/IssuerPublicKey
2025/08/07 17:29:17 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/IssuerRevocationPublicKey
Generating the orderer TLS certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://orderer:ordererpw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/tls --enrollment.profile tls --csr.hosts orderer.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:17 [INFO] encoded CSR
2025/08/07 17:29:17 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:17 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/tls/tlscacerts/tls-localhost-9054-ca-orderer.pem
2025/08/07 17:29:17 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/tls/IssuerPublicKey
2025/08/07 17:29:17 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/tls/IssuerRevocationPublicKey
Registering orderer2
+ fabric-ca-client register --caname ca-orderer --id.name orderer2 --id.secret orderer2pw --id.type orderer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:17 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] TLS Enabled
Password: orderer2pw
Generating the orderer2 MSP
+ fabric-ca-client enroll -u https://orderer2:orderer2pw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:17 [INFO] encoded CSR
2025/08/07 17:29:17 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:17 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/msp/cacerts/localhost-9054-ca-orderer.pem
2025/08/07 17:29:17 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/msp/IssuerPublicKey
2025/08/07 17:29:17 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/msp/IssuerRevocationPublicKey
Generating the orderer2 TLS certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://orderer2:orderer2pw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/tls --enrollment.profile tls --csr.hosts orderer2.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:17 [INFO] TLS Enabled
2025/08/07 17:29:17 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:17 [INFO] encoded CSR
2025/08/07 17:29:17 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:17 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/tlscacerts/tls-localhost-9054-ca-orderer.pem
2025/08/07 17:29:17 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/IssuerPublicKey
2025/08/07 17:29:17 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer2.example.com/tls/IssuerRevocationPublicKey
Registering orderer3
+ fabric-ca-client register --caname ca-orderer --id.name orderer3 --id.secret orderer3pw --id.type orderer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] TLS Enabled
Password: orderer3pw
Generating the orderer3 MSP
+ fabric-ca-client enroll -u https://orderer3:orderer3pw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:18 [INFO] encoded CSR
2025/08/07 17:29:18 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:18 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/msp/cacerts/localhost-9054-ca-orderer.pem
2025/08/07 17:29:18 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/msp/IssuerPublicKey
2025/08/07 17:29:18 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/msp/IssuerRevocationPublicKey
Generating the orderer3 TLS certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://orderer3:orderer3pw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/tls --enrollment.profile tls --csr.hosts orderer3.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:18 [INFO] encoded CSR
2025/08/07 17:29:18 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:18 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/tlscacerts/tls-localhost-9054-ca-orderer.pem
2025/08/07 17:29:18 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/IssuerPublicKey
2025/08/07 17:29:18 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer3.example.com/tls/IssuerRevocationPublicKey
Registering orderer4
+ fabric-ca-client register --caname ca-orderer --id.name orderer4 --id.secret orderer4pw --id.type orderer --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] TLS Enabled
Password: orderer4pw
Generating the orderer4 MSP
+ fabric-ca-client enroll -u https://orderer4:orderer4pw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:18 [INFO] encoded CSR
2025/08/07 17:29:18 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/msp/signcerts/cert.pem
2025/08/07 17:29:18 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/msp/cacerts/localhost-9054-ca-orderer.pem
2025/08/07 17:29:18 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/msp/IssuerPublicKey
2025/08/07 17:29:18 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/msp/IssuerRevocationPublicKey
Generating the orderer4 TLS certificates, use --csr.hosts to specify Subject Alternative Names
+ fabric-ca-client enroll -u https://orderer4:orderer4pw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/tls --enrollment.profile tls --csr.hosts orderer4.example.com --csr.hosts localhost --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:18 [INFO] encoded CSR
2025/08/07 17:29:18 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/signcerts/cert.pem
2025/08/07 17:29:18 [INFO] Stored TLS root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/tlscacerts/tls-localhost-9054-ca-orderer.pem
2025/08/07 17:29:18 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/IssuerPublicKey
2025/08/07 17:29:18 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/orderers/orderer4.example.com/tls/IssuerRevocationPublicKey
Registering the orderer admin
+ fabric-ca-client register --caname ca-orderer --id.name ordererAdmin --id.secret ordererAdminpw --id.type admin --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] Configuration file location: /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/fabric-ca-client-config.yaml
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] TLS Enabled
Password: ordererAdminpw
Generating the admin msp
+ fabric-ca-client enroll -u https://ordererAdmin:ordererAdminpw@localhost:9054 --caname ca-orderer -M /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/users/Admin@example.com/msp --tls.certfiles /home/vessel/src/fabric-demo/test-network/organizations/fabric-ca/ordererOrg/ca-cert.pem
2025/08/07 17:29:18 [INFO] TLS Enabled
2025/08/07 17:29:18 [INFO] generating key: &{A:ecdsa S:256}
2025/08/07 17:29:18 [INFO] encoded CSR
2025/08/07 17:29:18 [INFO] Stored client certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/users/Admin@example.com/msp/signcerts/cert.pem
2025/08/07 17:29:18 [INFO] Stored root CA certificate at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/users/Admin@example.com/msp/cacerts/localhost-9054-ca-orderer.pem
2025/08/07 17:29:18 [INFO] Stored Issuer public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/users/Admin@example.com/msp/IssuerPublicKey
2025/08/07 17:29:18 [INFO] Stored Issuer revocation public key at /home/vessel/src/fabric-demo/test-network/organizations/ordererOrganizations/example.com/users/Admin@example.com/msp/IssuerRevocationPublicKey
Generating CCP files for Org1 and Org2
WARN[0000] /home/vessel/src/fabric-demo/test-network/compose/docker/docker-compose-2-org.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
WARN[0000] /home/vessel/src/fabric-demo/test-network/compose/compose-couch.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
WARN[0000] /home/vessel/src/fabric-demo/test-network/compose/docker/docker-compose-couch.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
WARN[0000] Found orphan containers ([ca_org2 ca_org1 ca_orderer]) for this project. If you removed or renamed this service in your compose file, you can run this command with the --remove-orphans flag to clean it up.
[+] Running 20/20
 ✔ Volume "compose_peer2.org2.example.com"  Created                                                                                                                                                           0.0s
 ✔ Volume "compose_peer1.org1.example.com"  Created                                                                                                                                                           0.0s
 ✔ Volume "compose_orderer.example.com"     Created                                                                                                                                                           0.0s
 ✔ Volume "compose_peer0.org1.example.com"  Created                                                                                                                                                           0.0s
 ✔ Volume "compose_peer0.org2.example.com"  Created                                                                                                                                                           0.0s
 ✔ Volume "compose_peer2.org1.example.com"  Created                                                                                                                                                           0.0s
 ✔ Volume "compose_peer1.org2.example.com"  Created                                                                                                                                                           0.0s
 ✔ Container couchdb2_1                     Started                                                                                                                                                           0.8s
 ✔ Container couchdb1_1                     Started                                                                                                                                                           1.0s
 ✔ Container couchdb2_2                     Started                                                                                                                                                           0.7s
 ✔ Container couchdb1_0                     Started                                                                                                                                                           0.6s
 ✔ Container orderer.example.com            Started                                                                                                                                                           0.9s
 ✔ Container couchdb1_2                     Started                                                                                                                                                           0.6s
 ✔ Container couchdb2_0                     Started                                                                                                                                                           0.7s
 ✔ Container peer0.org1.example.com         Started                                                                                                                                                           1.1s
 ✔ Container peer0.org2.example.com         Started                                                                                                                                                           1.3s
 ✔ Container peer2.org2.example.com         Started                                                                                                                                                           1.2s
 ✔ Container peer2.org1.example.com         Started                                                                                                                                                           1.0s
 ✔ Container peer1.org2.example.com         Started                                                                                                                                                           1.4s
 ✔ Container peer1.org1.example.com         Started                                                                                                                                                           1.5s
CONTAINER ID   IMAGE                               COMMAND                  CREATED         STATUS                      PORTS                                                                                                                                   NAMES
444169f4c956   hyperledger/fabric-peer:2.5.13      "peer node start"        1 second ago    Up Less than a second       0.0.0.0:9051->9051/tcp, [::]:9051->9051/tcp, 7051/tcp, 0.0.0.0:9445->9445/tcp, [::]:9445->9445/tcp                                      peer1.org1.example.com
3d5642355faf   hyperledger/fabric-peer:2.5.13      "peer node start"        1 second ago    Up 1 second                 0.0.0.0:9151->9151/tcp, [::]:9151->9151/tcp, 7051/tcp, 0.0.0.0:9545->9545/tcp, [::]:9545->9545/tcp                                      peer2.org1.example.com
e8eaf4fb6c1a   hyperledger/fabric-peer:2.5.13      "peer node start"        1 second ago    Up Less than a second       0.0.0.0:8120->8120/tcp, [::]:8120->8120/tcp, 7051/tcp, 0.0.0.0:8125->8125/tcp, [::]:8125->8125/tcp                                      peer1.org2.example.com
d774bc68cc15   hyperledger/fabric-peer:2.5.13      "peer node start"        1 second ago    Up Less than a second       0.0.0.0:8140->8140/tcp, [::]:8140->8140/tcp, 7051/tcp, 0.0.0.0:8145->8145/tcp, [::]:8145->8145/tcp                                      peer2.org2.example.com
56998295cea8   hyperledger/fabric-peer:2.5.13      "peer node start"        1 second ago    Up Less than a second       0.0.0.0:8100->8100/tcp, [::]:8100->8100/tcp, 7051/tcp, 0.0.0.0:8105->8105/tcp, [::]:8105->8105/tcp                                      peer0.org2.example.com
5716539e3cd3   hyperledger/fabric-peer:2.5.13      "peer node start"        1 second ago    Up Less than a second       0.0.0.0:7051->7051/tcp, [::]:7051->7051/tcp, 0.0.0.0:9444->9444/tcp, [::]:9444->9444/tcp                                                peer0.org1.example.com
f25b3a41ae27   couchdb:3.4.2                       "tini -- /docker-ent…"   1 second ago    Up 1 second                 4369/tcp, 9100/tcp, 0.0.0.0:7984->5984/tcp, [::]:7984->5984/tcp                                                                         couchdb1_1
03a828e8164d   couchdb:3.4.2                       "tini -- /docker-ent…"   1 second ago    Up 1 second                 4369/tcp, 9100/tcp, 0.0.0.0:5994->5984/tcp, [::]:5994->5984/tcp                                                                         couchdb2_0
3270fa6aec4e   couchdb:3.4.2                       "tini -- /docker-ent…"   1 second ago    Up 1 second                 4369/tcp, 9100/tcp, 0.0.0.0:8084->5984/tcp, [::]:8084->5984/tcp                                                                         couchdb1_2
99ad61523a34   couchdb:3.4.2                       "tini -- /docker-ent…"   1 second ago    Up 1 second                 4369/tcp, 9100/tcp, 0.0.0.0:7994->5984/tcp, [::]:7994->5984/tcp                                                                         couchdb2_1
4ed2eb8c5e9d   couchdb:3.4.2                       "tini -- /docker-ent…"   1 second ago    Up 1 second                 4369/tcp, 9100/tcp, 0.0.0.0:8094->5984/tcp, [::]:8094->5984/tcp                                                                         couchdb2_2
802c1d8311f9   hyperledger/fabric-orderer:2.5.13   "orderer"                1 second ago    Up 1 second                 0.0.0.0:7050->7050/tcp, [::]:7050->7050/tcp, 0.0.0.0:7053->7053/tcp, [::]:7053->7053/tcp, 0.0.0.0:9443->9443/tcp, [::]:9443->9443/tcp   orderer.example.com
4e7a6e4bb626   couchdb:3.4.2                       "tini -- /docker-ent…"   1 second ago    Up 1 second                 4369/tcp, 9100/tcp, 0.0.0.0:5984->5984/tcp, [::]:5984->5984/tcp                                                                         couchdb1_0
576e9d17c275   hyperledger/fabric-ca:latest        "sh -c 'fabric-ca-se…"   8 seconds ago   Up 8 seconds                0.0.0.0:8054->8054/tcp, [::]:8054->8054/tcp, 7054/tcp, 0.0.0.0:18054->18054/tcp, [::]:18054->18054/tcp                                  ca_org2
b38e0ec52dec   hyperledger/fabric-ca:latest        "sh -c 'fabric-ca-se…"   8 seconds ago   Up 8 seconds                0.0.0.0:7054->7054/tcp, [::]:7054->7054/tcp, 0.0.0.0:17054->17054/tcp, [::]:17054->17054/tcp                                            ca_org1
67e1875dcc04   hyperledger/fabric-ca:latest        "sh -c 'fabric-ca-se…"   8 seconds ago   Up 8 seconds                0.0.0.0:9054->9054/tcp, [::]:9054->9054/tcp, 7054/tcp, 0.0.0.0:19054->19054/tcp, [::]:19054->19054/tcp                                  ca_orderer
9899b3a7a3dd   quay.io/minio/minio:latest          "/usr/bin/docker-ent…"   2 weeks ago     Exited (0) 46 hours ago                                                                                                                                             minio
a57f0b4b9971   app                                 "sh -c ./app"            4 weeks ago     Exited (1) 4 weeks ago                                                                                                                                              confident_babbage
c3b25cae7169   mongo                               "docker-entrypoint.s…"   4 weeks ago     Up 23 hours                 0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp                                                                                         mongo01
e410ef55d79f   apache/kafka:latest                 "/__cacert_entrypoin…"   6 weeks ago     Exited (143) 46 hours ago                                                                                                                                           broker
70c50cb3b5c4   redis                               "docker-entrypoint.s…"   6 weeks ago     Exited (0) 4 weeks ago                                                                                                                                              redis
3b2303f46b0a   confluentinc/cp-kafka:7.4.4         "/etc/confluent/dock…"   13 months ago   Exited (1) 4 weeks ago                                                                                                                                              kafka_kafka_1
f4b2339fab86   confluentinc/cp-zookeeper:7.4.4     "/etc/confluent/dock…"   13 months ago   Exited (143) 10 days ago                                                                                                                                            kafka_zookeeper_1
vessel@vessel-pc:~/src/fabric-demo/test-network$ ^C
vessel@vessel-pc:~/src/fabric-demo/test-network$

```

---
# Fabric-ca-client
Mạng fabric hiện tại: 
```

CONTAINER ID   IMAGE                               COMMAND                   CREATED             STATUS             PORTS                                                                                                                                   NAMES
0465ea2f8ef9   hyperledger/fabric-ccenv:2.5        "/bin/sh -c '\nset -e…"   7 seconds ago       Up 7 seconds                                                                                                                                               competent_brattain
c8117bd83878   hyperledger/fabric-peer:2.5.13      "peer node start"         About an hour ago   Up About an hour   0.0.0.0:9151->9151/tcp, [::]:9151->9151/tcp, 7051/tcp, 0.0.0.0:9545->9545/tcp, [::]:9545->9545/tcp                                      peer2.org1.example.com
18158690feab   hyperledger/fabric-peer:2.5.13      "peer node start"         About an hour ago   Up About an hour   0.0.0.0:7051->7051/tcp, [::]:7051->7051/tcp, 0.0.0.0:9444->9444/tcp, [::]:9444->9444/tcp                                                peer0.org1.example.com
07e2a24353d3   hyperledger/fabric-peer:2.5.13      "peer node start"         About an hour ago   Up About an hour   0.0.0.0:9051->9051/tcp, [::]:9051->9051/tcp, 7051/tcp, 0.0.0.0:9445->9445/tcp, [::]:9445->9445/tcp                                      peer1.org1.example.com
0ae0eab5f0dd   hyperledger/fabric-peer:2.5.13      "peer node start"         About an hour ago   Up About an hour   0.0.0.0:8100->8100/tcp, [::]:8100->8100/tcp, 7051/tcp, 0.0.0.0:8105->8105/tcp, [::]:8105->8105/tcp                                      peer0.org2.example.com
d74de89c8b28   hyperledger/fabric-peer:2.5.13      "peer node start"         About an hour ago   Up About an hour   0.0.0.0:8120->8120/tcp, [::]:8120->8120/tcp, 7051/tcp, 0.0.0.0:8125->8125/tcp, [::]:8125->8125/tcp                                      peer1.org2.example.com
b5a29f03a058   hyperledger/fabric-peer:2.5.13      "peer node start"         About an hour ago   Up About an hour   0.0.0.0:8140->8140/tcp, [::]:8140->8140/tcp, 7051/tcp, 0.0.0.0:8145->8145/tcp, [::]:8145->8145/tcp                                      peer2.org2.example.com
0aba38cdf134   couchdb:3.4.2                       "tini -- /docker-ent…"    About an hour ago   Up About an hour   4369/tcp, 9100/tcp, 0.0.0.0:7994->5984/tcp, [::]:7994->5984/tcp                                                                         couchdb2_1
205b25d6bade   hyperledger/fabric-orderer:2.5.13   "orderer"                 About an hour ago   Up About an hour   0.0.0.0:7050->7050/tcp, [::]:7050->7050/tcp, 0.0.0.0:7053->7053/tcp, [::]:7053->7053/tcp, 0.0.0.0:9443->9443/tcp, [::]:9443->9443/tcp   orderer.example.com
fd48a04f9821   couchdb:3.4.2                       "tini -- /docker-ent…"    About an hour ago   Up About an hour   4369/tcp, 9100/tcp, 0.0.0.0:5994->5984/tcp, [::]:5994->5984/tcp                                                                         couchdb2_0
fdc3bb3734c6   couchdb:3.4.2                       "tini -- /docker-ent…"    About an hour ago   Up About an hour   4369/tcp, 9100/tcp, 0.0.0.0:8094->5984/tcp, [::]:8094->5984/tcp                                                                         couchdb2_2
c1956f9f022c   couchdb:3.4.2                       "tini -- /docker-ent…"    About an hour ago   Up About an hour   4369/tcp, 9100/tcp, 0.0.0.0:5984->5984/tcp, [::]:5984->5984/tcp                                                                         couchdb1_0
1348a80744ce   couchdb:3.4.2                       "tini -- /docker-ent…"    About an hour ago   Up About an hour   4369/tcp, 9100/tcp, 0.0.0.0:7984->5984/tcp, [::]:7984->5984/tcp                                                                         couchdb1_1
2bcb04aa8631   couchdb:3.4.2                       "tini -- /docker-ent…"    About an hour ago   Up About an hour   4369/tcp, 9100/tcp, 0.0.0.0:8084->5984/tcp, [::]:8084->5984/tcp                                                                         couchdb1_2
c3b25cae7169   mongo                               "docker-entrypoint.s…"    4 weeks ago         Up 41 hours        0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp                                                                                         mongo01
vessel@vessel-pc:~$

```

Để hiểu được cách vận hành của CA trong Fabric:
Thử nghiệm với các trường hợp:

Cách để:
- [ ] revoke cert: 
	- [ ] revoke cert của một peer
	- [ ] revoke cert của một user
- [ ] renew cert: 
	- [ ] renew cert của peer và thử chaincode
	- [ ] renew cert của user và thử chaincode
- [ ] register new user:
	- [ ] cấp mới cert cho một user của tổ chức 1 với quyền read 
	- [ ] cấp mới cert cho một user của tổ chức 2 với quyền read, write**

---
# Fabric CA demo
Kịch bản demo: 
- Triển khai mạng Fabric với
	- Org1: 3 peers
	- Org2: 3 peers
	- Orderder: 1 node
	- CA.org.1
	- CA.org.2
	- CA.orderder
> Các peer, user, admin của từng org đều đã được register và enroll để tạo chứng chỉ
- Giải thích local MSP của một peer sẽ gồm những gì ? (peer certificate, CA certificate, private key,...) Và các cert/key trong đó dùng để làm gì ?
	- Show ở ngoài máy và trong docker container
- Giải thích thư mục TLS của từng peer
- Giải thích cách channel lưu thông tin root cert của các CA mà nó tin tưởng (lưu thông tin MSP) ? 
- Cấp chứng chỉ mới cho user mới (kenny) trong tổ chức 1
- Thu hồi chứng chỉ của kenny
- Thu hồi chứng chỉ của một peer trong tổ chức 1 và thử gọi gateway đến peer đó
## FAQ 
- [ ] Kiến trúc PKI là gì ?
- [ ] X.509 gồm những thông tin gì ?
- [ ] Quá trình một actor verify [[digital certificate]] của một actor khác?
 - [ ] Tại sao cần lưu các identity 
- [ ] Tại sao DB của CA là readonly ? Có đổi thành write được không
- [ ] Có thể dùng chung 1 CA duy nhất cho cả 2 tổ chức và orderer không ?
- [ ] 1 tổ chức có thể có 2 CA được không? 

Đây là kịch bản demo để giúp hiểu về Fabric CA về trong mạng blockchain Hyperledger Fabric.
Hãy góp ý kịch bản và thêm các câu hỏi FAQ