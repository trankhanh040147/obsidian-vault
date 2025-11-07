```bash
export PWD=
```
```bash
  export FABRIC_CA_CLIENT_HOME=/home/vessel/src/fabric-demo/test-network/organizations/peerOrganizations/org1.example.com/
```
---
## Quá trình đăng ký và ghi danh một actor mới: 
1.  Register
```bash
infoln "Registering peer0"  
set -x  
fabric-ca-client register --caname ca-org1 --id.name peer0 --id.secret peer0pw --id.type peer --tls.certfiles "${PWD}/organizations/fabric-ca/org1/ca-cert.pem"  
{ set +x; } 2>/dev/null
```
- Dùng để đăng ký (name, pw, type)
---

# FAQ

## Issuer khi deploy mạng fabric bằng CA & cryptogen

Khi deploy bằng cryptogen (./network up):
ca.org1.example.com-cert.pem:
```

ca.org1.example.com
Identity: ca.org1.example.com
Verified by: ca.org1.example.com
Expires: 10/08/2035

Subject Name
C (Country):	US
ST (State):	California
L (Locality):	San Francisco
O (Organization):	org1.example.com
CN (Common Name):	ca.org1.example.com
Issuer Name
C (Country):	US
ST (State):	California
L (Locality):	San Francisco
O (Organization):	org1.example.com
CN (Common Name):	ca.org1.example.com
Issued Certificate
Version:	3
Serial Number:	00 84 90 0A 8D 5F 3A 00 BF 29 58 8D F2 BB 1C F4 30
Not Valid Before:	2025-08-12
Not Valid After:	2035-08-10
Certificate Fingerprints
SHA1:	FA 6A B8 A7 99 94 AE 3F 6A FE 2D E4 F5 B2 84 BA 34 72 B0 D9
MD5:	EE 07 A8 83 EB 13 3B EE 6D 32 6D FA 44 B4 CD FF
Public Key Info
Key Algorithm:	Elliptic Curve
Key Parameters:	06 08 2A 86 48 CE 3D 03 01 07
Key Size:	256
Key SHA1 Fingerprint:	8E 6C 52 27 23 E8 DA 8D 38 90 DB 51 C0 01 2F FF E2 B7 C5 5F
Public Key:	04 92 BC 60 55 6A D5 95 A1 73 57 15 DC 9E EC 77 94 3E 53 CD A6 4B 7B 62 E2 86 0A E8 87 1F FC 80 93 14 E6 5F 09 26 C1 8F CB B6 51 6A 66 EF F7 ED DF 02 56 0D CB 9E DF 92 75 98 B4 68 28 CC 6F CC 79
Key Usage
Usages:	Digital signature Key encipherment Certificate signature Revocation list signature
Critical:	Yes
Extended Key Usage
Allowed Purposes:	Client Authentication Server Authentication
Critical:	No
Basic Constraints
Certificate Authority:	Yes
Max Path Length:	Unlimited
Critical:	Yes
Subject Key Identifier
Key Identifier:	33 F6 26 04 5C 5C C1 21 52 2C B1 24 12 55 A9 3F D3 17 C4 91 B0 A1 CC 60 7A 67 B0 EC 1C 5B D9 B2
Critical:	No
Signature
Signature Algorithm:	SHA256 with ECDSA
Signature:	30 45 02 20 42 1A F0 CD 8D B4 34 1B 2E 4A 74 00 A1 F8 61 08 BD B9 96 AB 08 9C 82 27 BE 7D 7D A7 68 11 C2 DF 02 21 00 9D 6A 64 DC 1C 9F 66 41 1C FA F4 1F BA 7E 34 3A 41 F5 C2 46 9D 3C 69 B5 EB 92 EA 21 9D 1C B6 EC


```
ca.org2.example.com-cert.pem:
```

ca.org2.example.com
Identity: ca.org2.example.com
Verified by: ca.org2.example.com
Expires: 10/08/2035

Subject Name
C (Country):	US
ST (State):	California
L (Locality):	San Francisco
O (Organization):	org2.example.com
CN (Common Name):	ca.org2.example.com
Issuer Name
C (Country):	US
ST (State):	California
L (Locality):	San Francisco
O (Organization):	org2.example.com
CN (Common Name):	ca.org2.example.com
Issued Certificate
Version:	3
Serial Number:	2C 57 C0 6D 14 CE 51 D4 3C E2 7A D3 61 E6 0C 1A
Not Valid Before:	2025-08-12
Not Valid After:	2035-08-10
Certificate Fingerprints
SHA1:	2C 6B 5E 30 73 93 FE 30 03 16 59 FE 12 6A 47 94 B2 10 61 4E
MD5:	E6 78 D5 35 C9 96 04 4B F9 18 42 40 A8 E6 9E 34
Public Key Info
Key Algorithm:	Elliptic Curve
Key Parameters:	06 08 2A 86 48 CE 3D 03 01 07
Key Size:	256
Key SHA1 Fingerprint:	3D C7 F2 74 55 89 FA DF 91 D9 B9 43 FD 1D CA EA 09 DA 51 3F
Public Key:	04 E9 4D E7 2A 66 7D E2 3C AA 43 3B FB 8C 22 C1 E3 9F F7 D6 1A FB 6C C2 A3 F7 57 18 1B ED A9 7D 1A 0C D9 A2 13 FD 83 C8 8E 7D 9B BF C6 C8 83 90 ED 01 DA 38 44 D0 68 CE 6E 41 48 95 25 4B 21 C9 9B
Key Usage
Usages:	Digital signature Key encipherment Certificate signature Revocation list signature
Critical:	Yes
Extended Key Usage
Allowed Purposes:	Client Authentication Server Authentication
Critical:	No
Basic Constraints
Certificate Authority:	Yes
Max Path Length:	Unlimited
Critical:	Yes
Subject Key Identifier
Key Identifier:	4B 4C 4E B5 0C 16 0B 01 37 C5 2D C6 FD 7D 93 83 B1 EC E6 AA BF 29 9E BA 92 A6 F4 8B 83 9E 3F A9
Critical:	No
Signature
Signature Algorithm:	SHA256 with ECDSA
Signature:	30 44 02 20 21 A3 FB 53 79 F3 BE 87 34 B8 AB 4B 9D 63 D7 90 D5 A9 BE 8B FC B8 D3 DF E7 3D C1 C4 D6 A4 96 EA 02 20 2C B6 33 84 ED 7E 12 00 99 C1 2C 27 80 2F DB E8 0B 64 F8 47 38 89 AB BE 09 BE DA 07 35 97 34 EE


```

