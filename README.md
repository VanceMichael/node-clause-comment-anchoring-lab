# 多稿本征询收件

本地资料交换采用虚构机构、业务编号和文件，目录、原件与接收端响应均为合成数据。服务仅监听本机回环地址。

## 运行环境

使用 Node.js 22。启动前用 `npm ci` 恢复锁定清单，`npm start` 启动监听，`npm test` 检查来源接口及原件。监听端口可用 `PORT` 环境变量指定。申请示例保存于 `request.json`。

网页使用 Chromium。首次在独立环境配置时可运行 `npx playwright install chromium`。网页检查会操作真实 Chromium 中的查询与回执表单。

## 资料交换约定

公告页 `/consultation` 的正文与打印稿分别有稿本码，咨询的窗口以 `window` 半开区间为准，官方对照关系只在 `appliesTo` 的新稿中有效。条文有永久项 ID 和该版可见的章节与段号。批注 `selectors` 中每项的 `range` 是原条文正文 NFC 后的 Unicode 码点起止位置，右端排除；`covers` 可含多个项。责任边界来自条目功能及官方对照中列出的原选区。同一句话的出现不会授予另一责任人签署权。受理页 `/portal/1` 以稿本及条文查询席位，办理附本次编号；正式收件与受理中各用系统实际回执。

选区的 quote 是原文。办事日见 asOf，办公室签认按指定稿本、条文和批注有效。相同 group 的选区属于同一份意见，需要共同表达时按签认决定是否可以整组投件，门户仍按单条分别给回执。signed 时点提供后续签认。

来源入口用 HTTP 返回资料。选择 `first` 或 `change` 可以观察同一批业务材料的两个时点，请向 `PUT /_source/phase` 提交包含 `phase` 的 JSON 对象。启动时默认 `first`。每个监听实例的演练时点及回执相互独立，新进程不继承演练端的状态。

接口路径、请求键及返回材料由 `wire.json` 描述，附属原件在 `materials` 中。路径表列出本地演练内容，不含外部网络服务。对返回文件的地址应领取对应响应，获取时的状态、时间及内容归属以来源声明为准。

| 方法和路径 | 查询字段 | 录入字段 |
| --- | --- | --- |
| `GET /office/signatures` | 无 | 无 |
| `GET /consultation` | 无 | 无 |
| `GET /print` | 无 | 无 |
| `GET /compare` | 无 | 无 |
| `GET /surveys` | `draft` | 无 |
| `POST /surveys/file` | 无 | `clause`、`draft`、`requestId`、`role` |

网页 `/portal/1` 用于公开征求意见席位。

回执可以按客户端办理号查询 `GET /receipts?requestId=…`。有时源端接受后会在返回前断开，查询仍能看到真实演练回执。
