# OpenClaw 离线电脑操作说明

适用场景：`Windows` + `不能联网` + `使用本地 Ollama 模型`

这份说明是给现场操作人员使用的。默认假设操作人员不是程序员，所以会把每一步尽量写清楚。

---

## 1. 你要先知道的事情

这套离线使用方式的核心思路是：

1. 目标电脑上已经提前安装好 `Ollama`
2. 目标电脑上已经提前准备好至少一个本地模型
3. `OpenClaw` 不走联网 OAuth 向导
4. 通过手工写配置文件，让 `OpenClaw` 直接连接本机的 `Ollama`

请记住一句话：

> 先启动 `Ollama`，再启动 `OpenClaw`

---

## 2. 开始前必须准备好的东西

在操作之前，请确认离线电脑上已经有下面这些内容。

### 2.1 OpenClaw 离线包文件夹

你应该已经拿到一个解压好的 OpenClaw 文件夹。

例如：

```text
D:\openclaw-offline-package\
```

这个文件夹里至少应该看到这些文件：

- `01_首次配置.bat`
- `02_启动服务.bat`
- `start.bat`
- `nodejs\`
- `node_modules\`
- `examples\`

### 2.2 Ollama 已安装

目标电脑上必须已经安装好了 `Ollama`。

### 2.3 Ollama 模型已提前准备好

因为目标电脑不能联网，所以模型不能临时下载。

也就是说，在你真正开始操作之前，这台电脑里就必须已经存在至少一个 Ollama 本地模型。

例如：

- `qwen2.5:14b`
- `llama3.1:8b`
- `mistral:7b`

实际名字以这台电脑上 `ollama list` 显示的结果为准。

---

## 3. 需要用到的两个重要文件路径

### 3.1 OpenClaw 示例配置文件

这个文件在 OpenClaw 离线包目录中：

```text
OpenClaw离线包目录\examples\openclaw.ollama-local.example.json5
```

例如：

```text
D:\openclaw-offline-package\examples\openclaw.ollama-local.example.json5
```

### 3.2 OpenClaw 真正使用的配置文件

这个文件不在 OpenClaw 安装目录里，而是在当前 Windows 用户目录里。

正确路径是：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

通常会对应成类似下面这种真实路径：

```text
C:\Users\你的用户名\.openclaw\openclaw.json
```

后面你真正要修改的是这个文件，不是示例文件。

---

## 4. 第一步：确认 Ollama 是否可用

### 4.1 打开命令提示符

任选一种方法：

#### 方法 A

1. 点击 Windows 开始菜单
2. 输入 `cmd`
3. 点击“命令提示符”

#### 方法 B

1. 按键盘 `Win + R`
2. 输入 `cmd`
3. 按回车

### 4.2 检查 Ollama 是否安装成功

在黑色窗口里输入：

```cmd
ollama --version
```

如果能显示版本号，说明 Ollama 已安装。

如果提示：

```text
'ollama' 不是内部或外部命令
```

说明这台电脑上的 Ollama 没装好，或者没有加到系统路径中，需要先修复 Ollama。

### 4.3 检查本地模型是否已经存在

输入：

```cmd
ollama list
```

你应该能看到一张模型列表。

请记住你准备使用的模型名称，后面要写到配置文件里。

例如你看到：

```text
qwen2.5:14b
```

那后面就要写成：

```text
ollama/qwen2.5:14b
```

如果 `ollama list` 没有任何模型，或者你想用的模型不在列表里，那么当前这台离线电脑还不能正常使用 OpenClaw。

---

## 5. 第二步：设置 Ollama 环境变量

这一步只需要做一次。

在命令提示符里输入：

```cmd
setx OLLAMA_API_KEY ollama-local
```

说明：

- 这里不需要真实 API Key
- 对本地 Ollama 来说，只要是非空字符串即可
- `ollama-local` 只是推荐值

### 5.1 这一步完成后要做什么

执行完 `setx` 之后：

1. 关闭当前命令提示符窗口
2. 重新打开一个新的命令提示符窗口

这样最稳妥，因为新窗口一定能读取到刚刚设置好的环境变量。

---

## 6. 第三步：创建 OpenClaw 配置文件

这一步最重要。

离线 + Ollama 本地模型模式下，不建议依赖联网向导。最稳妥的做法是手工创建配置文件。

### 6.1 打开当前用户目录

方法如下：

1. 打开任意一个文件夹
2. 点击顶部地址栏
3. 输入：

```text
%USERPROFILE%
```

4. 按回车

这时会跳转到你的用户目录。

例如：

```text
C:\Users\张三\
```

### 6.2 创建 `.openclaw` 文件夹

在这个用户目录里：

1. 右键空白处
2. 选择“新建” -> “文件夹”
3. 文件夹名字输入：

```text
.openclaw
```

注意：

- 前面必须有一个英文句点 `.`
- 不要写成 `openclaw`
- 不要写成 `_openclaw`

### 6.3 创建 `openclaw.json`

进入刚刚创建的：

```text
C:\Users\你的用户名\.openclaw\
```

然后用记事本创建一个文件，文件名必须是：

```text
openclaw.json
```

如果你是通过“另存为”创建文件，请注意：

- 文件名一定要写 `openclaw.json`
- “保存类型”请选择“所有文件”
- 编码建议选择 `UTF-8`

### 6.4 从示例文件复制内容

打开离线包里的这个示例文件：

```text
OpenClaw离线包目录\examples\openclaw.ollama-local.example.json5
```

例如：

```text
D:\openclaw-offline-package\examples\openclaw.ollama-local.example.json5
```

把里面的内容全部复制出来，粘贴到：

```text
C:\Users\你的用户名\.openclaw\openclaw.json
```

### 6.5 必须修改的配置项

打开你刚创建好的：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

找到这一段：

```json5
model: {
  primary: "ollama/REPLACE_WITH_PRIMARY_MODEL",
  fallbacks: [
    // "ollama/REPLACE_WITH_SECOND_MODEL",
    // "ollama/REPLACE_WITH_THIRD_MODEL",
  ],
}
```

#### 必改项：主模型

把：

```text
ollama/REPLACE_WITH_PRIMARY_MODEL
```

改成你实际存在的模型名。

例如，如果 `ollama list` 显示有：

```text
qwen2.5:14b
```

那么你就要改成：

```json5
primary: "ollama/qwen2.5:14b"
```

#### 可选项：备用模型

如果你这台电脑里还有其他模型，也可以把它们写进 `fallbacks`。

例如：

```json5
fallbacks: [
  "ollama/llama3.1:8b",
  "ollama/mistral:7b",
]
```

### 6.6 修改配置时最容易出错的地方

请特别注意下面几点：

1. 模型名必须和 `ollama list` 看到的一模一样
2. `ollama/` 前缀不能漏掉
3. 不要把模型名写到示例文件里然后就结束了
4. 真正生效的文件必须是：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

---

## 7. 第四步：启动 Ollama

在新的命令提示符窗口里输入：

```cmd
ollama serve
```

通常做法是：

1. 执行 `ollama serve`
2. 保持这个窗口不要关闭

只要这个窗口保持运行，Ollama 通常就会一直提供本地模型服务。

### 7.1 如果提示端口已被占用

如果你看到类似“端口已经被占用”的提示，不一定是坏事。

这通常表示：

- Ollama 已经在后台运行

这时你只需要确认 Ollama 是真的在运行即可，例如重新开一个窗口执行：

```cmd
ollama list
```

如果能正常看到模型列表，一般说明 Ollama 是可用的。

---

## 8. 第五步：首次运行 OpenClaw

如果你已经写好了配置文件，首次运行 OpenClaw 的目的主要是：

1. 生成 `openclaw.bat`
2. 把 OpenClaw 目录加入系统 PATH

### 8.1 如何操作

1. 打开 OpenClaw 离线包目录
2. 双击：

```text
01_首次配置.bat
```

### 8.2 这一步不会做什么

当前这个仓库已经改成“手动启动模式”，所以这一步：

- 不会自动安装后台守护
- 不会自动创建 Windows 计划任务
- 不会自动常驻后台运行

### 8.3 如果首次配置时出现很多选项，一般怎么选

有些情况下，运行 `01_首次配置.bat` 后，OpenClaw 仍然会出现一系列交互式选项。

这对普通用户来说往往最容易困惑。

下面这部分就是写给现场操作人员的“推荐选择表”。

原则只有两条：

1. 目标电脑是离线电脑
2. 你已经提前写好了 `%USERPROFILE%\.openclaw\openclaw.json`

在这个前提下，向导里的大多数高级选项都**不要乱改**。

#### 情况 A：如果它让你选 `QuickStart` 还是 `Advanced`

建议：

- 选 `QuickStart`

不要选 `Advanced`，除非你非常明确知道自己要改什么。

原因：

- `QuickStart` 更适合普通用户
- `Advanced` 会出现更多选项，普通用户更容易选错

#### 情况 B：如果它让你选 `Local Gateway` 还是 `Remote Gateway`

建议：

- 选 `Local Gateway`

不要选 `Remote Gateway`。

原因：

- 你现在是在本机运行 OpenClaw
- 不是把 Gateway 放在另一台机器上

#### 情况 C：如果它让你选模型提供商

对你现在这个场景，最好的原则是：

- **不要依赖向导来完成模型配置**
- 以 `%USERPROFILE%\.openclaw\openclaw.json` 为准

如果向导一定要求继续选择，建议：

- 选 `Ollama`
- 如果它继续问本地还是云端，选 `Local`

不要选：

- `Cloud + Local`
- 任何需要网页登录或 OAuth 的方式

原因：

- 目标电脑不能联网
- 你已经手工准备好了本地配置
- 你要避免把普通用户带进云模型登录流程

#### 情况 D：如果它让你输入 Ollama 地址

建议：

- 保持默认

也就是：

```text
http://127.0.0.1:11434
```

只有在一种情况下才需要改：

- Ollama 不是运行在本机
- 而是运行在另一台局域网机器上

如果你不是这种情况，请不要改。

#### 情况 E：如果它让你选择默认模型

建议：

- 如果已经能直接跳过，就跳过
- 如果不能跳过，就选你已经在 `ollama list` 里确认存在的那个模型

例如：

```text
qwen2.5:7b
```

请记住：

- 名字一定要和 `ollama list` 看到的一模一样
- 不要凭感觉乱输

#### 情况 F：如果它让你选择工作区目录

建议：

- 直接使用默认值

不要自己手工改一个奇怪目录。

原因：

- 对普通用户来说，这不是必须理解的内容
- 默认值通常已经够用

#### 情况 G：如果它让你设置 Gateway 端口

建议：

- 保持默认 `18789`

不要改成别的端口，除非你已经明确知道 `18789` 被占用了。

#### 情况 H：如果它让你选 Web Search

建议：

- 先跳过
- 先不配置

原因：

- 这不是离线 Ollama 本地聊天的前提条件
- 普通用户第一次使用时不需要先处理这个问题

#### 情况 I：如果它让你选聊天渠道

例如可能看到：

- Telegram
- Discord
- WhatsApp
- Slack

建议：

- 全部跳过

原因：

- 你当前首先要保证本地网页/本地 Gateway 能正常使用
- 聊天渠道属于后续增强项，不是第一次部署的必需项

#### 情况 J：如果它问要不要安装后台守护、计划任务、daemon

建议：

- 一律选 **不要**
- 一律选 **No**
- 一律选 **Skip**

原因：

- 当前这个离线包已经按“手动启动模式”设计
- 你不希望它自己常驻运行
- 普通用户也不容易理解后台守护的行为

#### 情况 K：如果它让你做健康检查

建议：

- 可以做

如果健康检查失败，也不要慌。

优先检查：

1. `ollama serve` 是否已经启动
2. `ollama list` 是否能看到模型
3. `%USERPROFILE%\.openclaw\openclaw.json` 是否存在
4. 里面的 `primary` 模型名是否写对

#### 情况 L：如果它让你选 Skills

建议：

- 普通用户第一次可以先接受默认值
- 或者先跳过

这不会妨碍你先把 OpenClaw 的本地基本使用跑起来。

### 8.4 普通用户的最简选择规则

如果现场人员不想看那么多，只记住下面这几条就行：

1. 看到 `QuickStart / Advanced`，选 `QuickStart`
2. 看到 `Local / Remote`，选 `Local`
3. 看到 `Ollama / 其他提供商`，选 `Ollama`
4. 看到 `Local / Cloud + Local`，选 `Local`
5. 看到端口，保持默认
6. 看到 Web Search、聊天渠道，先跳过
7. 看到守护/daemon/计划任务，一律不要安装
8. 如果不确定，就尽量使用默认值，不要自定义

---

## 9. 第六步：手动启动 OpenClaw Gateway

OpenClaw 现在是手动启动模式。

每次要用的时候，请手动启动。

### 方法 1：双击启动

1. 打开 OpenClaw 离线包目录
2. 双击：

```text
02_启动服务.bat
```

### 方法 2：命令行启动

1. 打开命令提示符
2. 进入 OpenClaw 离线包目录
3. 输入：

```cmd
start.bat start
```

### 9.1 启动成功后会有什么结果

启动后，Gateway 默认地址是：

```text
http://127.0.0.1:18789
```

---

## 10. 以后怎么切换模型

如果离线电脑里有多个 Ollama 模型，后续可以很方便地切换。

### 10.1 先查看当前可用模型

进入 OpenClaw 离线包目录后，输入：

```cmd
start.bat models list
```

### 10.2 切换默认模型

例如你想切换到：

```text
ollama/llama3.1:8b
```

那么输入：

```cmd
start.bat models set ollama/llama3.1:8b
```

### 10.3 如果不想用命令切换，也可以手工改文件

打开这个文件：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

找到：

```json5
primary: "ollama/当前模型名"
```

把它改成：

```json5
primary: "ollama/你想切换的新模型名"
```

保存后，重新启动 OpenClaw Gateway 即可。

---

## 11. 建议的日常使用顺序

每次开机后，如果你要使用 OpenClaw，建议按下面顺序做：

1. 先确认 `Ollama` 已启动
2. 再启动 `OpenClaw`
3. 如果需要切换模型，先执行模型切换命令
4. 切换后如有需要，再重新启动 OpenClaw

最简单的记法就是：

> 先 Ollama，后 OpenClaw

---

## 12. 手动操作速查

这一节是写给“已经部署完成，平时只想照着做”的用户。

如果你不想回头重新看整份文档，可以直接看这一节。

### 12.1 日常启动 OpenClaw

每次要使用时，推荐按这个顺序来：

#### 第一步：先启动 Ollama

打开命令提示符，输入：

```cmd
ollama serve
```

如果它提示端口已经被占用，通常表示 Ollama 已经在运行。

你可以再开一个命令提示符，输入：

```cmd
ollama list
```

只要能正常看到模型列表，通常就说明 Ollama 是可用的。

#### 第二步：再启动 OpenClaw

打开 OpenClaw 离线包目录，双击：

```text
02_启动服务.bat
```

如果你更喜欢命令行，也可以进入 OpenClaw 目录后执行：

```cmd
start.bat start
```

### 12.2 手动切换模型

#### 先查看模型列表

进入 OpenClaw 离线包目录后，执行：

```cmd
start.bat models list
```

#### 再切换到指定模型

例如：

```cmd
start.bat models set ollama/qwen2.5:7b
```

或者：

```cmd
start.bat models set ollama/llama3.1:8b
```

切换后，如果当前 OpenClaw 已经在运行，建议重新启动一次。

### 12.3 重新配置

如果你想重新走一次配置流程，可以在 OpenClaw 离线包目录执行：

```cmd
start.bat onboard
```

或者双击：

```text
01_首次配置.bat
```

适用场景：

- 你想重新生成 `openclaw.bat`
- 你想重新确认 PATH
- 你想重新过一遍向导

### 12.4 查看健康状态

如果你怀疑 OpenClaw 没配好，可以运行：

```cmd
start.bat doctor
```

如果你需要更详细的运行状态，也可以试：

```cmd
start.bat status --deep
```

### 12.5 最短的手动操作清单

普通用户只要记住这 5 个动作就够了：

1. `ollama serve`
2. `02_启动服务.bat`
3. `start.bat models list`
4. `start.bat models set ollama/<你的模型名>`
5. `start.bat doctor`

---

## 13. 常见问题排查

### 13.1 双击 `02_启动服务.bat` 后窗口一闪而过

处理方法：

1. 不要双击
2. 改为打开命令提示符
3. 进入 OpenClaw 离线包目录
4. 手动执行：

```cmd
start.bat start
```

这样你可以直接看到报错内容。

### 13.2 提示找不到模型

按下面顺序检查：

1. 运行：

```cmd
ollama list
```

2. 看模型名是否真的存在
3. 打开：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

4. 检查 `primary` 里的模型名是否完全一致

### 13.3 提示 `ollama` 不是内部或外部命令

说明：

- Ollama 没安装好
- 或者 Ollama 没加到 PATH

必须先修复 Ollama，OpenClaw 才能继续用。

### 13.4 配置文件写错位置了

OpenClaw 真正读取的是：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

不是下面这些位置：

- `examples\openclaw.ollama-local.example.json5`
- OpenClaw 安装目录根目录
- 桌面
- 下载目录

### 13.5 启动顺序不对

如果你先启动了 OpenClaw，后启动 Ollama，可能会导致 OpenClaw 无法正常连接模型。

最稳妥的做法是：

1. 先运行 `ollama serve`
2. 再运行 `02_启动服务.bat`

---

## 14. 最关键的 5 个位置和命令

请记住下面这 5 个信息。

### 14.1 OpenClaw 离线包目录

例如：

```text
D:\openclaw-offline-package\
```

### 14.2 OpenClaw 配置文件

```text
%USERPROFILE%\.openclaw\openclaw.json
```

### 14.3 查看 Ollama 模型

```cmd
ollama list
```

### 14.4 手动启动 OpenClaw

```text
02_启动服务.bat
```

### 14.5 切换模型

```cmd
start.bat models set ollama/<你的模型名>
```

---

## 15. 现场操作的最短步骤

如果你时间很紧，只看这一段也可以。

### 第一次配置

1. 打开命令提示符
2. 输入：

```cmd
ollama list
```

3. 记住一个你要用的模型名
4. 输入：

```cmd
setx OLLAMA_API_KEY ollama-local
```

5. 关闭命令提示符，再重新打开一个新的
6. 在：

```text
%USERPROFILE%\.openclaw\
```

里面创建：

```text
openclaw.json
```

7. 把：

```text
examples\openclaw.ollama-local.example.json5
```

里的内容复制进去
8. 把 `primary` 改成你的真实模型名，例如：

```json5
primary: "ollama/qwen2.5:14b"
```

9. 输入：

```cmd
ollama serve
```

10. 打开 OpenClaw 离线包目录
11. 双击：

```text
01_首次配置.bat
```

12. 再双击：

```text
02_启动服务.bat
```

### 以后切换模型

1. 打开命令提示符
2. 进入 OpenClaw 离线包目录
3. 输入：

```cmd
start.bat models list
```

4. 再输入：

```cmd
start.bat models set ollama/<新的模型名>
```

---

## 16. 以后如何更新离线电脑上的 OpenClaw 版本

如果你以后在 GitHub 上构建了一个新的离线包版本，离线电脑**不会自动更新**。

必须手动把新版本 ZIP 拷到离线电脑，然后按下面步骤更新。

### 16.1 先记住这条原则

更新时，**不要一上来就覆盖旧目录**。

推荐做法是：

1. 把新版本解压到一个新目录
2. 先测试新目录能不能正常工作
3. 确认没问题后，再决定是否删除旧目录

这样做的好处是：

- 出问题时可以立刻切回旧版本
- 不容易因为覆盖旧文件导致混乱
- 更适合非程序员现场操作

### 16.2 更新前，先知道哪些内容会保留

OpenClaw 的本地用户配置文件在这里：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

通常对应：

```text
C:\Users\你的用户名\.openclaw\openclaw.json
```

这个文件不在 OpenClaw 解压目录里，所以：

- 你更换离线包目录时，这个配置通常会保留
- 你之前写好的主模型、备用模型配置通常也会保留

但即使配置会保留，更新新版本后仍然建议重新执行一次：

- `01_首次配置.bat`
- `02_启动服务.bat`

### 16.3 更新步骤

假设你现在旧版本目录是：

```text
D:\openclaw-offline-package\
```

你新下载的压缩包版本是：

```text
openclaw-offline-package-v2026.6.x.zip
```

推荐按下面做。

#### 步骤 1：把新 ZIP 拷到离线电脑

把新的：

```text
openclaw-offline-package-v2026.6.x.zip
```

复制到离线电脑上。

如果你手头还有 `.sha256` 文件，也建议一起带过去。

#### 步骤 2：解压到一个新目录

不要直接覆盖旧目录。

例如把新版本解压到：

```text
D:\openclaw-offline-package-v2026.6.x\
```

而不是覆盖：

```text
D:\openclaw-offline-package\
```

#### 步骤 3：确认配置文件仍然存在

打开这个位置：

```text
%USERPROFILE%\.openclaw\
```

确认里面还有：

```text
openclaw.json
```

如果这个文件还在，通常就不需要重新手工抄配置。

#### 步骤 4：先启动 Ollama

和第一次安装一样，先保证 Ollama 正常运行：

```cmd
ollama serve
```

如果 Ollama 已经在后台运行，只要确认它可用即可。

可以用下面命令快速确认：

```cmd
ollama list
```

#### 步骤 5：进入新版本目录，运行首次配置

打开新版本目录：

```text
D:\openclaw-offline-package-v2026.6.x\
```

然后双击：

```text
01_首次配置.bat
```

这一步的目的主要是：

- 重新生成 `openclaw.bat`
- 让新目录接管命令入口
- 重新确认 PATH 设置

#### 步骤 6：启动新版本服务

接着双击：

```text
02_启动服务.bat
```

或者在命令行里运行：

```cmd
start.bat start
```

#### 步骤 7：确认新版本能正常工作

至少确认下面几件事：

1. OpenClaw 能正常启动
2. 网页或调用端能连上 `http://127.0.0.1:18789`
3. 模型能正常回复
4. 如果你要切换模型，`start.bat models list` 仍然可用

