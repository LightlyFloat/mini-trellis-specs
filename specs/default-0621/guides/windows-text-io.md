# Windows 文本 IO 编码规范

> 适用范围：在 Windows 上生成或修改任何涉及文本读写、控制台输出的代码。
> 目的：使程序的文本编码行为不依赖系统默认代码页，避免中文输出乱码。

---

## 一、规则

在 Windows 上生成任何涉及文本读写或控制台输出的代码时，必须显式声明 UTF-8，
不依赖系统默认代码页。

Windows 中文环境的默认代码页为 cp936，即 GBK。不显式声明编码时，文本读写与控制台输出
会按 cp936 处理，结果是中文乱码或抛出 `UnicodeDecodeError`。

这是一条默认规则，不是故障排查清单。编码在写代码时即显式声明，而不是等出现乱码后再修。

---

## 二、Python

以下标准流重配置与 `PYTHONUTF8` 均要求 Python 3.7 及以上。

### 1. 文件读写

所有文本模式的 `open` 显式传 `encoding="utf-8"`。

```python
with open(path, "r", encoding="utf-8") as f: ...
with open(path, "w", encoding="utf-8") as f: ...
```

`pathlib` 同理，使用 `Path(p).read_text(encoding="utf-8")` 与
`Path(p).write_text(s, encoding="utf-8")`。

`json.load` 与 `json.dump` 本身不涉及编码，实际编码由传入的文件对象决定，
因此仍须在 `open` 处声明。

### 2. 控制台输出

需要输出中文时，在程序入口处显式重配置标准流：

```python
import sys
sys.stdout.reconfigure(encoding="utf-8")
sys.stderr.reconfigure(encoding="utf-8")
```

另一种方式是在启动环境中设置 `PYTHONUTF8=1`，使整个解释器进入 UTF-8 模式。
两种方式择一即可。脚本内重配置的优点是不依赖调用方的环境变量。

### 3. 子进程

`subprocess` 捕获输出时显式指定编码与解码错误策略：

```python
subprocess.run(cmd, capture_output=True, text=True,
               encoding="utf-8", errors="replace")
```

只写 `text=True` 会按系统默认编码解码，在 cp936 环境下捕获 UTF-8 输出会报错。
`errors="replace"` 保证个别不可解码字节不会中断整个流程。

---

## 三、PowerShell

### 1. 控制台编码

脚本开头显式设置输出编码：

```powershell
$OutputEncoding = [System.Text.UTF8Encoding]::new($false)
[Console]::OutputEncoding = [System.Text.UTF8Encoding]::new($false)
```

`$OutputEncoding` 影响 PowerShell 通过管道向外部程序写入时使用的编码，
`[Console]::OutputEncoding` 影响从外部程序读取输出时使用的解码。
两者作用对象不同，须同时设置。构造时传入 `$false` 表示不写 BOM。

### 2. 文件写入

`Out-File`、`Set-Content`、`Add-Content` 均显式指定 `-Encoding utf8`。

```powershell
$text | Set-Content -Path $path -Encoding utf8
```

Windows PowerShell 5.1 的 `utf8` 会写入 BOM，PowerShell 6 及以上默认不写 BOM。
若产物必须无 BOM，使用 `utf8NoBOM`，该取值仅 PowerShell 6 及以上可用；
需兼容 5.1 时改用 .NET 方法 `[System.IO.File]::WriteAllText($path, $text, $utf8NoBom)`。

### 3. 文件读取

`Get-Content` 在 Windows PowerShell 5.1 中默认按系统代码页解码，读取无 BOM 的
UTF-8 文件会乱码，须显式指定 `-Encoding utf8`。

---

## 四、不在本规范范围内

Git Bash 中向 `curl` 传递中文命令行参数时，argv 按 ANSI 编码传入。这是 Git Bash 自身的
参数传递行为，只影响手工调用接口，不影响生成的程序，适用面窄得多，不纳入本规范。

---

## 五、验证

生成的代码须在中文 Windows 环境下满足以下三项：

- 控制台输出中文不出现乱码或问号
- 读写含中文的文本文件不抛 `UnicodeDecodeError`
- 捕获子进程的中文输出不报错
