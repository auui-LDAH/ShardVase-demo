# ShardVase demo — hashcalc 演示包

> 演示包：受保护的真实小工具（hashcalc）。下载 Releases 里的 zip，
> 解压后按本文件运行。

> ### ⚠️ 先读这一条：本包**需要你申请一次授权**才能跑通
>
> 包内的 `license_eval.txt` 是**签发时的样例**，它绑定了**签发机**的
> 机器码、且签发时间已过。**在你的机器上首次运行会被拒绝** —— 这不是
> 包坏了，而是机器绑定机制**按设计工作**（这正是你要评估的能力之一）。
>
> **三步跑通**：
> ```
> 1. python req_gen.py --name 你的称呼      # 生成 .req（含你的机器码）
> 2. 开 Issue 附上 .req                     # 或用邮件
> 3. 收到 license.txt 后替换包内文件，再运行
> ```
> 详细说明见下方「**怎么跑通（必读）**」。
>
> 想**跳过申请、立刻看效果**？直接看下方「**不申请也能验证的部分**」——
> 那些防护行为无需有效授权即可观察。

能力与边界：[ShardVase-docs](https://github.com/auui-LDAH/ShardVase-docs)

---

## 本包内容

```
hashcalc.exe       受保护的可执行文件（机器绑定 + 分级授权 + VM 保护链）
vm.blob            VM 镜像包（服务器签名，防替换）
license_eval.txt   评估授权**样例**（绑定签发机，已过期 —— 见文首说明）
req_gen.py         申请授权工具（生成含你机器码的 .req）
```

系统要求：Windows x64。

## 怎么跑通（必读）

1. 生成授权申请（含你的机器码）：
   ```
   python req_gen.py --name 你的称呼
   ```
2. 开 [Issue：评估申请](https://github.com/auui-LDAH/ShardVase-docs/issues/new?template=eval.yml)
   附上生成的 `.req` 文件（或按 Issue 模板提供机器码）；
3. 我们会签发与你机器绑定的 **14 天评估授权** 回传；
4. 把收到的 `license.txt` 放入本目录，然后：

   ```
   hashcalc.exe --license-file license.txt --vm-blob vm.blob
   ```

5. 输入序列号：`DEMO-2026-ABCDEFGH`

   应看到：
   ```
   license: 1 (tier=1 expires=17xxxxxx)
   == 批量哈希校验工具 (正式版) ==
   机器绑定: 0x.... 授权: Tier1
     hashcalc.exe: <64位sha256>
   ```

> **为什么不能预置一个"通用"授权？** 因为机器绑定就写在 RSA 签名载荷里 ——
> 一个能通过所有人机器的授权，等于没有机器绑定。这条限制本身就是本产品
> 的核心能力（也是**边界 B1/B2** 的来源，见边界清单）。

## 不申请也能验证的部分

以下行为**无需有效授权**即可观察（均为实测结果），用来验证"保护是真实的"：

| 尝试 | 实测结果 | 说明 |
|------|---------|------|
| 直接运行 `hashcalc.exe`（不带授权文件） | `序列号无效。` exit=1 | 授权门存在，业务不执行 |
| 修改 `license_eval.txt` 任意一个字符 | `license: 0` -> `序列号无效。` exit=1 | 全字段参与 RSA-3072 签名 |
| 用伪造/修改过的 `vm.blob` | VM 镜像装载失败 | blob 与授权同私钥签名 |
| 用调试器附加 | 反调试多维检测 | 12 项检测（关键 7 + 咨询 5）|
| 反汇编 `hashcalc.exe` 找授权判定 | 判定链在 VM 字节码中 | 47 条自定义指令集 |

> 注：`license: 0` 这行只在**传了 `--license-file`** 时打印（授权校验被调用）。
> 不带该参数时校验直接跳过、由序列号校验拦截 —— 两者都**拒绝运行**，
> 只是日志不同。

拿到有效授权后还可验证：**把整个包拷到另一台机器 → 拒绝**（机器绑定生效）。

**复现命令**（把两个文件放在同一目录）：
```
rem 1. 不带授权 -> 拒绝
hashcalc.exe --vm-blob vm.blob

rem 2. 篡改授权 -> 拒绝（改 TIER=1 为 TIER=2 后）
hashcalc.exe --license-file license_eval.txt --vm-blob vm.blob
```

## 功能与分级
| 授权 | 功能 |
|------|------|
| Tier 1（评估授权）| 计算自身 SHA-256（示例业务逻辑）|
| Tier 2（高级，演示用）| 递归目录扫描 + 汇总报告（可向 ShardVase 申请演示授权）|

## 诚实说明
- 本文档首段即说明"首次运行会被拒" —— 这是**设计**，不是缺陷；
- 授权有效期从**签发时刻**起算（不是下载时刻），请及时申请；
- 这是"评估"而非"试用破解挑战"——但欢迎你试破坏它；
- 本工具业务逻辑简单（演示用），保护链条与正式版一致；
- 完整边界清单（含 B1 安装目录可写、B2 本地时钟）见
  [诚实边界](https://github.com/auui-LDAH/ShardVase-docs/blob/main/boundaries.md)。

（企业/开发者若希望为 **你自己的软件** 接入这套保护，见
[接入咨询](https://github.com/auui-LDAH/ShardVase-docs/issues/new?template=consult.yml)。）
