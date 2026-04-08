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

## 12. 常见问题排查

### 12.1 双击 `02_启动服务.bat` 后窗口一闪而过

处理方法：

1. 不要双击
2. 改为打开命令提示符
3. 进入 OpenClaw 离线包目录
4. 手动执行：

```cmd
start.bat start
```

这样你可以直接看到报错内容。

### 12.2 提示找不到模型

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

### 12.3 提示 `ollama` 不是内部或外部命令

说明：

- Ollama 没安装好
- 或者 Ollama 没加到 PATH

必须先修复 Ollama，OpenClaw 才能继续用。

### 12.4 配置文件写错位置了

OpenClaw 真正读取的是：

```text
%USERPROFILE%\.openclaw\openclaw.json
```

不是下面这些位置：

- `examples\openclaw.ollama-local.example.json5`
- OpenClaw 安装目录根目录
- 桌面
- 下载目录

### 12.5 启动顺序不对

如果你先启动了 OpenClaw，后启动 Ollama，可能会导致 OpenClaw 无法正常连接模型。

最稳妥的做法是：

1. 先运行 `ollama serve`
2. 再运行 `02_启动服务.bat`

---

## 13. 最关键的 5 个位置和命令

请记住下面这 5 个信息。

### 13.1 OpenClaw 离线包目录

例如：

```text
D:\openclaw-offline-package\
```

### 13.2 OpenClaw 配置文件

```text
%USERPROFILE%\.openclaw\openclaw.json
```

### 13.3 查看 Ollama 模型

```cmd
ollama list
```

### 13.4 手动启动 OpenClaw

```text
02_启动服务.bat
```

### 13.5 切换模型

```cmd
start.bat models set ollama/<你的模型名>
```

---

## 14. 现场操作的最短步骤

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
