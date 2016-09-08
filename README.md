## 格式说明
以下两种方式 BearyChat 都可以接受：

1. 发送一个 JSON 字符串做为 POST 请求的 payload 参数
2. 发送一个 JSON 字符串做为 POST 请求的 body, Content-Type 为 application/json

### JSON 格式要求

 - 必须有 `text` 字段: `{"text": "Hello world"}`
 - `markdown` 为可选字段，用于控制 `text` 字段是否进行 `markdown` 解析，默认为 `true`
示例：

```json
{
    "text": "text, this field may accept markdown",
    "markdown": true,
    "channel": "bearychat-dev",
    "attachments": [
        {
            "title": "title_1",
            "text": "attachment_text",
            "color": "#ffa500",
            "images": [
                {"url": "http://img3.douban.com/icon/ul15067564-30.jpg"}
            ]
        }
    ]
}
```

z## 字段含义

### 顶层字段

`text`. 必须字段。支持 inline md 的文本内容，不能超过 4K 字节，超过部分会被截断。

`notification`. 可选字段。用于控制消息提醒的内容显示。

`markdown`. 可选字段。用于控制 text 是否解析为 markdown，默认为 true。

`channel`. 可选字段，讨论组名称。如果有该字段并且该讨论组对于机器人创建者可见，消息会发送到指定讨论组中。

`user`. 可选字段，用户名。在没有指定 channel 的情况下，如果有该字段并且该团队中有对应用户名的成员，消息会发送到该成员和 BearyBot 的私聊会话中。

`attachments`. 可选字段，一系列附件。

### attachments
`title`. 可选。

`text`. 可选，内容不能超过 4K 字节，超过部分会被截断。

`color`. 可选，用于控制 attachment 在排版时左侧的竖线分隔符颜色。

`title` 和 `text` 字段必须有一个。

`images`. 可选。用于在推送中推送图片，可以最多同时推送3个图片。使用这个字段需要注意，服务器在收到带images的请求时会主动抓取一次图片内容并缓存，这个过程会比较慢，可能造成请求响应时间增加。另外如果两次推送的图片地址都一样，那么第二次的响应时间会显著降低，因为服务器会对请求进行缓存至少一天，所以如果需要不同的图片请使用不同地址。

## 测试你的 Webhook

通过对 Webhook url 发送请求

1. 可以直接往 url post 一个 json 数据
2. 可以往 url post 一个 form，对应字段是 payload，该字段的值应该是一个序列化之后的 json 字符串
代码示例

```bash
curl 'https://hook.bearychat.com/=bw7z6/incoming/20c2611233b6aa2e28e34b8703a58bbe' \
  -X POST \
  -d 'payload={"text":"愿原力与你同在"}'
```

![仅 text 字段](https://auto601.bearychat.com/tutorials/image/incoming_r2d2_1.png)

```bash
curl 'https://hook.bearychat.com/=bw7z6/incoming/20c2611233b6aa2e28e34b8703a58bbe' \
  -H 'Content-Type: application/json' \
  -d '
{
  "text": "愿原力与你同在",
  "attachments": [
    {
      "title": "Star Wars III",
      "text": "Return of the Jedi",
      "color": "#ffa500"
    }
  ]
}'
```

![title, text & color](https://auto601.bearychat.com/tutorials/image/incoming_r2d2_2.png)

```bash
curl 'https://hook.bearychat.com/=bw7z6/incoming/20c2611233b6aa2e28e34b8703a58bbe' \
  -H 'Content-Type: application/json' \
  -d '
{
  "text": "a quote a day keeps the doctor away",
  "attachments": [
    {
      "title": "Professor John Frink:",
      "text": "Numbers are fun. ",
      "color": "#ffa500",
      "images": [{"url":"http://img3.douban.com/icon/ul15067564-30.jpg"}]
    }
  ]
}'
```

![image](https://auto601.bearychat.com/tutorials/image/incoming_prof_frink.png)