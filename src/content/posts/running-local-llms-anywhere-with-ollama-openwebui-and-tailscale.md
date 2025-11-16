---
title: 'Running Local LLMs Anywhere with Ollama, OpenWebUI and Tailscale'
pubDate: '2025-11-16'
---

## 1. Introduction

Large language models (LLMs) have become essential tools for developers, researchers and enthusiasts. Many of the most capable models are available only through paid cloud services, which means your data is sent to external servers and sometimes used to improve those services.

Running models locally gives you full control over your data while still providing access to powerful AI tools. With the right setup, you can run LLMs on a desktop, laptop or small server, giving you a private and reliable AI assistant without relying on external providers.

This guide will show you how to set up local LLMs using **Ollama**, enhance the experience with **OpenWebUI**, and access your models securely on any device via **Tailscale**.

## 2. Prerequisites

- **A capable device**: Desktop, laptop or small server. GPU acceleration is helpful for larger models.
- **Ollama installed**
- **Docker** for running OpenWebUI
- **A Tailscale account**
- **Another device for testing**, such as a phone or laptop
- **Sufficient storage** for models
- **Internet access** for downloading models and software
- **Basic command line familiarity**

*Personal note:* My own setup uses a Ryzen 5 5600, a Radeon RX7800XT and 32 GB of DDR4 RAM, and it handles models such as Qwen3 14B very comfortably. You do not need the same hardware, but it gives you an idea of what is possible on a reasonably affordable system.

## 3. Architecture Overview

Ollama runs the models locally, OpenWebUI provides a browser interface to interact with them, and Tailscale allows secure access from other devices. Together, this setup gives you a fully local AI environment that can be accessed anywhere without sending data to the cloud.

## 4. Installing and Configuring Ollama

### 4.1 Install Ollama

Download the appropriate version of Ollama for your operating system and follow the installation instructions. Confirm the installation with:

```bash
ollama --help
```

### 4.2 Pull Your First Model

Download a model from the library. For example:

```bash
ollama pull mistral:7b
```

For most setups, Mistral 7B is a great model to start with because it offers a good balance of speed and capability. If you plan to work on programming or technical tasks, the Qwen3 models are a strong choice and tend to perform very well.

After downloading a model, test it with:

```bash
ollama run mistral
```

### 4.3 Run Ollama as a Service

To keep Ollama running in the background:

- macOS or Linux: use launchd or systemd
- Windows: use Task Scheduler

This ensures your models are available whenever you need them.

## 5. Setting Up OpenWebUI

### 5.1 Installation

Docker provides the easiest installation method:

```bash
docker pull ghcr.io/open-webui/open-webui:main

docker run -d \
  -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

This maps the container’s port 8080 to port 3000 on your host, stores OpenWebUI data in a persistent Docker volume and allows the container to communicate with Ollama running on the host machine.

### 5.2 Connecting OpenWebUI to Ollama

Open your browser and navigate to http://localhost:3000.

In OpenWebUI settings, open Connections (or the backend configuration) and set the backend URL to your Ollama instance. When using Docker, this is often:

http://host.docker.internal:11434

Save your settings and send a test prompt to confirm that OpenWebUI is correctly routing requests to your local Ollama models.


## 6. Enabling Remote Access with Tailscale

### 6.1 Why Use Tailscale?

Tailscale creates a secure private network between your devices, allowing you to access OpenWebUI and Ollama without opening ports to the public internet.

### 6.2 Installation

Install Tailscale on the host machine and any devices you want to connect from. Sign in to the same tailnet.

### 6.3 Allowing Access

Each device receives a private IP and hostname. You can access OpenWebUI using a MagicDNS name such as:

```
http://mydesktop:3000
```

### 6.4 Testing

Visit the hostname and port from another device and submit a prompt to ensure the system is working. Wi-Fi generally provides the most reliable connection.

## 7. Performance Tips and Best Practices

- **Choosing the Right Model**: Check RAM and VRAM requirements. Smaller models run smoothly on most modern systems, while larger models require more resources.
- **GPU Acceleration**: A capable GPU improves inference speed significantly.
- **Adequate RAM**: More memory allows larger models to run and supports multitasking without slowing the system.

## 8. Security Considerations

- **Do Not Expose OpenWebUI Publicly**: Keep access restricted to private networks such as your Tailscale tailnet.
- **User Access in OpenWebUI**: The first user account is an administrator. Configure permissions carefully if multiple people will be using the system.
- **Tailscale ACLs**: Use Access Control Lists to restrict which devices or users can reach your host.

## 9. Real-World Use Cases

- **Remote personal assistant:** Use your local LLM from anywhere via Tailscale for note taking, drafting, quick questions or summaries, while keeping everything private.

- **Home server or NAS:** Add Ollama and OpenWebUI to an existing homelab or NAS to provide local, always-available AI tasks such as document queries or small automations.

- **Mobile device access:** Connect from your phone or tablet through Tailscale to run prompts on your home machine, giving you a private AI service wherever you are.

- **Team or household use:** OpenWebUI supports multiple users, so families or small teams can share the same local model securely through Tailscale.

- **MCP servers:** You can extend your setup with Model Context Protocol (MCP) servers, which allow models to make controlled use of external tools such as APIs, documentation sources or file operations.  
  The MCP community maintains a registry of available servers here: 
  ::github{repo="modelcontextprotocol/servers"}

  Some MCP servers I have found useful in my work at **Kainos** include:
  - **Microsoft Learn MCP**  
  - **Context7 MCP**

## 10. Conclusion

By combining Ollama, OpenWebUI and Tailscale, you can create a private, flexible and accessible local AI setup. This approach keeps your data on your own hardware while allowing you to interact with your models securely from multiple devices.

Once running, you can experiment with different models, explore new workflows and adapt the setup to your needs. Whether for personal productivity, homelab projects or shared use, a local LLM environment gives you full control and a foundation that can grow as tools and models continue to improve.