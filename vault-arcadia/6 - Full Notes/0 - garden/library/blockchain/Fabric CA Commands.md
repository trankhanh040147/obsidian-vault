Set **FABRIC_CA_CLIENT_HOME**:
```
  export FABRIC_CA_CLIENT_HOME=${PWD}/organizations/peerOrganizations/org1.example.com/
  export FABRIC_CA_CLIENT_TLS_CERTFILES=${PWD}/organizations/fabric-ca/org1/tls-cert.pem
```

**Config TLS:** 
```bash
sudo vim ${PWD}/organizations/peerOrganizations/org1.example.com/fabric-ca-client-config.yaml
```
*sửa*: 
```bash
  certfiles: ../../fabric-ca/org1/tls-cert.pem
```
---
**Register**
```bash
fabric-ca-client register --id.name "user2" --id.secret "user2pw" --id.type client
```
[w/ affilation & attrs]
```bash
fabric-ca-client register --id.name "admin2" --id.secret "admin2pw" --id.type admin  --id.affiliation org1.department1 --id.attrs 'hf.Revoker=true,admin=true:ecert'
```

**Enroll**
```bash
 fabric-ca-client enroll -u "https://user2:user2pw@localhost:7054" -M "${PWD}/organizations/peerOrganizations/org1.example.com/users/User2@org1.example.com/msp"
```

**Revoke**
```bash
fabric-ca-client revoke -e user1 --gencrl -r keycompromise
```

base64 encode:
```bash
base64 -i organizations/peerOrganizations/org1.example.com/msp/crls/crl.pem | tr -d '\n' > crl_base64
```