# 游戏领航师 · Game Navigator

### 带上你常用的 AI 工具，走进下一段冒险。

找路、规划装备与技能、理解难选的分支，也记住那些值得以后回来的地方。
领航师让兼容的 AI 工具连接你在 Windows 电脑或掌机上玩的已支持游戏。

[English](README.md) · [看看已支持的游戏](https://gamenavigator.raycraftlab.com/?utm_source=github&utm_medium=repository&utm_campaign=skill-launch&utm_content=readme-zh)

![从提问到结合游戏情境的领航](assets/journey.svg)

## 开始使用

把下面这段话复制给电脑上正在使用的 **WorkBuddy、Codex、Cursor 或 Claude Code**：

```text
请帮我安装官方游戏领航师 Skill 和领航端。
Windows 使用 https://gamenavigator.raycraftlab.com/install.ps1，
macOS 或 Linux 使用 https://gamenavigator.raycraftlab.com/install.sh。
请先检查安装脚本再执行，然后引导我登录并连接玩游戏的电脑。
启用任何游戏的试用前，请先问我。
```

AI 工具需要支持 Skill，并能在本机执行安装。如果它不能安装软件，先打开
[产品页](https://gamenavigator.raycraftlab.com/?utm_source=github&utm_medium=repository&utm_campaign=skill-launch&utm_content=install-zh)，按页面引导开始。
你不需要自己把命令搬到另一台掌机的终端里执行。

如果你熟悉从 GitHub 直接安装 Skill，目录是 [`skills/game-navigator`](skills/game-navigator)。
仅装 Skill 不等于装好程序，首次使用时它会继续引导安装官方领航端。

登录后，告诉领航师游戏是在这台 Windows 电脑上，还是另一台 Windows 设备上。
另一台设备会使用可点击的安装器和短配对码，通常两台设备需要在同一家庭网络。
AI 工具所在的领航端支持 Windows、macOS 和受支持的 Linux；游戏端目前运行于 Windows。

## 直接这样问

- “接下来该往哪里走？还有什么没探索到？”
- “刚拿到这件装备，适合谁？技能和队伍需要调整吗？”
- “这几个选择有什么区别？先给我一点提示，别剧透。”
- “记住这个打不开的箱子，拿到需要的东西后帮我想起回来。”

这是提问示例，不代表每款游戏都具备相同能力。可获取的信息取决于游戏和版本：
有的来自当前画面，有的来自存档，有的需要单独同意安装经过审查的观察组件。
[每款游戏当前能帮什么，以产品页为准](https://gamenavigator.raycraftlab.com/?utm_source=github&utm_medium=repository&utm_campaign=skill-launch&utm_content=capabilities-zh)。

## 免费 Skill，不等于免费开放全部功能

**这里的 Skill 开源；核心程序和商业服务仍然闭源。**
符合条件的账号最多可以选择 3 款不同游戏，每款从领取时开始独立试用 5 天。
每次启用试用都需要你确认，删除重装不会重新计算。
之后可通过订阅使用已支持目录，或在开放购买时单独购买某款游戏的永久权限。
已有权益的用户直接使用，不因更换安装入口重新试用或购买。
具体资格、购买开放状态和权益由服务端判断。你使用的 AI 工具及其费用另行计算。

## 隐私与安全

游戏存档、画面和本地旅途记忆不会上传到游戏领航师服务端。
你选择的 AI 工具可能处理交给它的信息，具体以该工具的政策为准。
自动共创信息收集默认关闭，只有你同意才开启。领航师不代玩、不修改内存、不绕过反作弊；
可选观察组件会单独解释用途与影响，再由你决定。

[隐私说明](https://gamenavigator.raycraftlab.com/privacy?lang=zh-CN) · [服务条款](https://gamenavigator.raycraftlab.com/terms?lang=zh-CN)

## 反馈与更新

遇到问题，可以直接告诉已安装的领航师，或使用[官方联系页](https://gamenavigator.raycraftlab.com/contact?lang=zh-CN)。
GitHub Issues 是公开的，请不要提交存档、含个人信息的截图、密码或付款信息。
想增加游戏，也可以直接在产品页表达想玩的游戏。

本仓库同步维护中的用户 Skill，不包含三端程序源码、完整游戏数据或项目维护工具。
官方程序的下载和更新仍由产品服务提供。

## 许可范围

**本仓库**中的 Skill 和原创说明采用 [MIT 许可](LICENSE)。
它不覆盖闭源程序、服务、受保护的游戏包内容和第三方游戏素材，也不授予订阅、试用或商标使用权。
游戏领航师是独立产品，与 GitHub、Steam、所列 AI 工具和游戏没有官方隶属关系。
