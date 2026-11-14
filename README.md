# Loon 简洁版

目标：Loon 3.5.3（1014）。保留 12 个配置段落与必要备注，删除批量 Host 域名映射。

## 方案
- 本机需要解析的域名统一使用阿里、Cloudflare、Google DoH，并发查询，不是主备。国内外不再分别指定 DNS。
- 国内域名/国内 IP/国外规则直接订阅 blackmatrix7；GEOIP,CN 补充国内 IP 判断。
- 自定义规则本地填写；FINAL 代理，代理组不包含 DIRECT 备用。
- 局域网代理访问默认关闭，局域网地址仍按规则直连。
- 脚本、远程脚本、插件、MITM 不配置，不复制第三方证书。

## 使用前
1. 在设备本地添加有效节点，把节点名加入“代理出口”并选中。初始仅有 REJECT，代理请求会失败。
2. 到 Loon DNS 服务器页面关闭加密 DNS 失败回落普通 DNS。本配置不提供普通 DNS，不使用 system 兜底。
3. 确认三份规则和 GeoIP 数据库下载成功。建议规则每天检查、GeoIP 每周检查，更新周期在 App 中设置。
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


