# FlowUs 上线说明

这个项目是静态网页 App，可以部署到 Vercel、Netlify 或 GitHub Pages，然后把部署后的 HTTPS 链接嵌入 FlowUs。

## 当前激活方式

App 首屏会要求输入激活码。激活成功后，浏览器会把激活状态保存到本地。

当前内置测试码：

- `YBY-MEET-PREP-2026`
- `YBY-FOUNDERS-2026`

注意：前端内置激活码只能挡住普通用户，不能当成严格防盗版方案。正式卖课或卖模板时，建议把 `app.js` 里的 `LICENSE_VERIFY_ENDPOINT` 改成你的服务器接口，由服务器校验支付状态、激活码、会员期限和设备绑定。

## 增加本地激活码

如果暂时不接服务器，可以先用 Node 生成新激活码的 SHA-256：

```powershell
node -e "console.log(require('crypto').createHash('sha256').update('你的新激活码').digest('hex'))"
```

把输出结果加入 `app.js` 顶部的 `LOCAL_LICENSE_HASHES` 数组即可。不要把明文激活码写进页面文案里。

## FlowUs 使用方式

1. 把本项目部署到一个 HTTPS 地址。
2. 在 FlowUs 里创建付费模板或付费页面。
3. 页面里放“使用入口”，嵌入部署后的 App 链接。
4. 用户支付后，在 FlowUs 页面中看到激活码或领取激活码。
5. 用户在嵌入的 App 中输入激活码并开始生成计划。

## 推荐部署

最简单方式：

1. 打开 Netlify。
2. 新建站点。
3. 上传这个文件夹。
4. 获得类似 `https://your-app.netlify.app` 的链接。
5. 把链接嵌入 FlowUs。

项目不需要后端即可运行，但数据会保存在用户当前浏览器中。跨设备同步需要后续接数据库或继续使用 JSON 备份/恢复。

## 以后接正式支付

需要新增一个服务器接口，例如：

```txt
POST /api/license/verify
{
  "code": "USER-CODE"
}
```

返回：

```json
{
  "active": true,
  "licenseId": "license_xxx",
  "message": "ok"
}
```

然后把 `app.js` 顶部的：

```js
const LICENSE_VERIFY_ENDPOINT = "";
```

改成你的接口地址。
