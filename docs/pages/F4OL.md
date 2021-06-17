# F4 OL-2000 APP



## 授权获取`code`

- 访问地址：`http://host/auth/authorize?client_id=client_id&redirect_uri=redirect_uri`

  `client_id`：网站地址

  `redirect_uri`：和`client_id`同源的回调地址

  `client_id`和`redirect_uri`需要encode编码转换，如：

  ```javascript
  "http://host/auth/authorize?client_id="+encodeURIComponent(client_id)+"&redirect_uri="+encodeURIComponent(redirect_uri)
  ```

  授权登录后页面会跳转到`redirect_uri`并在地址栏附带`code`

## 通过`code`换取`token`

拿到上一步授权获取到的code的值

- 请求地址：`http://host/auth/token`

- 请求方式：`POST`

- 请求头：`Content-Type:application/x-www-form-urlencoded`

- 请求参数：

  | 字段       | 类型   | 是否必填 |
  | ---------- | ------ | -------- |
  | grant_type | String | Y        |
  | code       | String | Y        |
  | client_id  | String | Y        |

  `grant_type`传参固定为`authorization_code`

- 返回值示例：

  ```json
  {
      "access_token": "ABCDEFGH",
      "expires_in": 1800,
      "refresh_token": "IJKLMNOPQRST",
      "token_type": "Bearer"
  }
  ```

  `access_token`有效期为1800秒

## 刷新`token`

- 请求地址：`http://host/auth/token`

- 请求方式：`POST`

- 请求头：`Content-Type:application/x-www-form-urlencoded`

- 请求参数：

  | 字段          | 类型   | 是否必填 |
  | ------------- | ------ | -------- |
  | grant_type    | String | Y        |
  | refresh_token | String | Y        |
  | client_id     | String | Y        |

  `grant_type`传参固定为`refresh_token`，`refresh_token`为上一步授权获取到的`refresh_token`

  

- 返回值示例：

  ```json
  {
      "access_token": "ABCDEFGH",
      "expires_in": 1800,
      "token_type": "Bearer"
  }
  ```

## 获取全部实体状态信息

- 请求地址：`http://host/api/states`

- 请求方式：`GET`

- 请求头：`Content-Type:application/json`

- 请求参数：无

- 返回值示例：

  ```json
  [
      {
          "attributes": {},
          "entity_id": "sun.sun",
          "last_changed": "2016-05-30T21:43:32.418320+00:00",
          "state": "below_horizon"
      },
      {
          "attributes": {},
          "entity_id": "process.Dropbox",
          "last_changed": "22016-05-30T21:43:32.418320+00:00",
          "state": "on"
      }
  ]
  ```

## 获取对应`entity_id`实体的状态信息

- 请求地址：`http://host/api/states/<entity_id>`

- 请求方式：`GET`

- 请求头：`Content-Type:application/json`

- 请求参数：无

- 返回值示例：

  ```json
  {
     "attributes":{
        "azimuth":336.34,
        "elevation":-17.67,
        "friendly_name":"Sun",
        "next_rising":"2016-05-31T03:39:14+00:00",
        "next_setting":"2016-05-31T19:16:42+00:00"
     },
     "entity_id":"sun.sun",
     "last_changed":"2016-05-30T21:43:29.204838+00:00",
     "last_updated":"2016-05-30T21:50:30.529465+00:00",
     "state":"below_horizon"
  }
  ```

## 改变实体状态     `switch`开关

- 请求地址：`http://host/api/services/modbus/write_coil`

- 请求方式：`POST`

- 请求头：`Content-Type:application/json`

- 请求参数：

  | 字段    | 类型    | 是否必填 | 说明                            |
  | ------- | ------- | -------- | ------------------------------- |
  | hub     | String  | Y        | PLC名称 |
  | unit    | Number  | Y        | 从站编号 |
  | address | Number  | Y        | Modbus地址                      |
  | state   | Boolean | Y        | 要改变成的状态 `true` |

## 改变数值 

- 请求地址：`http://host/api/services/modbus/write_register`

- 请求方式：`POST`

- 请求头：`Content-Type:application/json`

- 请求参数：

  | 字段    | 类型            | 是否必填 | 说明                                                         |
  | :------ | --------------- | -------- | :----------------------------------------------------------- |
  | hub     | String          | Y        | PLC名称                                                      |
  | unit    | Number          | Y        | 从站编号                                                     |
  | address | Number          | Y        | Modbus地址                                                   |
  | value   | Number \| Array | Y        | 读写类型为`INT`时参数为`Number`类型，读写类型为`REAL`或`DINT`时参数为`Array`类型；参数为`Array`类型时，如[n,m]，表示`n*2^16+m` |

