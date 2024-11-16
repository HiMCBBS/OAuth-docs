# 米坛社区 OAuth API 文档

### 1. 获取 申请 客户端ID 和 客户端密钥
```
客户端ID：应用的唯一标识。用于识别接入应用的身份  
客户端密钥：客户端ID对应的密钥，访问用户资源时用来验证应用的合法性 
权限类型： 用于判断权限
```
### 2. 放置 米坛社区帐号 登录 按钮

将米坛社区帐号 登录 按钮或选项放入您的APP或者网页  

### 3. 唤起接口获取授权码(Authorization Code)

用户跳转地址：  
```https://www.bandbbs.cn/oauth2/authorize```  

请求参数：  
|  参数   | 说明  |
|  ----  | ----  |
| type  | 授权类型，此值固定为“authorization_code” |
| client_id  | 分配给应用的客户端ID |
| redirect_uri  | 成功授权后的回调地址，必须是申请客户端ID时提供的回调地址，注意需要进行URLEncode |
| state  | 状态值，必须为32位由小写字母和/或数字组成的值。用于防止CSRF攻击，成功授权后回调时会原样带回。请务必严格检查用户与state参数状态的绑定 |
| response_type | 响应类型，此值固定为“code” |
| scope | 申请的权限类型，以 %20 分割多个权限  |

例如：  
```https://www.bandbbs.cn/oauth2/authorize?type=authorization_code&client_id=2550505&redirect_uri=https://test.bandbbs.cn/callback.php&response_type=code&scope=user%3Aread%20user%3Awrite&state=7a990681fc5c697092236ee1e4ece2d0```  

返回说明：  
1. 如果用户成功登录授权，则会跳转到指定的回调地址，并在redirect_uri地址后带上Authorization Code和原始的state值。如：  
```https://test.bandbbs.cn/callback.php?code=988940bb8e7ebcae2fdfa77f2c825cd7&state=7a990681fc5c697092236ee1e4ece2d0```


|  参数   | 说明  |
|  ----  | ----  |
| code  | 授权码(Authorization Code) |
| state  | 请求时的状态值，用于防止CSRF攻击。请务必严格检查用户与state参数状态的绑定|

2. 如果用户在登录授权过程中取消登录流程，登录页面直接关闭  


### 4. 通过授权码(Authorization Code)获取访问令牌(Access Token)

此步骤必须在您的应用服务器完成

请求地址：  
```https://www.bandbbs.cn/api/oauth2/token```  
请求方法：  
POST 

请求参数：  
|  参数   | 说明  |
|  ----  | ----  |
| grant_type  | 授权类型，此值为“authorization_code” |
| client_id  | 分配给应用的appid |
| client_secret  | 分配给应用的客户端密钥 |
| code  | 上一步用户跳转回调地址所携带的的授权码(Authorization Code) |
| redirect_uri  | 成功授权后的回调地址，必须是申请客户端ID时提供的回调地址，注意需要进行URLEncode |


返回值：  
如果成功返回，将返回类似如下内容
```
{
    "access_token": "4f40d5ee8bf0476cb6b6f42ed53247a1",
    "refresh_token": "f6bec4e6ef7470fc6f0101e3975ad4b9",
    "token_type": "bearer",
    "expires_in": 7200,
    "issue_date": 1722550505
}
```

请注意保存访问令牌(Access Token) 和 刷新令牌(Refresh Token)

### 5.通过访问令牌(Access Token)访问资源  

此步骤必须在您的应用服务器完成

对于获取当前token的信息 请使用：

请求地址：  
```https://www.bandbbs.cn/api/oauth2/token```  
请求方法：  
GET

请求参数： 
|  参数   | 说明  |
|  ----  | ----  |
| token  | 用户的访问令牌(Access Token) |

返回值：  
如果成功返回，将返回类似如下内容
```
{
    "user_id": 1,
    "scope": {
        "user:read": true,
        "user:write": true
    },
    "expires_in": 7200,
    "issue_date": 1722550505
}
```

对于使用其他API 请参照
[https://xenforo.com/community/pages/api-endpoints/](https://xenforo.com/community/pages/api-endpoints/)

最可能用到的API一般在“me/”端点部分
[https://xenforo.com/community/pages/api-endpoints/#route_get_me_](https://xenforo.com/community/pages/api-endpoints/#route_get_me_)

直接按照端点文档调用需要的API即可
在请求头使用 Bearer Token 进行授权 Token值为 用户的访问令牌(Access Token)

不需要使用 XF-Api-Key 等授权密钥和请求参数

### 6. 访问令牌(Access Token)的刷新

此步骤必须在您的应用服务器完成

请求地址：  
```https://www.bandbbs.cn/api/oauth2/token```  
请求方法：  
POST 

请求参数：  
|  参数   | 说明  |
|  ----  | ----  |
| grant_type  | 授权类型，此值为“refresh_token” |
| client_id  | 分配给应用的appid |
| client_secret  | 分配给应用的客户端密钥 |
| refresh_token  | 用户的刷新令牌(Refresh Token) |
| redirect_uri  | 成功授权后的回调地址，必须是申请客户端ID时提供的回调地址，注意需要进行URLEncode |


返回值：  
如果成功返回，将返回类似如下内容
```
{
    "access_token": "4f40d5ee8bf0476cb6b6f42ed53247a1",
    "refresh_token": "f6bec4e6ef7470fc6f0101e3975ad4b9",
    "token_type": "bearer",
    "expires_in": 7200,
    "issue_date": 1722550505
}
```

请注意保存访问令牌(Access Token) 和 刷新令牌(Refresh Token)
