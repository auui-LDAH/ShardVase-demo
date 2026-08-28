# ShardVase demo

> 演示包：受保护的真实小工具（hashcalc）。下载 Releases 里的 zip，解压后按包内 README 运行（含 7 天评估授权，可申请续期）。

能力与边界：[ShardVase-docs](https://github.com/auui-LDAH/ShardVase-docs)

---

# hashcalc 演示包（受保护的真实工具）

> ShardVase 的公开演示：一个被完整保护的真实小工具，含 **7 天评估授权**。
> 本包内容：
> ```
> hashcalc.exe       受保护的可执行文件（机器绑定 + 分级授权 + VM 保护链）
> vm.blob            VM 镜像包（服务器签名，防替换）
> license_eval.txt   7 天评估授权（绑定当前机器）
> req_gen.py         重新申请授权的工具（到期或换机时用）
> ```
> 系统要求：Windows x64。

## 运行
```
hashcalc.exe --license-file license_eval.txt --vm-blob vm.blob
```
输入序列号：`DEMO-2026-ABCDEFGH`（评估用固定序列号）

应看到：
```
license: 1 (tier=1 expires=17xxxxxx)
== 批量哈希校验工具 (正式版) ==
机器绑定: 0x.... 授权: Tier1
  hashcalc.exe: <64位sha256>
```

## 功能与分级
| 授权 | 功能 |
|------|------|
| Tier 1（本包）| 计算自身 SHA-256（示例业务逻辑）|
| Tier 2（高级，演示用）| 递归目录扫描 + 汇总报告（可向 ShardVase 申请演示授权）|

## 请亲手验证（保护是真实的）
| 尝试 | 预期结果 |
|------|---------|
| 修改 license_eval.txt 任意字段 | `license: 0`，拒绝运行 |
| 把本包拷到另一台 Windows 机器运行 | 拒绝（机器绑定在签名载荷内）|
| 用伪造/修改过的 vm.blob | VM 镜像装载失败（签名强制）|
| 到期后运行（可把系统时间调过 expires 体验）| 拒绝（评估到期）|
| 用调试器附加 | 反调试检测（可咨询详细行为）|

## 到期了怎么办（这是授权流程的一部分）
1. 运行 `python req_gen.py --name 你的称呼` → 生成 `.req` 文件（含机器码）；
2. 开 GitHub Issue（评估申请）附上 `.req`，或发邮件；
3. 我们会签发与你的机器绑定的 **14 天评估授权** 回传；
4. 放入本目录，替换 `license_eval.txt` 后运行。

（企业/开发者若希望为 **你自己的软件** 接入这套保护，见 首页/接入咨询。）

## 诚实说明
- 评估到期略早于你预期？expires 是从签发时刻 +7 天，请及时申请续期；
- 这是"评估"而非"试用破解挑战"——但欢迎你试破坏它，边界见首页；
- 本工具业务逻辑简单（演示用），保护链条与正式版一致。