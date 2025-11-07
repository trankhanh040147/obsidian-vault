  
You are a senior software principal architect specializing in containerization, networking, and secure API integrations.

Your task is to analyze and correct a Python script that fails to connect to a remote Docker daemon protected by a proxy requiring HTTP Basic Authentication.

**Context:** The provided Python script attempts to connect to a TLS-enabled Docker daemon endpoint (`https://dev-docker-socket-proxy.signetcenter.dev:443`) using the `docker-py` library. The script fails with a `401 Unauthorized` error, even though a `curl` command using the same credentials via HTTP Basic Authentication (`-u admin:PASSWORD`) succeeds.

This discrepancy indicates that the proxy requires an `Authorization: Basic ...` header on all requests, which the original script does not correctly configure. The script misuses the `credstore_env` parameter, which is not designed for direct daemon authentication credentials. The correct approach is to use the lower-level `docker.api.APIClient` and inject the authentication credentials into the underlying `requests` session.

**Problematic Code and Error:**

```
# --- Original Script Snippet ---
# ...
client = docker.DockerClient(
    base_url=DOCKER_HOST_URL,
    tls=tls_config,
    credstore_env=auth_config, # This is incorrect for daemon authentication
)
# ...

# --- Resulting Error ---
An unexpected error occurred: Error while fetching server API version: 401 Client Error for https://dev-docker-socket-proxy.signetcenter.dev:443/version: Unauthorized ("401 Unauthorized")
```

**Requirements for the Corrected Script:**

1. **Resolve the 401 Error:** The primary goal is to fix the authentication failure by correctly sending HTTP Basic Auth headers.
2. **Implement Correct Authentication:** The solution must correctly layer HTTP Basic Authentication (for the proxy) on top of the TLS configuration (for the Docker daemon connection).
3. **Use Low-Level API Client:** Leverage the `docker.api.APIClient` and a custom `requests.Session` object. This provides the necessary control to inject the `Authorization` header into every API request sent to the daemon proxy. The high-level `docker.DockerClient` does not easily expose this functionality.
4. **Maintain Security:** Continue to use the provided TLS certificates (`ca.pem`, `client.pem`, `client-key.pem`) for a secure, verified connection.
5. **Code Quality:** The final script must be robust, self-contained, and include clear comments explaining the authentication mechanism. It should define all variables and handle potential exceptions gracefully.

**Final Output:** Produce a single, complete, and corrected Python script that successfully connects to the Docker daemon through the authenticating proxy and prints the server version information. The script should be ready to run, assuming the certificate files and credentials are correct.