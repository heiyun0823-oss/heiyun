# Nginx AI Proxy

本项目是一个基于 Nginx 的反向代理服务，用于将 OpenAI API 请求转发到英伟达(NVIDIA) API 服务。
当然你也可以将 OpenAI API 请求转发到其他 AI 服务。
当然你也可以将 OpenAI API 请求转发到其他 AI 服务。
## 项目作用

将针对 `api.openai.com` 的 API 请求代理转发到英伟达的 `integrate.api.nvidia.com`，使得使用 OpenAI SDK 或 API 的应用可以无缝切换到英伟达的 AI 服务。

## 目录结构

```
nginx-ai-proxy/
├── cert/                    # SSL 证书目录
│   ├── api.openai.com.crt   # OpenAI 域名证书
│   ├── api.openai.com.key   # OpenAI 域名私钥
│   └── openaica.crt         # CA 证书
├── conf/                    # Nginx 配置目录
│   └── nginx.conf           # 主配置文件
├── logs/                    # 日志目录
│   ├── access.log           # 访问日志
│   └── error.log            # 错误日志
├── nginx.exe                # Nginx 可执行文件
└── killnginx.bat            # 停止 Nginx 脚本
```

## 使用方法

### 1. 配置 hosts 文件

以管理员身份编辑 `C:\Windows\System32\drivers\etc\hosts`，添加以下内容：

```
127.0.0.1 api.openai.com
```

### 2. 安装并信任 SSL 证书

1. 双击 `cert/openaica.crt` 文件
2. 点击"安装证书"
3. 选择"本地计算机" -> "将所有的证书都放入下列存储" -> "受信任的根证书颁发机构"
4. 完成安装

### 3. 启动 Nginx

双击 `nginx.exe` 或在命令行中执行：

```bash
./nginx.exe
```

### 4. 停止 Nginx

双击 `killnginx.bat` 或在命令行中执行：

```bash
./killnginx.bat
```

或者使用命令：

```bash
nginx -s stop
```

## 重要配置说明

### nginx.conf 关键配置

| 配置项 | 值 | 说明 |
|--------|-----|------|
| `worker_processes` | 10 | 工作进程数 |
| `worker_connections` | 1024 | 每个进程的最大连接数 |
| `proxy_pass` | https://integrate.api.nvidia.com | 代理目标地址 |
| `proxy_buffering` | off | 关闭缓冲，支持流式响应 |
| `proxy_read_timeout` | 3600s | 超时时间，适应长时间 AI 响应 |
| `proxy_ssl_verify` | off | 关闭 SSL 验证 |

## 注意事项

1. **证书信任**：必须将 CA 证书安装到"受信任的根证书颁发机构"，否则 HTTPS 请求会失败。

2. **hosts 配置**：确保 hosts 文件配置正确，否则请求不会经过本地代理。

3. **端口占用**：确保 443 端口没有被其他程序占用。

4. **日志查看**：如遇问题，请查看 `logs/error.log` 获取详细错误信息。

5. **API Key**：使用时需要在请求头中携带英伟达 API 的 Authorization，格式与 OpenAI 一致。

6. **防火墙**：可能需要配置防火墙允许 nginx.exe 通过。

## 常见问题

### Q: 启动后无法访问？

检查：
- hosts 文件是否正确配置
- 证书是否已信任
- 443 端口是否被占用

### Q: 如何验证代理是否工作？

查看 `logs/access.log` 文件，应该能看到请求记录。

### Q: 如何修改代理目标？

编辑 `conf/nginx.conf` 中的 `proxy_pass` 配置项，修改为目标 API 地址。

## 目标 API

当前代理目标：`https://integrate.api.nvidia.com`
