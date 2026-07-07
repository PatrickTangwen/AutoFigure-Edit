# AutoFigure-Edit Docker 启动指南

这份指南只讲 Web 服务的正确 Docker 启动流程，分为两种情况：

- 第一次使用：还没有构建过镜像，或者刚拿到项目。
- 已配置好的使用：之前已经配置过 `.env`，也已经有 `autofigure-edit` 容器。

## 一、已配置好的用户：日常启动

如果你之前已经成功配置并运行过 AutoFigure-Edit，日常只需要做这几步。

### 1. 启动 Docker Desktop

先打开 Docker Desktop，等它进入可用状态。

可以用下面命令确认 Docker 引擎是否已经启动：

```powershell
docker info
```

如果这里报错，例如找不到 `dockerDesktopLinuxEngine`，说明 Docker Desktop 还没有真正启动完成。

### 2. 进入项目目录

```powershell
cd <AutoFigure-Edit 项目目录>
```

### 3. 启动已有容器

推荐用：

```powershell
docker compose start
```

或者直接启动容器：

```powershell
docker start autofigure-edit
```

这两个命令适合“已经有容器，只是现在没运行”的情况。

### 4. 检查服务状态

```powershell
docker compose ps
```

正常时应该能看到类似状态：

```text
autofigure-edit   Up ... (healthy)   0.0.0.0:8000->8000/tcp
```

再检查健康接口：

```powershell
curl http://127.0.0.1:8000/healthz
```

正常返回：

```json
{"status":"ok"}
```

然后打开：

```text
http://localhost:8000
```

## 二、第一次使用：首次构建和启动

第一次使用时，需要先准备 `.env`，再构建镜像和启动容器。

### 1. 启动 Docker Desktop

先打开 Docker Desktop，并等待 Docker 引擎启动完成。

确认方式：

```powershell
docker info
```

### 2. 进入项目目录

```powershell
cd <AutoFigure-Edit 项目目录>
```

### 3. 准备 `.env`

如果还没有 `.env`，先从示例文件复制：

```powershell
Copy-Item .env.example .env
```

然后编辑 `.env`，至少填写你要使用的 provider 所需的 key。

常用变量：

```text
OPENAI_API_KEY=...
GEMINI_API_KEY=...
GOOGLE_API_KEY=...
ROBOFLOW_API_KEY=...
FAL_KEY=...
BIANXIE_API_KEY=...
OPENROUTER_API_KEY=...
```

如果你只用 OpenAI 路线，重点确认：

```text
OPENAI_API_KEY=你的_OpenAI_Key
```

如果使用 Gemini 路线，重点确认：

```text
GEMINI_API_KEY=你的_Gemini_Key
```

### 4. 首次构建并启动

第一次没有镜像或容器时，使用：

```powershell
docker compose up -d --build
```

这个命令会做两件事：

- `--build`：构建 Docker 镜像。
- `up -d`：后台创建并启动容器。

首次构建可能比较慢，因为需要拉基础镜像和安装依赖。

### 5. 检查启动结果

```powershell
docker compose ps
curl http://127.0.0.1:8000/healthz
```

健康检查返回 `{"status":"ok"}` 后，打开：

```text
http://localhost:8000
```

## 三、什么时候需要 `--build`

日常启动不要默认使用 `docker compose up -d --build`。

只有下面这些情况才需要重新 build：

- 第一次使用，还没有镜像。
- 修改了 `Dockerfile`。
- 修改了 `requirements.txt`。
- 修改了需要打进镜像里的后端代码，并且当前容器没有挂载这些代码。
- 怀疑镜像环境已经过期，需要和当前代码重新对齐。

如果只是“电脑重启了，Docker 容器没运行”，用这个就够：

```powershell
docker compose start
```

如果只是想重启服务：

```powershell
docker compose restart autofigure-edit
```

## 四、常用维护命令

查看容器状态：

```powershell
docker compose ps
```

查看日志：

```powershell
docker compose logs -f autofigure-edit
```

停止容器但保留容器：

```powershell
docker compose stop
```

再次启动已停止的容器：

```powershell
docker compose start
```

停止并删除容器，但保留镜像和数据卷：

```powershell
docker compose down
```

重新构建镜像：

```powershell
docker compose up -d --build
```

完全无缓存重建：

```powershell
docker compose build --no-cache
docker compose up -d
```

## 五、今天这类情况应该怎么做

如果已经有 `autofigure-edit` 容器，只是服务没开，正确流程是：

```powershell
cd <AutoFigure-Edit 项目目录>
docker info
docker compose start
docker compose ps
curl http://127.0.0.1:8000/healthz
```

如果 `docker info` 报错，就先启动 Docker Desktop，等它 ready 后再执行：

```powershell
docker compose start
```

不需要先跑本地 `python server.py`，也不需要每次 `docker compose up -d --build`。

## 六、前端 provider 小提示

如果要整条路线切到 OpenAI：

- `SVG / 推理 Provider` 选择 `OpenAI Responses`。
- `Step 1 Image Provider` 保持 `Same as SVG path`。

这样后端会使用：

- SVG / 推理：`openai_response`
- 第一步生图：自动映射到 `openai`

如果只在 `Step 1 Image Provider` 里选 `OpenAI Images`，那只会切换第一步生图，SVG 重建仍然使用上面的主 provider。
