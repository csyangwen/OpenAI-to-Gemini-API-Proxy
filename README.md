# Gemini API Proxy (Compatible with Gemini-Cli)

A lightweight Google Gemini API-compatible proxy server that allows you to call OpenAI-compatible LLM services using the Gemini API format.

## 🔥 Quick Setup for Gemini CLI

Currently, Gemini CLI cannot easily use models other than Gemini. This Python tool was developed to meet this need.

**Usage Steps:**
1. Modify `config.json` and fill in your Kimi API key
2. Install dependencies and run `python gemini_proxy_for_kimi.py`
3. Set environment variables:
   ```bash
   export GOOGLE_GEMINI_BASE_URL=http://localhost:8000/
   export GEMINI_API_KEY=sk-1234
   ```
4. In Gemini CLI, use `/auth` and select "Use Gemini API Key"

> **⚠️ Important Note: The current version has only been fully tested and optimized on Moonshot Kimi. Other OpenAI-compatible services require your own testing and adjustments.**

[中文文档](README_ZH.md) | [Chinese Documentation](README_ZH.md)

## ✨ Features

- 🔄 **Complete API Compatibility** - Supports all Gemini API endpoints
- 🔀 **Intelligent Format Conversion** - Seamless Gemini ↔ OpenAI format conversion  
- 🌊 **Streaming Response Support** - Complete Server-Sent Events streaming processing
- 🛠️ **Function Calling Support** - Bidirectional conversion of tool calls
- 🗣️ **Multi-turn Conversations** - Complete conversation history handling
- 📊 **Model Mapping** - Flexible model name mapping configuration
- 📝 **Detailed Logging** - Configurable access logs and detailed request logs
- ⚙️ **Configuration Files** - Unified management through JSON configuration files

## 🚀 Quick Start

### 1. Requirements

- Python 3.8+
- Dependencies: `fastapi`, `uvicorn`, `openai`

### 2. Install Dependencies

```bash
pip install fastapi uvicorn openai
```

### 3. Configuration File

Create a `config.json` file:

```json
{
  "openai_api_key": "sk-your-kimi-api-key",
  "openai_base_url": "https://api.moonshot.cn/v1",
  "model_mapping": {
    "gemini-2.5-pro": "kimi-k2-0711-preview",
    "gemini-2.5-flash": "moonshot-v1-auto",
  },
  "default_openai_model": "kimi-k2-0711-preview",
  "server": {
    "host": "0.0.0.0",
    "port": 8000,
    "log_level": "info"
  },
  "logging": {
    "enable_detailed_logs": true,
    "enable_access_logs": true,
    "log_directory": "logs"
  }
}
```

### 4. Start the Service

```bash
python gemini_proxy_for_kimi.py
```

The service will start at `http://0.0.0.0:8000`.

## 📖 API Usage

### Basic Request

```bash
curl -X POST http://localhost:8000/v1beta/models/gemini-2.5-pro:generateContent \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Hello, how are you?"}]
    }],
    "generationConfig": {
      "temperature": 0.7,
      "maxOutputTokens": 200
    }
  }'
```

### Streaming Request

```bash
curl -X POST http://localhost:8000/v1beta/models/gemini-2.5-pro:streamGenerateContent \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Write a short story"}]
    }]
  }'
```

### Token Counting

```bash
curl -X POST http://localhost:8000/v1beta/models/gemini-2.5-pro:countTokens \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Count tokens for this text"}]
    }]
  }'
```

### Function Calling

```bash
curl -X POST http://localhost:8000/v1beta/models/gemini-2.5-pro:generateContent \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "What is the weather like in Beijing?"}]
    }],
    "tools": [{
      "functionDeclarations": [{
        "name": "get_weather",
        "description": "Get weather information for a city",
        "parameters": {
          "type": "object",
          "properties": {
            "city": {"type": "string", "description": "City name"},
            "date": {"type": "string", "description": "Date in YYYY-MM-DD format"}
          },
          "required": ["city"]
        }
      }]
    }]
  }'
```

## 🔧 Configuration

### Complete Configuration File (config.json)

```json
{
  "openai_api_key": "sk-your-kimi-api-key",
  "openai_base_url": "https://api.moonshot.cn/v1",
  "model_mapping": {
    "gemini-2.5-pro": "kimi-k2-0711-preview",
    "gemini-2.5-flash": "moonshot-v1-auto",
  },
  "default_openai_model": "kimi-k2-0711-preview",
  "server": {
    "host": "0.0.0.0",
    "port": 8000,
    "log_level": "info"
  },
  "logging": {
    "enable_detailed_logs": false,
    "enable_access_logs": true,
    "log_directory": "logs"
  }
}
```

