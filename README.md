# AutoFigure-Edit 配置教学手册

适用项目：https://github.com/ResearAI/AutoFigure-Edit

本文档按当前本地默认配置编写：Gemini 负责生图和 SVG 重建，Roboflow 负责 SAM 分割，Hugging Face 负责 RMBG-2.0 去背景。

## 1. 安装 Docker Desktop

AutoFigure-Edit 依赖 Python、深度学习库和图像处理库。推荐使用 Docker 部署，避免本机 Python 版本和依赖冲突。

Docker Desktop：
https://www.docker.com/products/docker-desktop/

安装后打开 Docker Desktop，等待 Docker Engine 正常运行。

## 2. 获取项目

在codex / cluade code里输入：
```
根据https://github.com/ResearAI/AutoFigure-Edit，
按照这个gitHub repo的README帮我配置下
```

## 3. 进入Vscode/Cursor等本地IDE，编辑 `.env`文件

当前推荐的最小配置只需要三类 key/token：

```env
GEMINI_API_KEY=你的_Google_AI_Studio_Gemini_API_key
HF_TOKEN=你的_HuggingFace_Read_token
ROBOFLOW_API_KEY=你的_Roboflow_Private_API_key
```

说明：

- `GEMINI_API_KEY`：用于 Gemini 生图和 SVG 重建。
- `HF_TOKEN`：用于下载 gated 模型 `briaai/RMBG-2.0` 做去背景。
- `ROBOFLOW_API_KEY`：用于 Web/Docker 默认 SAM3 图像分割的后端。


## 4. API Key 获取

### 4.1 Google Gemini API key

用途：

```env
GEMINI_API_KEY=...
```

获取步骤：

1. 打开 Google AI Studio。
2. 进入 API Keys 页面。
3. 创建 Gemini API key。
4. 可选：限制 key 只用于 Gemini API。
5. 复制到 `.env` 的 `GEMINI_API_KEY=` 后面。

官方文档：

https://ai.google.dev/gemini-api/docs/api-key

截图参考：

![Google AI Studio 创建 Gemini API key](instrut_img/gemini-api-key-create.png)

### 4.2 Hugging Face token

用途：

```env
HF_TOKEN=...
```

本项目使用 `briaai/RMBG-2.0` 做图标去背景。该模型是 gated 模型，需要先申请/接受访问权限。

获取步骤：

1. 登录 Hugging Face。
2. 访问 `briaai/RMBG-2.0` 模型页面并接受访问要求。
3. 打开 Settings 里的 Access Tokens。
4. 创建一个 Read token。
5. 复制到 `.env` 的 `HF_TOKEN=` 后面。

官方文档：

https://huggingface.co/briaai/RMBG-2.0?utm_source=chatgpt.com

https://huggingface.co/docs/hub/en/security-tokens

截图参考：

![Hugging Face RMBG-2.0 模型访问页面](instrut_img/huggingface-rmbg-access.png)

![Hugging Face 创建 Read token 页面](instrut_img/huggingface-read-token.png)

### 4.3 Roboflow API key

用途：

```env
ROBOFLOW_API_KEY=...
```

获取步骤：

1. 打开 Roboflow workspace： https://universe.roboflow.com/agenttrain/agent
2. 先 sign up / 登录。
3. 打开 workspace 的 Settings。
4. 进入 API Keys 页面。
5. 找到或生成 Private API Key。
6. 复制到 `.env` 的 `ROBOFLOW_API_KEY=` 后面。

官方文档：

https://docs.roboflow.com/developer/authentication/find-your-roboflow-api-key

截图参考：

![Roboflow 生成 Private API Key 页面](instrut_img/roboflow-api-key-create.png)

## 5. 检查 `.env` 是否包含以下key

保留下面这些即可：

```env
ROBOFLOW_API_KEY=你的_roboflow_private_key
GEMINI_API_KEY=你的_gemini_api_key
HF_TOKEN=你的_huggingface_read_token
```

不使用的 provider 相关配置可以不填，也不需要在手册流程里关注。

## 6. 启动服务

如果codex / claude code已经为你开启了服务器，则打开 Web 页面，复制粘贴以下URL
http://localhost:8000

推荐页面配置(见页面右边选项)：
![AutoFigure页面](instrut_img/autofigure_main_page.png)
```text
Provider: Gemini
Step 1 Image Provider: Same as SVG path / Gemini
Image Model: gemini-3.1-flash-image
Image Size: 4K
Auto Upscale: On
SVG Model: gemini-3.1-pro-preview
SAM Backend: roboflow
```

下方API key 输入框可以留空，后端会读取 `.env`。


## 8. 查看输出目录和历史图片

输出目录位于：

```text
AutoFigure-Edit\outputs
```

当前任务目录命名规则：

```text
output_YYYY-MM-DD_HH-MM
```

如果同一分钟内提交多个任务，会自动追加序号。

每个任务目录通常包含：

```text
run.log
figure.png
samed.png
boxlib.json
icons/
template.svg
optimized_template.svg
final.svg
```

如果任务失败，优先查看：

```text
outputs\<任务目录>\run.log
```

## 9. 如果有任何报错

问codex / claude code