# llama.cpp CUDA Docker Container

This project provides a streamlined Docker-based solution for running **llama.cpp** with NVIDIA GPU acceleration. It uses a centralized configuration file (`models.ini`) to manage multiple models and their specific inference parameters.

## 📋 Prerequisites

Before setting up this project, ensure your host machine has:
- **NVIDIA Drivers** installed.
- **NVIDIA Container Toolkit** installed and configured.
- **Docker** and **Docker Compose** installed.

## ⚙️ Environment Variables

This project uses an environment file to manage API keys and default settings.

1. Copy the skeleton file to create your local environment file:
   ```bash
   cp .env.skel .env
   ```

2. Edit `.env` with your specific details:

| Variable | Description | Default |
|----------|-------------|---------|
| `LLAMA_API_KEY` | API key for authentication (optional). | `Your secure api key here` |
| `DOCKER_TAG` | The Docker image tag (e.g., `server-cuda13`). | `server-cuda13` |
| `PORT` | The host port to expose (maps to container port 8000). | `8000` |

## 🚀 Installation & Setup

### 1. Prepare the Models Directory
Create the `models` folder if it doesn't exist and place your **GGUF** model files inside:
```bash
mkdir -p models
```

### 2. Configure Model Presets
The server uses `models.ini` to determine how to load models. 
- Use `models.ini.skel` as a template.
- Copy it to `models.ini` and define your models.

```bash
cp models.ini.skel models.ini
```

> 💡 **Tip**: Ensure the `m` path in `models.ini` correctly points to the file inside the `./models` folder.

### 3. Launch the Container
Start the server in detached mode:
```bash
docker compose up -d
```

## 🌐 Accessing the Server

The server is exposed on host port **8000** (mapping to container port 8000).

- **URL**: `http://localhost:8000`
- **Status**: The container is set to `unless-stopped`, ensuring it restarts automatically.

## 🛠️ Configuration Explanations

The following settings are defined in `docker-compose.yml`:

| Flag | Description |
|------|-------------|
| `--host 0.0.0.0` | Binds the server to all network interfaces. |
| `--port 8000` | The internal port the server listens on. |
| `--models-preset /app/models.ini` | Points the server to your configuration file. |
| `--models-autoload` | Automatically loads models defined in the `.ini` file on startup. |
| `--models-max 1` | Limits the number of models loaded into VRAM simultaneously. |
| `-ngl 99` | **GPU Offloading**. `99` offloads all layers to the GPU. Reduce this value if you experience VRAM limits. |

## 📝 Customization Tips

- **VRAM Optimization**: If you encounter "Out of Memory" errors, reduce the `-ngl` value in `docker-compose.yml` or within your `models.ini`.
- **Multiple Models**: If you have enough VRAM to run multiple models at once, increase the `--models-max` value in `docker-compose.yml`.
- **Thinking Models**: For models requiring specific jinja templates (like reasoning models), ensure `jinja = enabled` is set in your `models.ini`.

---

*For more information on llama.cpp, visit [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)*