### Configuration Options

| Option | Description | Default |
|--------|-------------|---------|
| `openai_api_key` | OpenAI API key | Required |
| `openai_base_url` | OpenAI API base URL | `https://api.openai.com/v1` |
| `model_mapping` | Gemini to OpenAI model mapping | `{}` |
| `default_openai_model` | Default OpenAI model | `gpt-3.5-turbo` |
| `server.host` | Listen address | `0.0.0.0` |
| `server.port` | Listen port | `8000` |
| `server.log_level` | Log level | `info` |
| `logging.enable_detailed_logs` | Enable detailed request logs | `false` |
| `logging.enable_access_logs` | Enable access logs | `true` |
| `logging.log_directory` | Log directory | `logs` |

## 📊 Supported LLM Services

### ✅ Tested Services

#### Kimi (Moonshot) - Recommended
```json
{
  "openai_api_key": "sk-xxx",
  "openai_base_url": "https://api.moonshot.cn/v1",
  "model_mapping": {
    "gemini-2.5-pro": "kimi-k2-0711-preview",
    "gemini-2.5-flash": "kimi-k2-0711-preview"
  }
}
```

### ⚠️ Services Requiring Your Own Testing

> **Note: The following services are theoretically compatible but require your own testing and adjustments. You can enable enable_detailed_logs in the config file to output detailed request and response information for targeted adaptation.**

#### OpenAI
```json
{
  "openai_api_key": "sk-xxx",
  "openai_base_url": "https://api.openai.com/v1",
  "model_mapping": {
    "gemini-2.5-pro": "gpt-4o",
    "gemini-2.5-flash": "gpt-4o-mini"
  }
}
```

#### Azure OpenAI
```json
{
  "openai_api_key": "your-azure-key",
  "openai_base_url": "https://your-resource.openai.azure.com/openai/deployments/your-deployment",
  "model_mapping": {
    "gemini-2.5-pro": "gpt-4",
    "gemini-2.5-flash": "gpt-35-turbo"
  }
}
```

#### DeepSeek
```json
{
  "openai_api_key": "sk-xxx", 
  "openai_base_url": "https://api.deepseek.com/v1",
  "model_mapping": {
    "gemini-2.5-pro": "deepseek-chat",
    "gemini-2.5-flash": "deepseek-chat"
  }
}
```

#### Zhipu AI
```json
{
  "openai_api_key": "your-zhipu-key",
  "openai_base_url": "https://open.bigmodel.cn/api/paas/v4",
  "model_mapping": {
    "gemini-2.5-pro": "glm-4",
    "gemini-2.5-flash": "glm-4-flash"
  }
}
```

#### Ollama (Local Deployment)
```json
{
  "openai_api_key": "ollama",
  "openai_base_url": "http://localhost:11434/v1",
  "model_mapping": {
    "gemini-2.5-pro": "llama3:8b",
    "gemini-2.5-flash": "llama3:8b"
  }
}
```

### 🛠️ Customizing Other Services

If you need to adapt other OpenAI-compatible services:

1. **Clone the project**: `git clone `
2. **Modify configuration**: Update API endpoints and model mappings in `config.json`
3. **Test functionality**: Focus on testing streaming responses, function calls, multi-turn conversations. You can enable enable_detailed_logs in the config file to output detailed request and response information for targeted adaptation.
4. **Optimize code**: Adjust conversion logic based on target service characteristics

## 📝 Logging System

### Access Logs
Concise access logs showing basic information for each request:
```
🚀 POST /v1beta/models/gemini-2.5-pro:generateContent - 200 - Model: gemini-2.5-pro - ID: abc12345 - 2.341s
🚀 POST /v1beta/models/gemini-2.5-pro:streamGenerateContent - 200 - Model: gemini-2.5-pro(stream) - ID: def67890 - 5.123s
```

### Detailed Logs
Complete request/response conversion process (optional):
- `1_GEMINI_REQUEST` - Original Gemini request
- `2_OPENAI_REQUEST` - Converted OpenAI request  
- `3_OPENAI_RESPONSE` - Raw OpenAI response
- `4_GEMINI_RESPONSE` - Final Gemini response

