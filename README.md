# Loon 简洁版

目标：Loon 3.5.3（1014）。保留 12 个配置段落与必要备注，删除批量 Host 域名映射。

## 方案
- 本机需要解析的域名统一使用阿里、Cloudflare、Google DoH，并发查询，不是主备。国内外不再分别指定 DNS。
- 国内域名、国内 IP、苹果（含 iCloud）、微软、谷歌规则直接订阅 blackmatrix7；苹果/iCloud/微软直连，谷歌代理。已移除国外综合规则，其他未命中流量由 FINAL 代理。GEOIP,CN 补充国内 IP 判断。
- 自定义规则本地填写；国内/局域网/苹果/iCloud/微软分配到“国内直连”（DIRECT）；谷歌分配到“全球代理”，FINAL 分配到“漏网之鱼”（使用全球代理）。
- 局域网代理访问默认关闭，局域网地址仍按规则直连。
- 脚本、远程脚本、插件、MITM 不配置，不复制第三方证书。

## 使用前
1. 在设备本地添加自建节点和订阅。名称过滤器自动纳入匹配节点，“全球代理”组每 600 秒测速选择节点。不要添加不可信订阅；名称过滤不保证排除全部通知/回国节点。无可用节点时没有 DIRECT 备用。
2. 到 Loon DNS 服务器页面关闭加密 DNS 失败回落普通 DNS。本配置不提供普通 DNS，不使用 system 兜底。
3. 确认八份规则和 GeoIP 数据库下载成功。建议规则每天检查、GeoIP 每周检查，更新周期在 App 中设置。
4. 不要把含节点密码、订阅 token、私钥、证书的设备完整配置上传公开仓库。

## 安全边界
DoH 保护传输，不隐藏 DNS 查询对解析服务商的可见性；国内 DNS 可能收到国外域名查询。部分代理连接可能在服务端解析，使用哪家 DNS 取决于节点。
三家在同一全局列表并发查询，采用先返回的有效结果；不是国内阿里、国外 CF/Google 的分流，也不是顺序回落。国内外查询均可能发给三家，内部 DoH 是否经过代理尚未验证。DNS 解析与连接直连/代理独立。
GEOIP 与 ChinaIPs 任一可允许直连，扩大覆盖也继承误分类风险。本地 GEOIP 来源优先级高于订阅，不能视为远程 IP 后的顺序兜底。
hijack-dns 不能拦截所有应用内 DoH/DoT；IPv6、WebRTC、系统旁路需设备实测。代理不可用时配置拒绝请求，但 Loon 关闭或崩溃不构成系统级断网保护。
未进行 iPhone 导入及抓包验收。应在 Wi-Fi、蜂窝测试实际路由，并阻断 DoH、断开代理验证不会回落明文或直连。
仓库：https://github.com/likmm/loon
配置下载：https://raw.githubusercontent.com/likmm/loon/main/loon.conf

参考：https://nsloon.app/docs/DNS/ 、https://nsloon.app/docs/Rule/





日常只更新远程规则与节点订阅。完整重新导入主配置仍可能覆盖本地节点/订阅，自动筛选不能恢复被覆盖的凭证，请先备份。自动切换可能改变出口 IP。


远程源按国内域名、国内IP、苹果、微软、谷歌排列。苹果集合已覆盖 iCloud 核心域名，不再单列 iCloud。Loon 仍按域名/IP 类型及来源优先级匹配，不保证仅按显示顺序；重叠域名需查看实际命中。


苹果分类必须同时加载 Apple_Domain.list（常规域名）与 Apple.list（其余规则），两者均分配国内直连。修复 iCloud 域名因缺少配套列表而落入 FINAL 的问题。


## 服务覆盖核对（2026-11-14）
- 苹果必须配套 Apple_Domain.list + Apple.list；包括 App Store、系统更新、Apple Music/TV、Siri、Find My、iCloud 等上游子规则。iCloud 核心域名 icloud.com、icloud-content.com、apple-cloudkit.com、me.com 已检查。
- Microsoft.list 可单独使用，包含 Edge、OneDrive、Teams，检查到 Office、Outlook、Bing、Live 等核心域名；GitHub 被微软规则排除，现使用独立 GitHub.list 明确分配全球代理。直连不保证所有地区服务可用。
- Google.list 可单独使用，包含 Search、Drive、Voice 及 Gmail/API/静态资源域名，明确排除 YouTube/YouTube Music；YouTube 保留专用代理规则；YouTube Music 专用规则已移除，不新增策略组。
- 规则覆盖不等于所有实际请求均已验收，服务共享 CDN、上游重叠与应用内解析仍需查看日志。没有为了凑齐 China 全套而加入 China.list 的宽泛关键词规则，国内当前使用纯域名集合加 ChinaIPs/GEOIP。


GitHub.list 分配全球代理，覆盖 github.com（含 API）、githubusercontent.com（含 Raw/下载）、githubassets.com、github.io、github.dev、githubcopilot.com、ghcr.io，以及上游收录的 npmjs.com/npmjs.org 等。移除音乐专用订阅不代表将音乐直连，未命中其他规则时仍由 FINAL 代理。


策略组共三个：国内直连、全球代理、漏网之鱼。FINAL 固定语法保留，目标为漏网之鱼，其唯一选项是全球代理，不提供直连备用。

