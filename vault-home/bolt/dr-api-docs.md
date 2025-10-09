focus: openapi.yaml

wrong error response schema:

```
SuccessResponse:
  type: object
  properties:
    status:
      type: string
      example: "success"
    message:
      type: string
      example: "Operation completed successfully"
```

read: backend/common/response/http_response.go for response schema

---
focus: openapi.yaml

currently, it describe wrong error likes BadRequest
```
BadRequest:  
  description: "Bad Request - The server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing)."  
  content:  
    application/json:  
      schema:  
        $ref: '#/components/schemas/ErrorResponse'  
      example:  
        error_code: 40001  
        status_code: 400  
        error: "Invalid request body"
```
```
{

"status_code": 400,

"error": "Field wrong type",

"error_code": 0

}
```
read: backend/common/response/http_response.go for response schema
