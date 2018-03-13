# PTSDK 接入文档

Placement Test （后文若无歧义均简称为 **PT**）可为用户提供英语水平测试。

名词说明：

**用户**：接入 PTSDK 的第三方 app 的使用者

**开发者**：接入 PTSDK 的第三方 app 的开发者（通常而言，也就是本文档的目标读者）

**PT 服务器**：由流利说提供 PT 服务的服务器

如发现行为与文档不一致，请及时与我们联系。

## 关于 token 的约定：

1. token 有一定的有效期。有效期目前暂定为24小时，未来不排除会缩短或延长。
2. PT 进行过程中会多次向 PT 服务器发送请求，每次请求均须使用来自同一开发者的 token，但并不要求是同一 token。
3. 同一 token 可由第三方开发者在自己服务器缓存并下发给多个第三方 app 的设备，事实上我们也鼓励这么做，因为兑换 token 的接口会被限流。

## 如何获取 token

以下的 `bash` 脚本可用于获取 token，其中用到的命令均为 Ubuntu 16.04 发行版默认自带版本，其依赖3个环境变量：
1. `${RAND_STR}`: 一个随机字符串，不含有 shell 或 json 会引起转义的字符，建议为形如 `[a-zA-Z0-9_]+` 的字符串
2. `${APP_ID}`：开发者用于接入 PTSDK 而注册的 AppID
3. `${SECRET}`：开发者用于接入 PTSDK 而注册的 secret

```bash
#!/bin/sh
NONCE=${RAND_STR}:$(date '+%s')
HASH=$(echo -n "${NONCE}-${SECRET}-pt-${APP_ID}" | md5)
curl "https://ptsdk.llsapp.com/token/${APP_ID}" -d "{\"nonce\": \"${NONCE}\", \"hash\": \"${HASH}\"}"
```

正常情况下会返回如下形式的 json：

```json
{"token": "XXXYYYZZZ", "expires": "1518093909"}
```

其中 `token` 字段的内容即为可用于开启或进行 PT 的 token， `expires` 字段的内容为该 token 的过期时间，时间格式为 *UNIX Epoch time*。

如认证失败则会返回类似如下形式的 json：

```json
{"code":"4004","message":"Auth token is missing or invalid"}
```

请开发者根据自身情况选用任意合适的编程语言/库实现以上 bash 脚本逻辑即可获取 token。

## 客户端接入文档

iOS: [https://github.com/lingochamp/PTSDK-iOS]

Android: [https://github.com/lingochamp/PTSDK-Android]
