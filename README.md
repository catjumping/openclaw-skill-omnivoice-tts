# OmniVoice TTS - OpenClaw Skill

> 🔊 OmniVoice 专业级语音克隆与文本转语音合成技能，为 OpenClaw 智能助手提供高自然度、低延迟的企业级语音合成能力，适配短视频配音、有声书制作、智能客服等多场景。

[![Version](https://img.shields.io/badge/version-1.0.0-green)]()
[![License](https://img.shields.io/badge/license-MIT-yellow)](LICENSE)

---

## ⚠️ 重要说明：API Key 获取

**本技能需要前往 OmniVoice 官网注册获取专属 API Key 才能使用**。

- 官网：[https://omnivoiceai.net](https://omnivoiceai.net)
- 注册账号后即可获取你的专属 API Key
- API Key 为用户专属，请勿泄露给他人

---

## ✨ 功能特性

- 🎤 **高拟真声音克隆**：上传 20秒左右清晰音频即可创建接近真人发声的专属声音模型
- 📋 **模型全生命周期管理**：支持模型列表查询、删除操作
- 🚀 **一键合成自动下载**：单命令完成「创建合成任务→自动轮询等待→下载音频到本地」全流程，无需分步操作
- 🌍 **多风格多语言支持**：支持基础/专业/多语言三种模型版本，支持语气控制、语速调整
- 🪟 **完美 Windows 兼容**：从底层解决 Windows 控制台 GBK 中文编码乱码问题
- 📦 **零依赖运行**：仅使用 Python 标准库，无需安装任何第三方 pip 包
- 🔑 **安全密钥机制**：API Key 不硬编码，每个用户独立配置自己的密钥，数据安全隔离


---

## 📦 安装

### 手动安装
1. 下载本技能压缩包并解压
2. 将 `omnivoice-tts` 文件夹放到你的 OpenClaw 工作区 `skills/` 目录下，路径通常为：
   ```
   C:\Users\你的用户名\.openclaw\workspace\skills\
   ```
3. 重启 OpenClaw，技能会自动被识别加载

---

## 🔧 配置

### 设置 API Key（二选一）

**方式1：环境变量（推荐，一次配置永久生效）**

Windows PowerShell:
```powershell
$env:OMNIVOICE_API_KEY='你的 API Key'
```


Windows CMD:
```cmd
set OMNIVOICE_API_KEY=你的 API Key
```

Linux/macOS:
```bash
export OMNIVOICE_API_KEY='你的 API Key'
```

想要永久生效，请将 `OMNIVOICE_API_KEY` 添加到系统环境变量中。

**方式2：命令行参数临时传入**
所有命令都支持 `--api-key 你的APIKey` 参数。

---

## 🚀 使用方法

进入技能目录：
```bash
cd skills/omnivoice-tts
```

> **注意**：Windows 下用 `py` 启动 Python，Linux/macOS 用 `python3`。

### 1. 查看已有声音模型
```bash
py scripts/omnivoice_tts.py list
```
列出你账号下所有已创建的声音模型，记录下 `audio-id` 用于后续合成。

### 2. 上传音频创建声音克隆模型
```bash
py scripts/omnivoice_tts.py upload --file <音频文件路径> --name "<模型名称>" [--describe "<描述>"]
```
- 支持格式：mp3/wav/m4a
- 建议使用 20秒左右清晰无背景噪音的人声，克隆效果最佳

**示例：**
```powershell
py scripts/omnivoice_tts.py upload --file "C:/voices/myvoice.wav" --name "我的声音" --describe "温暖旁白男声"
```

### 3. 文本转语音合成（自动等待+下载）
```bash
py scripts/omnivoice_tts.py tts --text "<要合成的文本>" --audio-id <模型ID> [选项]
```

**选项：**
- `--style <1|2|3>`：模型版本
  - `1`：基础模型（默认，参考原音频风格，克隆效果最贴近原声）
  - `2`：专业模型（支持语气控制、参考音频模式，效果更稳定自然）
  - `3`：多语言模型（支持多语言混合合成）
- `--genre <0|1|2>`：模型类别（可选，整数）
  - `0`：参考原音频（默认，所有模型都支持）
  - `1`：语气参考模式（仅专业模型 style=2 支持）
  - `2`：使用参考音频模式（仅专业模型 style=2 支持，需要额外传入参考音频）
- `--speed <0.5~1.5>`：语速控制（可选，浮点数，默认 1.0）
- `--emotion-path <URL>`：参考音频URL（仅 `--genre 2` 且 `--style 2` 专业模式时使用）
- `--output/-o <路径>`：**脚本本地参数**，自定义合成后音频下载保存到本地的 wav 文件路径（可选，默认自动生成文件名，API本身无此参数）

> **注意：** 
> - 使用参考音频模式需要同时设置 `--genre 2 --style 2 --emotion-path <参考音频URL>`
> - 合成的语音文件服务器仅保留1天，请及时下载。

**示例：**
```powershell
py scripts/omnivoice_tts.py tts --text "你好，欢迎使用OmniVoice语音合成！" --audio-id 你的模型ID --output "output.wav"
```

### 4. 删除不需要的模型
```bash
py scripts/omnivoice_tts.py delete --audio-id <模型ID>
```
> ⚠️ 删除后不可恢复，请谨慎操作。

---

## 📖 OpenClaw 技能调用示例

在 OpenClaw 对话中可以直接通过自然语言调用：
```
用OmniVoice合成这句话："今天天气真好，适合出去走走。"
帮我用刚才上传的参考音频创建一个叫"我的声音"的克隆模型
列出我所有的OmniVoice声音模型
```
助手会自动调用技能完成对应操作，合成完成后直接返回音频文件。

---

## 🔌 API 接口说明

**API 基础地址**：`https://openapi.lipvoice.cn/api/third/`

**鉴权方式**：所有接口需要在 HTTP Header 中传递 `sign: <你的API Key>`

| 功能 | 方法 | 端点 |
|------|------|------|
| 创建模型 | POST | `/reference/upload` |
| 模型列表 | GET | `/reference/list` |
| 删除模型 | DELETE | `/reference/delete` |
| 创建合成任务 | POST | `/tts/create` |
| 查询合成结果 | GET | `/tts/result?taskId=xxx` |

---

## ❓ 常见问题

### Q: 提示 "未配置API Key"
A: 请设置环境变量 `OMNIVOICE_API_KEY`，或者调用时添加 `--api-key 你的Key` 参数。API Key 可前往 https://omnivoiceai.net 获取。

### Q: 提示 "sign无效或用户未开通API"
A: 检查 API Key 是否正确，确认你的密钥是从 https://omnivoiceai.net 获取的有效密钥。

### Q: 合成需要多长时间？
A: 通常 3-10 秒，脚本会自动轮询等待，最长等待 60 秒超时。

### Q: 为什么Windows下输出乱码？
A: 本技能已经修复 Windows GBK 编码问题，如果仍有问题请确保使用 Python 3.7+ 版本。

---

## 📝 更新日志

### v1.0.0 - 2026-07-09
- 🎉 OmniVoice 语音合成技能首次发布
- ✅ 支持声音模型上传、列表、删除全功能
- ✅ 一键 TTS 合成，自动等待完成并下载到本地
- ✅ 完美修复 Windows 中文 GBK 编码乱码问题
- ✅ 零依赖，仅使用 Python 标准库即可运行
- ✅ 支持环境变量和命令行参数两种 API Key 配置方式

---

## 📄 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件。
