---
title: "Aspire Docker Compose Publisher — 從應用模型到部署產物的轉譯層"
type: concept
created: 2026-09-08
updated: 2026-09-08
sources: 2
tags: [dotnet-aspire, docker-compose, deployment, publisher, nuget, container]
topics: [dotnet-aspire, container-platform]
canonical: concepts/dotnet-aspire-docker-compose-publisher
---

# Aspire Docker Compose Publisher

> .NET Aspire 9.2+ 的正式部署功能，將應用模型轉譯為標準 `docker-compose.yaml`，讓部署產物完全脫離 Docker Desktop 依賴。

## 核心定位

Aspire 本地開發用 DCP（[[wiki/concepts/developer-control-plane-dcp|Developer Control Plane]]）做編排，跟 docker-compose 無關。Docker Compose Publisher 是**部署階段**的工具——把 AppHost 的 Code-first 拓撲翻譯成 docker-compose 格式的靜態產物。

```
AppHost (Code-first)
    │
    │ aspire publish
    ▼
docker-compose.yaml + .env
    │
    │ docker compose up / podman-compose up
    ▼
任何 OCI 相容 runtime 執行（不需要 Docker Desktop）
```

## 使用方式

### 1. 安裝 NuGet package

```xml
<PackageReference Include="Aspire.Hosting.Docker" Version="9.2.*" />
```

### 2. 在 AppHost 中啟用

```csharp
var builder = DistributedApplication.CreateBuilder(args);
builder.AddDockerComposeEnvironment("compose-env");
// ... 其他資源定義
```

### 3. 執行 publish

```bash
aspire publish --publisher docker-compose --output-path ./compose
```

產出 `docker-compose.yaml` + `.env`（機密用 `${VAR}` 佔位）。

## 為什麼這很重要

### 解耦開發與部署

| 階段 | 工具 | 跟 Docker Desktop 的關係 |
|------|------|------------------------|
| 本地開發（`aspire run`） | DCP | 需要 container runtime（Podman / Docker Desktop） |
| 部署（`aspire publish`） | Docker Compose Publisher | **完全無關**——產出靜態 YAML，任何 runtime 都能跑 |

### 繞過授權限制

產出的 `docker-compose.yaml` 是標準格式，可以用：
- Docker Engine + `docker compose` plugin（Apache 2.0，免費）
- Podman + `podman-compose`（Apache 2.0，免費）
- 任何支援 compose 格式的 OCI runtime

不需要 Docker Desktop 的 GUI 或 VM 層。

## 與其他 Publisher 的關係

Aspire 支援多種 publisher：
- **docker-compose**：產出 docker-compose.yaml（本頁主題）
- **kubernetes**：產出 Helm Chart / K8s Manifests
- **azure-container-apps**：直連 Azure

Publisher 是部署策略的切換開關，不是功能差異——同一份 AppHost 程式碼可以切換不同 publisher 產出不同部署格式。

## 來源

- [[wiki/sources/2026-09-08-aspire-container-runtime-comparison|Aspire 容器執行環境替代方案比較]]
- [Aspire 9.2 公告](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [Milan Jovanović 教學](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)

## 相關頁面

- [[wiki/entities/dotnet-aspire|.NET Aspire]]
- [[wiki/concepts/developer-control-plane-dcp|Developer Control Plane (DCP)]]
- [[wiki/comparisons/dotnet-aspire-windows-container-runtimes|Aspire 在 Windows 上的容器執行環境比較]]
- [[wiki/entities/docker|Docker]]
- [[wiki/entities/podman|Podman]]
