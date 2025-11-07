# Triển khai dịch vụ
```
[DEV][STG][dg-receiver-management-api] Triển khai dịch vụ
**MÔI TRƯỜNG**: DEV, STG
**DỊCH VỤ**: dg-receiver-management-api
**ENV:**
**NOTE** (nếu có):
- git: https://gitlab.signetcenter.systems/signetpro/be/defense-gateway/dg-receiver-management-api
```

[DEV][STG][survey-sgi-hub] Triển khai dịch vụ
**DỊCH VỤ**: sigpro-api-conversation
**MÔI TRƯỜNG**: DEV, STG
**ENV:**
- PORT=8246
**NOTE** (nếu có):
- git: https://gitlab.signetcenter.systems/signetpro/be/app/survey-sgi-hub/
---
# Cập nhật dịch vụ`
[DEV][STG][sigpro-api-conversation] Cập nhật ENV
**DỊCH VỤ**: sigpro-api-conversation
**MÔI TRƯỜNG**: DEV, STG
**ENV cần xóa:**
- MONGODB_MSG_URI
- MONGODB_MSG_NAME
**ENV cần thêm:**
- MONGODB_COMMUNITY_GROUP_URI
- MONGODB_COMMUNITY_GROUP_NAME
**NOTE** (nếu có): 
