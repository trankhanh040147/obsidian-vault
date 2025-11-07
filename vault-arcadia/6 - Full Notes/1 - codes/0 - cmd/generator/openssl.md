#### Generating an RSA Key Pair

To generate a 4096-bit RSA private key, use the following command:

Bash

```
openssl genrsa -out rsa_private.pem 4096
```

To extract the corresponding public key:

Bash

```
openssl rsa -in rsa_private.pem -pubout -out rsa_public.pem
```

#### Generating an EC (ECDSA) Key Pair

To generate an ECDSA private key using the popular `prime256v1` curve:

Bash

```
openssl ecparam -name prime256v1 -genkey -out ec_private.pem
```

To extract the public key:

Bash

```
openssl ec -in ec_private.pem -pubout -out ec_public.pem
```

#### Generating an Ed (EdDSA) Key Pair

For generating an EdDSA private key using the `Ed25519` curve:

Bash

```
openssl genpkey -algorithm Ed25519 -out ed_private.pem
```

To extract the public key:

Bash

```
openssl pkey -in ed_private.pem -pubout -out ed_public.pem
```