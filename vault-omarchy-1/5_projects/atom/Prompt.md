This project is to call gRPC to another service (Atom)

```go
// in config.go

AtomServerName string `env:"ATOM_SERVER_NAME" envDefault:"localhost"`

AtomServerAddr string `env:"ATOM_SERVER_ADDR" envDefault:"127.0.0.1"`

```


## Issue 1: 
Add config proxy server to util @atom_client : It must connect proxy server before connect gRPC, but currently there is no config to connect proxy server
## Issue 2: 
Proxy server is Squid version 6. Currently we have to add this config, without it we will failed to connect gRPC:

```

# 1. Define the specific target (Your gRPC Server)

# Replace 101.10.30.4 with your actual server IP

acl grpc_server dst 101.10.30.4

  

# 2. Define the port (30000)

acl grpc_port port 30000

  

# 3. CRITICAL RULE: Splice (Passthrough) this specific traffic

# This tells Squid: "Do not touch/decrypt this. Just forward the packets."

ssl_bump splice grpc_server grpc_port

  

# 4. Peek at everything else (Standard Squid config)

# You likely already have lines like these below.

# Make sure the 'splice' line above comes BEFORE any 'bump' lines.

ssl_bump peek step1 all

ssl_bump bump all

```

--> We need to find out if there is any way to connect gRPC through proxy without changing squid config

### Error grpc log without squid config:

```

  

2025/12/01 17:11:21 INFO: [core] [Channel #1] Channel Connectivity change to CONNECTING

  

2025/12/01 17:11:21 INFO: [core] [Channel #1 SubChannel #2] Subchannel created

  

2025/12/01 17:11:21 INFO: [core] [Channel #1 SubChannel #2] Subchannel Connectivity change to CONNECTING

  

2025/12/01 17:11:21 INFO: [core] [Channel #1 SubChannel #2] Subchannel picks a new address "10.1.102.3:3128" to connect

  

2025/12/01 17:11:23 INFO: [core] Creating new client transport to "{Addr: \"10.1.102.3:3128\", ServerName: \"server.threatintelligenceplatform.com\", Attributes: {\"<%!p(proxyattributes.keyType=grpc.resolver.delegatingresolver.proxyOptions)>\": \"<%!p(proxyattributes.Options={<nil> 101.10.30.4:30000})>\" }, }": connection error: desc = "transport: authentication handshake failed: credentials: cannot check peer: missing selected ALPN property. If you upgraded from a grpc-go version earlier than 1.67, your TLS connections may have stopped working due to ALPN enforcement. For more details, see: https://github.com/grpc/grpc-go/issues/434"

````