### Model

**miniapp*

| Column          | Type     | Example                                                                                                                                    |
| --------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| _id             | ObjectID |                                                                                                                                            |
| app_id          | string   | org.example.miniapp                                                                                                                        |
| name            | string   |                                                                                                                                            |
| description     | string   |                                                                                                                                            |
| current_version | string   | 1.0.1                                                                                                                                      |
| icons           | array    | "icons": [<br>    {<br>      "label": "Red lightning",<br>      "src": "common/icons/icon.png",<br>      "sizes": "48x48"<br>    }<br>  ], |
| req_permissions | []string | [<br>      "system.permission.LOCATION",<br>      "system.permission.CAMERA",<br>  ],                                                      |
| device_type     | []string | [<br>     "ios",<br>     "android",<br>     "pc",...<br>  ]                                                                                |
| createdAt       | time     |                                                                                                                                            |
| updatedAt       | time     |                                                                                                                                            |
**miniapp_subscribe**

| Column          | Type       | Example |
| --------------- | ---------- | ------- |
| _id             | ObjectID   |         |
| user_id         | ObjectID   |         |
| miniapp_id_list | []ObjectID |         |
| createdAt       | time       |         |
| updatedAt       | time       |         |

**miniapp_version**

| Column      | Type     | Example     |
| ----------- | -------- | ----------- |
| _id         | ObjectID |             |
| miniapp_id  | ObjectID |             |
| version     | string   | 1.0.1       |
| type        | string   | dev,stg,pro |
| is_disable  | bool     |             |
| createdAt   | time     |             |
| change_log  | string   |             |
| updatedAt   | time     |             |
| package_url | string   |             |