#### 步骤 8：确认没问题后，再决定是否删除旧版本

如果新版本工作正常，你可以选择：

- 保留旧版本目录，方便回退
- 或删除旧版本目录，节省空间

如果你不确定，建议先保留旧版本目录。

### 16.4 如果新版本有问题怎么办

如果更新后发现新版本不能正常工作，不要慌。

最简单的回退方式是：

1. 不再使用新目录
2. 回到旧目录
3. 重新运行旧目录里的：

```text
02_启动服务.bat
```

因为你没有覆盖旧目录，所以通常可以直接切回去。

### 16.5 更新版本时最容易犯的错误

请尽量避免下面这些做法：

#### 错误 1：直接覆盖旧目录

这样做的风险是：

- 文件可能新旧混杂
- 出问题后不容易回退
- 现场人员容易分不清到底跑的是哪个版本

#### 错误 2：只解压新版本，但没有重新运行 `01_首次配置.bat`

这样可能导致：

- 仍然走旧目录的命令入口
- 你以为自己在用新版本，实际上跑的还是旧版本

#### 错误 3：以为 GitHub 上出了新 release，离线电脑就自动更新了

不会自动更新。

离线电脑的实际版本，永远取决于你拷过去并实际运行的是哪个目录。

### 16.6 给现场人员的最短更新步骤

如果只看最短版本，请按下面做：

1. 把新的 ZIP 拷到离线电脑
2. 解压到**新目录**
3. 确认 `%USERPROFILE%\.openclaw\openclaw.json` 还在
4. 先运行：

```cmd
ollama serve
```

5. 打开新目录
6. 双击：

```text
01_首次配置.bat
```

7. 再双击：

```text
02_启动服务.bat
```

8. 测试确认没问题
9. 再决定要不要删旧目录