## 🚀 Production Deployment

### Using Gunicorn (Recommended)

1. Install Gunicorn:
```bash
pip install gunicorn
```

2. Create startup script `start.sh`:
```bash
#!/bin/bash
gunicorn gemini_proxy_for_kimi:app \
  --worker-class uvicorn.workers.UvicornWorker \
  --workers 4 \
  --bind 0.0.0.0:8000 \
  --access-logfile - \
  --error-logfile - \
  --log-level info
```

3. Run:
```bash
chmod +x start.sh
./start.sh
```

### Using systemd Service

Create `/etc/systemd/system/gemini-proxy.service`:

```ini
[Unit]
Description=Gemini API Proxy
After=network.target

[Service]
Type=exec
User=your-user
Group=your-group
WorkingDirectory=/path/to/your/app
ExecStart=/path/to/your/venv/bin/gunicorn gemini_proxy_for_kimi:app \
  --worker-class uvicorn.workers.UvicornWorker \
  --workers 4 \
  --bind 0.0.0.0:8000
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

Start the service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable gemini-proxy
sudo systemctl start gemini-proxy
```

### Nginx Reverse Proxy

Create Nginx configuration:

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Support streaming responses
        proxy_buffering off;
        proxy_cache off;
        
        # Increase timeout
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
}
```

### Performance Optimization

1. **Worker Count**: Set based on CPU cores, recommended `2 * CPU_CORES + 1`

2. **Memory Optimization**:
```bash
# Limit memory usage
gunicorn --max-requests 1000 --max-requests-jitter 100 ...
```

3. **Connection Pool**: Add connection pool settings in configuration

4. **Caching**: Consider adding Redis caching layer for responses

## 🔍 Monitoring and Maintenance

### Health Check

```bash
curl http://localhost:8000/health
```

Returns:
```json
{"status": "healthy", "service": "gemini-proxy"}
```

### Log Rotation

Use logrotate to manage log files:

```bash
# /etc/logrotate.d/gemini-proxy
/path/to/your/app/logs/*.log {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    create 644 your-user your-group
    postrotate
        systemctl reload gemini-proxy
    endscript
}
```

### Monitoring Metrics

Access logs include:
- Request method and path
- Response status code
- Model used
- Request ID
- Response time

## 🛠️ Development and Debugging

### Development Mode

```bash
# Enable detailed logs
# Set in config.json
{
  "logging": {
    "enable_detailed_logs": true,
    "enable_access_logs": true
  }
}

# Start development server
python gemini_proxy_for_kimi.py
```

### Debugging Tips

1. **View detailed logs**: Enable `enable_detailed_logs` to see complete conversion process
2. **Model mapping testing**: Test mapping with different Gemini model names
3. **Streaming response debugging**: Observe SSE data streams
4. **Function call debugging**: Check format conversion of tool calls

## 🤝 Contributing

Issues and Pull Requests are welcome!

### Development Environment Setup

```bash
git clone 
cd gemini-proxy
pip install -r requirements.txt
```

### Code Standards

- Follow PEP 8 code style
- Add appropriate type annotations
- Write clear docstrings
- Ensure backward compatibility

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) file for details.

## 🆘 FAQ

### Q: Which Gemini API endpoints are supported?
A: Supports `generateContent`, `streamGenerateContent`, `countTokens`, and `health` endpoints.

### Q: Which LLM services are currently supported?
A: **Currently only fully tested on Moonshot Kimi**. Other OpenAI-compatible services are theoretically usable but require your own testing and adjustments.

### Q: How to adapt other LLM services?
A: 1) Clone the project code 2) Modify `config.json` configuration 3) Focus on testing streaming responses, function calls, etc. 4) Adjust code logic as needed

### Q: Why only support Kimi?
A: Different LLM services have variations in API details, response formats, error handling, etc., requiring targeted testing and optimization. Currently, efforts are focused on complete Kimi adaptation.

### Q: What if streaming responses don't work?
A: Check if the client correctly handles `text/event-stream` format and ensure the network environment supports SSE.

### Q: How to handle high concurrent requests?
A: Increase Gunicorn worker count, use load balancers, consider adding caching layers.

## 🔗 Related Links

- [Google Gemini API Documentation](https://ai.google.dev/gemini-api/docs/text-generation?hl)
- [Moonshot Kimi API Documentation](https://platform.moonshot.cn/docs/introduction)

---

**⭐ If this project helps you, please give us a Star!**