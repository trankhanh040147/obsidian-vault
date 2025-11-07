- websocket 101
      	ref: https://syntackle.com/blog/websockets-101-JiIrdn/
	- [ ] the importance of ack
	- [ ] architecture: bridge, hub
	- [ ] build a chat bot with WS
	- [ ] scaling websocket
	      https://composehq.com/blog/scaling-websockets-1-23-25?ref=dailydev
---
- oauth2
	- [ ] state --> dùng để phân biệt cho từng phiên đăng nhập của client
	- [ ] scopes --> openid vs profile
	- [ ] tạo login session bên phía sử dụng để quản lý (lưu = state & user-agent)
		- [ ] why user-agent ?
	- [ ] redirectURI
	- [ ] AuthEndpoint
	- [ ] response_type, response_mode ?
		- [ ] response_mode: query, fragment, form_post, web_message
	- [ ] luồng đăng nhập: Get Login Params --> Authorize từ provider (.../oauth2/authorize) để lấy token --> Login = token
	- [ ] code challenge & code challenge method
		- In **OAuth2’s PKCE (Proof Key for Code Exchange)** extension, the code challenge is a value derived from a randomly generated string known as the code verifier.
		- **Generation:** The client first creates a high-entropy, random string (the code verifier). It then transforms this verifier—commonly using a SHA256 hash followed by base64 URL-encoding—to produce the code challenge.
		- **Usage in the Authorization Request:** The client includes the code challenge in its initial authorization request to the authorization server.
		- **Token Request Verification:** Later, when the client exchanges the authorization code for an access token, it sends the original code verifier. The authorization server applies the same transformation to this verifier and compares it with the original code challenge. If they match, it confirms that the request is from the same client that initiated the flow, thus mitigating interception attacks.
		This mechanism enhances the security of OAuth2 authorization code flows, especially for public clients (such as mobile or desktop apps) that cannot securely store client secrets.
	- [ ] why code challenge gen by S256 method need to be stripped padding (trim right "=")
		Padding in Base64 is used to ensure the encoded output has a length that’s a multiple of four. However, in the context of PKCE (RFC 7636), the specification requires that any trailing "=" padding characters be removed from the base64url-encoded hash. This is done to:
		- **Maintain Consistency:** The code challenge must match exactly between the client’s initial authorization request and the later token exchange. Removing padding ensures a consistent, canonical representation.
		- **Enhance URL Safety:** Padding characters are unnecessary and can sometimes complicate URL parsing or comparisons. Removing them helps ensure that the code challenge remains URL-safe.
	- [ ] code generate when Authorize 
		- use **CRC32Table**
		- format **%08x-%s**
	- [ ] IntrospectToken, what for ?
---
- server send event
	- [ ] event source 
		- [ ] method allow: GET
---
- gossip protocol 
	- gossip in block chain ?
	  https://www.liminalcustody.com/knowledge-center/what-is-gossip-protocol/
---
- JIT compiler vs AOT compiler
---
- jitsi
	- [ ] architecture
	- [ ] octo protocol