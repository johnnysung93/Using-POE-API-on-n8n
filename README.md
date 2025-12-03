# Using POE API in n8n via OpenAI-Compatible Proxy

A comprehensive tutorial for integrating POE API with n8n using the poe2openai proxy server.

## ❓ The Problem

**POE API cannot be used natively in n8n.** There is no built-in POE node, and the API format is incompatible with existing n8n integrations.

However, [POE](https://poe.com) provides access to a wide variety of AI models — including GPT-4o, Claude, Gemini, Llama, and many more — all through a single subscription. This makes it an attractive option for n8n workflows that require AI capabilities.

**The Solution:** Use an OpenAI-compatible proxy to bridge POE API with n8n's existing OpenAI integration.

## 📖 Overview

This tutorial demonstrates how to use [poe2openai](https://github.com/jeromeleong/poe2openai), a proxy server that translates OpenAI API calls into POE API calls. This allows you to seamlessly access POE's AI models within n8n workflows using the native OpenAI credential.

## 🎯 Prerequisites

### Required
- [n8n](https://n8n.io/) - Workflow automation tool
- [POE API key](http://poe.com/api_key) - Available from your POE account
- [Docker](https://www.docker.com/) - Container platform

### Recommended
- [Portainer](https://www.portainer.io/) - Docker container management UI

## 🚀 Installation

### Step 1: Install Portainer

If you haven't already installed Portainer, follow the [official installation guide](https://docs.portainer.io/start/install).

### Step 2: Deploy the poe2openai Container

1. Open your Portainer dashboard
2. Navigate to **Containers** → **Add Container**
3. Configure the container with the following settings:

#### Basic Settings
- **Name**: `poe2openai` (or your preferred name)
- **Image**: `jeromeleong/poe2openai:latest`

#### Port Mapping
- **Host**: `8080` → **Container**: `8080`  
  *(You can use a different port if 8080 is already in use)*

#### Volume Mapping
- **Container Path**: `/data`
- **Volume**: `portainer_data` (local)  
  *(This should be automatically created when you install Portainer)*

#### Environment Variables
Add the following environment variables:

| Variable | Value | Description |
|----------|-------|-------------|
| `CONFIG_DIR` | `/data` | Configuration directory path |
| `ADMIN_USERNAME` | `your_username` | Admin username for the proxy |
| `ADMIN_PASSWORD` | `your_password` | Admin password for the proxy |
| `PORT` | `8080` | Port the proxy will listen on |

4. Click **Deploy the container**

## ✅ Verification

### Option 1: Web Interface

1. Access the proxy interface:
   - Via Portainer: Click the link in the container list
   - Via Browser: Navigate to `http://your_host:8080`

2. You should see the model management interface if the deployment was successful

### Option 2: Terminal Testing

Test the proxy using curl:

```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-poe-api-key" \
  -d '{
    "model": "gpt-4o-mini",
    "messages": [{"role": "user", "content": "Hello!"}],
    "stream": true
  }'
```

## 🔧 Configure n8n

### Step 1: Get Your POE API Key

1. Visit [http://poe.com/api_key](http://poe.com/api_key)
2. Copy your API key

### Step 2: Create n8n Credential

1. In n8n, navigate to **Credentials** → **Create Credential**
2. Select **OpenAI** from the credential types
3. Configure the credential:

| Field | Value | Notes |
|-------|-------|-------|
| **Name** | `POE_API` | Or any descriptive name |
| **API Key** | `anything` | This field is required but not validated |
| **Base URL** | `http://your_host:8080/` | Replace with your actual host |

4. Add a Custom Header:
   - **Header Name**: `Authorization`
   - **Header Value**: `Bearer your-poe-api-key`  
     *(Replace `your-poe-api-key` with your actual POE API key)*

5. Save the credential

## 🎉 Usage in n8n

1. Create a new AI Agent or open an existing one
2. Select *OpenAi* for chat model 
3. Select the credential you created (e.g., `POE_API`)
4. Choose your desired model from POE's available models
5. Test the node to verify the connection

## 🐛 Troubleshooting

### Container Won't Start
- Check if port 8080 is already in use
- Verify environment variables are correctly set
- Check Docker logs: `docker logs poe2openai`

### Connection Refused in n8n
- Ensure the Base URL includes the trailing slash: `http://your_host:8080/`
- Verify the proxy container is running
- Check firewall settings if using a remote host

### Invalid API Key Error
- Verify your POE API key at [http://poe.com/api_key](http://poe.com/api_key)
- Ensure the Authorization header is correctly formatted: `Bearer your-poe-api-key`
- Check for extra spaces in the header value

## 📚 Resources

- [poe2openai GitHub Repository](https://github.com/jeromeleong/poe2openai)
- [n8n Documentation](https://docs.n8n.io/)
- [POE API Documentation](https://creator.poe.com/docs/quick-start)

## 🙏 Acknowledgments

This tutorial uses [poe2openai](https://github.com/jeromeleong/poe2openai) by [jeromeleong](https://github.com/jeromeleong) to provide OpenAI-compatible proxy functionality for POE API.

## 📝 License

This tutorial is provided as-is for educational purposes. Please refer to the respective licenses of n8n, POE, and poe2openai for usage terms.

---

**Note**: Replace `your_host`, `your_username`, `your_password`, and `your-poe-api-key` with your actual values throughout this tutorial.
