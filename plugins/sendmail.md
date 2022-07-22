## 服务说明

**请通过市场安装, 详细文档请参考市场最新版本**

发送邮件服务. 可以快速调用 tars 接口完成邮件的发送.

- 支持发送 html/text 格式
- 支持发送 markdown 格式, 会自动转成 html, 且可以指定样式

## 配置说明

配置文件名为`config.json`, 格式如下:

```json
{
  "email": {
    "smtp": {
      "host": "smtp.exmail.qq.com",
      "port": 465,
      "secure": true,
      "auth": {
        "user": "",
        "pass": ""
      }
    }
  }
}
```

需要配置好 smtp 中字段即可.

## 使用说明

接口如下:

```c++


struct SendInfo
{
    1 optional vector<string> to;
    2 optional vector<string> cc;
    3 optional vector<string> bcc;
    4 optional string subject;

};

struct ContentInfo
{
    1 optional string text;
    2 optional string html;
};

struct MarkdownInfo
{
    1 optional string markdown;
    2 optional string css = "github.css";
};

interface SendEmail
{
    int sendEmail(SendInfo send, ContentInfo info);

    int sendEmailMarkdown(SendInfo send, MarkdownInfo info);

};

```

说明:

- 使用 sendEmail 发送邮件, 同时填写 text, html, 如果对方支持网页则显示 html, 否则显示 text
- 使用 sendEmailMarkdown, 内容使用 markdown 格式, 会自动转换成网页, 样式默认是用`github.css`, 当然你也可以自定义样式, 在服务的配置中添加样式文件, 发送时指定文件名即可
