# 使用云函数搭建公众号后台

## Prerequisites

1. 一个 NodeJs 云函数，API 网关需启用集成响应 [网址](https://console.cloud.tencent.com/scf/index)
2. 一个微信公众号 [网址](https://mp.weixin.qq.com/)

## 公众号接入

云函数默认返回 Json，返回纯文本时需利用集成响应来指定格式。

```json
{
    "isBase64Encoded": false,
    "statusCode": 200,
    "headers": {"Content-Type":"plain/text"},
    "body": event.queryString.echostr
}
```

## 后台

```JavaScript
'use strict';

const Koa = require('koa');
const serverless = require('serverless-http');
const wechat = require('co-wechat');

const config = {
    token: 'token',
    appid: 'appid',
    encodingAESKey: 'encodingAESKey'
};

const app = new Koa();
app.use(
    wechat(config, true).middleware(async(message, ctx) => {
        if (message.Content === '1') {
            return '2'
        } else {
            return '1'
        }
        // your code
    })
);

const handler = serverless(app);

exports.main_handler = async(event, context, callback) => {
    console.log("%j", event);
    return await handler({...event, queryStringParameters: event.queryString },
        context
    );
};
```

## 坑

### 插件
vscode 插件：Tencent Serverless Toolkit for VS Code v2.0.5

本地创建的云函数打包的包不完整，`package.json` 和 `node_modules` 没打包进去。

解决：

1. 上传后手动创建，再处理依赖；

2. 直接使用默认创建时的代码（未测试）
