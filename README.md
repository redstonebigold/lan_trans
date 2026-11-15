# 屏幕翻译助手 (Screen Translator)

一款跨平台（Windows / macOS）的屏幕内容实时翻译软件。支持**文件翻译**和**屏幕框选翻译**两种模式，基于大模型（LLM）翻译引擎。

## 功能特性

### 📄 文件翻译模式
- 拖入文件到翻译区，自动识别源语言并翻译为目标语言（默认中文）
- 支持格式：`.txt` / `.md` / `.markdown` / `.srt` / `.vtt`
- 两种输出方式：
  - **备份后翻译**（推荐）：先备份原文件，翻译结果输出到指定目录
  - **直接修改源文件**：翻译结果直接写回源文件

### 🖥 屏幕框选翻译模式
- 全局快捷键 `Ctrl+Shift+T`（可自定义）唤起框选
- 鼠标拖拽框选屏幕区域 → 自动截图 → OCR 识别文字 → LLM 翻译
- 翻译结果以悬浮窗覆盖在框选区域上方（半透明蒙层 + 译文浮在原位置）
- 悬浮窗可拖动、可关闭

### ⚙️ 翻译引擎
- 支持 DeepSeek / OpenAI / 通义千问 Qwen / 自定义（OpenAI 兼容接口）
- API Key 仅保存在本机，不上传
- 目标语言可选：中文 / 英语 / 日语 / 韩语 / 法语 / 德语 / 西班牙语 / 俄语

## 技术栈

| 层 | 技术 |
|---|---|
| 应用框架 | Electron 31 + React 18 |
| 构建工具 | Vite 5（渲染进程）+ esbuild（主进程/预加载） |
| OCR | Tesseract.js 5 |
| 翻译 | 大模型 Chat Completions API |
| 配置存储 | 本地 JSON（userData/settings.json） |

## 开发

```bash
# 安装依赖（国内网络建议使用镜像）
ELECTRON_MIRROR=https://npmmirror.com/mirrors/electron/ npm install

# 开发模式（渲染进程热更新）
npm run electron:dev

# 构建
npm run build

# 打包发行版（Windows NSIS / macOS dmg）
npm run electron:build
```

## 目录结构

```
electron/
  main/
    index.ts            # 主进程入口（窗口、托盘、快捷键、IPC）
    services/
      llm.ts            # LLM 翻译器
      fileParser.ts     # 文件解析与写回
      ocr.ts            # OCR 识别
      store.ts          # 配置存储
  preload/
    index.ts            # 预加载脚本（contextBridge）
  shared/
    types.ts            # 共享类型与 IPC 通道定义
src/
  App.tsx               # 主界面
  components/
    FileTranslate.tsx   # 文件翻译页
    ScreenTranslate.tsx # 屏幕翻译页
    Settings.tsx        # 设置页
public/
  capture.html          # 框选遮罩页
  overlay.html          # 翻译结果悬浮窗页
```

## 使用说明

1. 首次启动，进入「设置」页，选择引擎并填入 API Key，点击「测试连接」验证，然后「保存设置」。
2. **文件翻译**：切换到「文件翻译」页，拖入文件，选择输出方式，点击「开始翻译」。
3. **屏幕翻译**：按下 `Ctrl+Shift+T`（或「屏幕翻译」页点击框选按钮），拖拽框选屏幕区域，松开后自动翻译并显示悬浮窗。

## 当前限制与后续规划

- [x] 英文 OCR 与翻译（第一版）
- [x] 悬浮窗覆盖呈现
- [ ] 侧边栏对照模式
- [ ] 像素级原位替换
- [ ] 持续实时刷新模式
- [ ] 更多文件格式（PDF / DOCX / XLSX / PPTX）

## 注意事项

- OCR 首次使用需联网下载 Tesseract 语言数据（`eng.traineddata`）。
- LLM 翻译需要联网并配置有效 API Key。
- 屏幕捕获在 macOS 上首次使用需授予「屏幕录制」权限。