**Khi deploy bằng Fabric CA (./network up -ca):**

ca.org1.example.com-cert.pem:
```

fabric-ca-server
Identity: fabric-ca-server
Verified by: fabric-ca-server
Expires: 08/08/2040

Subject Name
C (Country):	US
ST (State):	North Carolina
O (Organization):	Hyperledger
OU (Organizational Unit):	Fabric
CN (Common Name):	fabric-ca-server
Issuer Name
C (Country):	US
ST (State):	North Carolina
O (Organization):	Hyperledger
OU (Organizational Unit):	Fabric
CN (Common Name):	fabric-ca-server
Issued Certificate
Version:	3
Serial Number:	4C 0E 48 0A A1 B0 B3 95 46 CF 3B 3F 0F C6 71 4A 61 7D D3 87
Not Valid Before:	2025-08-12
Not Valid After:	2040-08-08
Certificate Fingerprints
SHA1:	AD CB 66 03 7B D3 7E 86 FB 99 9F 51 2D BE F7 B7 EF 5E 64 22
MD5:	94 E2 34 0A F8 E5 99 D0 4F E2 FB 57 34 9A 77 D8
Public Key Info
Key Algorithm:	Elliptic Curve
Key Parameters:	06 08 2A 86 48 CE 3D 03 01 07
Key Size:	256
Key SHA1 Fingerprint:	03 BC C6 62 A2 A9 65 70 B7 C1 3E 65 E3 33 26 2D EB A4 71 D8
Public Key:	04 86 3B BD C3 C3 79 E1 F5 C0 8E AE EC 46 64 77 5F DB D7 11 BE A5 84 82 E0 E4 3D DE D8 54 47 69 73 23 60 14 04 DA AA 12 7F 1F 18 CC 4C F1 9B DC 2C 33 0E 7C 6A 59 85 59 62 01 C7 30 33 68 13 B4 3F
Key Usage
Usages:	Certificate signature Revocation list signature
Critical:	Yes
Basic Constraints
Certificate Authority:	Yes
Max Path Length:	1
Critical:	Yes
Subject Key Identifier
Key Identifier:	DE 42 8A C2 4A B2 00 E7 1C 52 C8 38 B9 D1 8C 2E A0 14 89 CE
Critical:	No
Signature
Signature Algorithm:	SHA256 with ECDSA
Signature:	30 45 02 21 00 C3 12 2C B1 B4 F8 4D 4F A0 E9 AC 7D 15 B4 BA 37 32 E1 11 1E E5 E5 17 43 CE BD 98 DE A8 3B 37 26 02 20 02 0E BD E3 A0 02 6D 06 90 AB 1B C1 29 2F B1 3B 6C 09 B8 59 37 F1 50 E2 46 E8 65 2A 64 C5 C2 CA
```

