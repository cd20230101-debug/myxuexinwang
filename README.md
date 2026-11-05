# 番茄小说去广告（Loon 插件）

只对 **番茄小说（`com.dragon.read`）** 生效的广告拦截插件。抖音、今日头条、西瓜视频、飞书等其他头条系 App 的请求不会被改写。

- 插件文件：[`fanqie-novel-adblock.plugin`](./fanqie-novel-adblock.plugin)
- 安装地址：`https://raw.githubusercontent.com/cd20230101-debug/myxuexinwang/main/fanqie-novel-adblock.plugin`

## 它是怎么做到「只对番茄生效」的

番茄小说发出的请求，User-Agent 里带 `com.dragon.read`（Android 形如
`com.dragon.read/71332 (...; tt-ok/3.12.13.20)`，iOS 同理）。插件里每一条
Rewrite 都先判断 UA：

```
request if ${request.header['User-Agent']} ~= /(com\.dragon\.read|...)/i && ${url} ~= ... then reject(204)
```

UA 不是番茄小说时，规则直接不命中，请求原样发出去。抖音、今日头条、飞书
的 UA 不含 `com.dragon.read`，所以不会被拦、也不会被改包。

作为对比，很多公开的番茄去广告规则是直接 `DOMAIN,...,REJECT`，会把
`i.snssdk.com`、`zijieapi`、`bytedance.com` 这类共享域名整段掐掉，抖音、
飞书跟着遭殃（参见 blackmatrix7/ios_rule_script#55）。本插件没有用任何
无条件的域名拦截规则。

## 覆盖的广告

| 类型 | 说明 |
| --- | --- |
| 穿山甲 / Pangle | 开屏、信息流、章末、激励视频的广告拉取与统计 |
| 巨量 Gromore | 聚合广告下发 |
| 广告安装包 | 下载类广告的 APK 下载 |
| 广告素材 | 广告图片、视频、渲染模板 CDN |
| 其他字节广告 SDK 域名 | `byteorge.com`、`bytegoofy.com` 等 |

## 安装

1. 打开 Loon → 配置 → 插件 → 右上角 `+`。
2. 粘贴安装地址：
   `https://raw.githubusercontent.com/cd20230101-debug/myxuexinwang/main/fanqie-novel-adblock.plugin`
3. 启用插件，确认 Loon 的 MITM 证书已安装并信任。
4. 完全退出番茄小说再打开。

> 需要 Loon 3.5.1 (978) 及以上（使用了新版 Rewrite 条件语法）。

## 新增广告怎么加

在 `[Rewrite]` 里复制一行，把域名换掉即可，UA 条件保持不变：

```
request if ${request.header['User-Agent']} ~= /(com\.dragon\.read|fanqienovel|novelapp|fqnovel)/i && ${url} ~= /^https?:\/\/[^\/]*新域名[^\/]*\//i then reject(204)
```

同时把 `新域名` 加进 `[MITM]` 的 `hostname`，否则 HTTPS 请求看不到，规则不会生效。

## 已知限制

- 只拦「广告请求」。如果番茄把广告内容直接塞在正文/书城接口的 JSON 里，
  需要按字段改写响应，本插件没有做（不同版本字段会变，容易误伤正文）。
- 共享 CDN（`*.pstatp.com`、`*.snssdk.com`）上的广告图片默认不拦，因为
  MITM 这些域名会波及抖音等 App 的图片/视频。需要时打开文件里的「可选增强」。
- 如果番茄某个版本把广告请求的 UA 改成不含 `com.dragon.read` 的通用 UA，
  对应请求不会被拦。以「不影响其他 App」为优先，这里选择宁可漏、不误伤。

## 免责声明

仅供学习与个人使用。请遵守番茄小说的用户协议与相关法律法规。