ca.org2.example.com-cert.pem:
```

fabric-ca-server
Identity: fabric-ca-server
Verified by: fabric-ca-server
Expires: 08/08/2040

Subject Name
C (Country):	US
ST (State):	North Carolina
O (Organization):	Hyperledger
OU (Organizational Unit):	Fabric
CN (Common Name):	fabric-ca-server
Issuer Name
C (Country):	US
ST (State):	North Carolina
O (Organization):	Hyperledger
OU (Organizational Unit):	Fabric
CN (Common Name):	fabric-ca-server
Issued Certificate
Version:	3
Serial Number:	6F 0F 48 45 28 4C 29 24 4D F9 8F 8F 6D A9 4C B9 C7 F5 E4 3F
Not Valid Before:	2025-08-12
Not Valid After:	2040-08-08
Certificate Fingerprints
SHA1:	43 82 37 A4 95 09 8C 7B FB 8D 51 75 13 0B DF F7 C9 5F B3 D2
MD5:	76 58 3A C2 EB AE F4 65 B1 FF A3 C5 E3 63 B5 DA
Public Key Info
Key Algorithm:	Elliptic Curve
Key Parameters:	06 08 2A 86 48 CE 3D 03 01 07
Key Size:	256
Key SHA1 Fingerprint:	EA A1 9A 81 37 FD 2C 19 7D E7 8D A8 4F EC EE F9 AA 89 20 1A
Public Key:	04 A1 7C D2 6A DD 52 5D 8C 6C 36 DF 41 0D A8 CC 59 A3 BE 23 0F C6 FB 2B EE AB 5D D7 7B 54 3A D7 56 A3 07 55 63 20 39 94 9E DC B7 5F 2C 0D FF 08 1A E4 2B F5 9E 77 1A 67 E6 C3 3E CE 02 E8 4B 5B 1A
Key Usage
Usages:	Certificate signature Revocation list signature
Critical:	Yes
Basic Constraints
Certificate Authority:	Yes
Max Path Length:	1
Critical:	Yes
Subject Key Identifier
Key Identifier:	D6 54 81 3B 80 56 59 DE C6 99 7F 0D EA BA 92 4B 93 9A 49 D3
Critical:	No
Signature
Signature Algorithm:	SHA256 with ECDSA
Signature:	30 45 02 21 00 BE 43 C4 C5 03 07 CC F9 37 80 D8 0D 55 4C 8D 53 B5 0F 57 32 D4 3F 15 3B 23 20 81 C2 4E 25 4B 70 02 20 78 0A 0B 0F 14 87 DA A4 DA F4 63 04 8B D8 93 B8 64 E9 20 87 A5 02 A2 EC AB 4D B8 86 80 21 42 88


```

Why there are the differences ?