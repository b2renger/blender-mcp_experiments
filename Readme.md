# Blender MCP — Complete Setup & Usage Tutorial

A comprehensive guide to connecting Blender with AI through the Model Context Protocol, from first install to your first 3D scenes created with natural language.

---

## Table of Contents

- [What Is Blender MCP?](#what-is-blender-mcp)
- [Prerequisites](#prerequisites)
- [Part 1 — Install `uv` (the Package Manager)](#part-1--install-uv-the-package-manager)
- [Part 2 — Install the Blender Addon](#part-2--install-the-blender-addon)
- [Part 3 — Configure Your MCP Client](#part-3--configure-your-mcp-client)
  - [Option A: Claude Desktop](#option-a-claude-desktop)
  - [Option B: Cursor IDE](#option-b-cursor-ide)
  - [Option C: VS Code](#option-c-vs-code)
- [Part 4 — Connect Everything](#part-4--connect-everything)
- [Part 5 — Troubleshooting Common Issues](#part-5--troubleshooting-common-issues)
- [Part 6 — Your First Examples](#part-6--your-first-examples)
- [Part 7 — Tips for Effective Prompting](#part-7--tips-for-effective-prompting)
- [Part 8 — Available MCP Tools Reference](#part-8--available-mcp-tools-reference)
- [Part 9 — Advanced Workflows](#part-9--advanced-workflows)
- [Part 10 — Using Blender MCP with Local LLMs (Free Alternative)](#part-10--using-blender-mcp-with-local-llms-free-alternative)
  - [Option A: Blender Open MCP + Ollama](#option-a-blender-open-mcp--ollama-recommended--fully-local)
  - [Option B: Original Blender MCP + Roo Code + Ollama](#option-b-original-blender-mcp--vs-code--roo-code--ollama)
  - [Option C: OpenRouter Free Models](#option-c-original-blender-mcp--openrouter-free-models-no-gpu-needed)
  - [Choosing a Local Model](#choosing-a-local-model--detailed-comparison)
  - [Tips for Local Models](#tips-for-getting-good-results-with-local-models)
- [Quick-Start Checklist (Claude Desktop)](#quick-start-checklist-claude-desktop)
- [Resources](#resources)

---

## What Is Blender MCP?

Blender MCP (by **Siddharth Ahuja** — [github.com/ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp)) is an open-source bridge that connects Blender to AI assistants like Claude through the **Model Context Protocol (MCP)**. Instead of manually modeling in Blender, you describe what you want in plain English and the AI generates and executes the Blender Python code for you in real time.

**What it can do:**

- Create, modify, and delete 3D objects via natural language
- Apply and modify materials, colors, and textures
- Inspect and query the current scene
- Execute arbitrary Python code in Blender
- Download assets from Poly Haven (HDRIs, textures, 3D models)
- Generate 3D models via Hyper3D integration

**How it works (architecture):**

```
You (in Claude / Cursor / VS Code)
        ↓  natural language
   MCP Client (Claude Desktop, Cursor, etc.)
        ↓  MCP protocol
   MCP Server (blender-mcp, Python)
        ↓  TCP socket (port 9876)
   Blender Addon (addon.py, running inside Blender)
        ↓  bpy Python API
   Blender (creates/modifies the scene)
```

The addon runs a socket server inside Blender. The MCP server translates AI tool calls into JSON commands and sends them over TCP. Commands execute via Blender's Python API (`bpy`).

---

## Prerequisites

Before you start, make sure you have:

| Requirement | Details |
|---|---|
| **Blender** | Version 3.0 or newer (3.6+ recommended for full compatibility) |
| **Python** | Version 3.10 or newer (usually already on your system) |
| **uv** | The fast Python package manager (provides the `uvx` command) |
| **MCP Client** | Claude Desktop, Cursor IDE, or VS Code |
| **AI Backend** | Claude subscription (paid) OR Ollama with a local model (free) — see Part 10 |

---

## Part 1 — Install `uv` (the Package Manager)

`uv` is a fast Python package manager made by Astral. It provides the `uvx` command which launches the MCP server on demand. **You must install this before proceeding.**

### macOS

Using Homebrew:

```bash
brew install uv
```

Or using the install script:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Windows

Open **PowerShell** and run:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Then add it to your PATH:

```powershell
$localBin = "$env:USERPROFILE\.local\bin"
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")
[Environment]::SetEnvironmentVariable("Path", "$userPath;$localBin", "User")
```

**Important:** Close and reopen your terminal after this step.

### Linux

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Verify installation

```bash
uvx --version
```

If you see a version number, you're ready to proceed.

---

## Part 2 — Install the Blender Addon

The addon is a single Python file (`addon.py`) that creates a TCP socket server inside Blender.

### Step-by-step

1. **Download `addon.py`** from the GitHub repository:
   - Go to [github.com/ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp)
   - Download the `addon.py` file (click it → Raw → Save As), or clone the whole repo:
     ```bash
     git clone https://github.com/ahujasid/blender-mcp.git
     ```

2. **Open Blender**

3. **Install the addon:**
   - Go to **Edit → Preferences → Add-ons**
   - Click **"Install..."** (or "Install from Disk" in Blender 4.x)
   - Navigate to and select the `addon.py` file
   - **Enable** the addon by checking the box next to **"Interface: Blender MCP"**

4. **Verify it's working:**
   - In the 3D Viewport, press **`N`** to open the sidebar
   - Look for the **"BlenderMCP"** tab
   - You should see connection options and a "Start MCP Server" or "Connect" button

### Telemetry note

By default, the addon collects anonymized usage data. You can disable this:

- In Blender: **Edit → Preferences → Add-ons → Blender MCP** → uncheck the telemetry consent checkbox
- Or via environment variable (see the Claude Desktop config section below)

---

## Part 3 — Configure Your MCP Client

Pick the client you want to use. The three main options are Claude Desktop, Cursor, and VS Code.

### Option A: Claude Desktop

This is the most common setup.

1. Open Claude Desktop
2. Go to **Settings → Developer → Edit Config**
3. This opens the file `claude_desktop_config.json`. Add the following:

**macOS / Linux:**

```json
{
  "mcpServers": {
    "blender": {
      "command": "uvx",
      "args": [
        "blender-mcp"
      ]
    }
  }
}
```

**Windows:**

```json
{
  "mcpServers": {
    "blender": {
      "command": "cmd",
      "args": [
        "/c",
        "uvx",
        "blender-mcp"
      ]
    }
  }
}
```

**To disable telemetry**, add an `env` block:

```json
{
  "mcpServers": {
    "blender": {
      "command": "uvx",
      "args": ["blender-mcp"],
      "env": {
        "DISABLE_TELEMETRY": "true"
      }
    }
  }
}
```

4. **Save the file and restart Claude Desktop**

### Option B: Cursor IDE

1. Open Cursor
2. Go to **Settings → MCP → Add Server**
3. Paste the same JSON configuration as above (use the Windows variant if on Windows)
4. Save and restart Cursor

### Option C: VS Code

1. Make sure you have the MCP extension installed
2. VS Code supports a one-click install badge from the GitHub repo, or you can configure manually
3. The configuration follows the same JSON pattern as above

---

## Part 4 — Connect Everything

Now that both pieces are installed, here's the connection sequence:

### Step 1: Start the addon server in Blender

1. Open Blender
2. Press **`N`** to open the sidebar in the 3D Viewport
3. Click the **"BlenderMCP"** tab
4. Click **"Connect to Claude"** (or "Start MCP Server")
5. The addon will start listening on **port 9876** by default

### Step 2: Open your MCP client

- Open **Claude Desktop** (or Cursor/VS Code)
- If configured correctly, you should see a **hammer icon** (🔨) indicating MCP tools are available
- The tools exposed include: `get_scene_info`, `create_object`, `modify_object`, `set_material`, `execute_blender_code`, and more

### Verify the connection

In Claude, try a simple prompt:

> "Get information about the current Blender scene"

If Claude responds with details about your scene (the default cube, camera, light), the connection is working.

---

## Part 5 — Troubleshooting Common Issues

| Problem | Solution |
|---|---|
| **Connection errors** | Make sure the Blender addon server is running first, then open Claude. Do NOT run `uvx blender-mcp` manually in a terminal — Claude handles that. |
| **First command fails** | This is a known quirk. The first command sometimes times out, but subsequent ones work fine. Just try again. |
| **Timeout errors** | Break complex requests into smaller, simpler steps. |
| **No hammer icon in Claude** | Restart Claude Desktop completely. Verify the JSON config file has no syntax errors. |
| **Windows PATH issues** | Make sure `uvx` is on your system PATH. Open a new terminal and run `uvx --version` to verify. |
| **Port conflict** | If something else is using port 9876, you can change it in the addon settings and the server config. |
| **Firewall blocking** | On Windows, add Blender and your MCP client to the Windows Defender exception list. |

**General tip:** If all else fails, restart both Blender (toggle the addon off/on, reconnect) and your MCP client.

---

## Part 6 — Your First Examples

Now for the fun part. Below are practical examples, ordered from simple to complex.

### Example 1: Create a simple object

**Prompt to Claude:**

> "Create a red sphere at the origin"

What happens behind the scenes: Claude calls `create_object` with type `SPHERE`, then calls `set_material` to apply a red diffuse material.

**Try these variations:**

- "Create a blue cube at position (2, 0, 0)"
- "Add a green cylinder with radius 0.5 and height 3 at location (0, 3, 0)"
- "Create a torus and make it gold metallic"

### Example 2: Build a simple scene

**Prompt:**

> "Create a simple table with four legs. The tabletop should be a flattened cube and the legs should be thin cylinders. Make the table brown wood-colored."

Claude will typically break this down into multiple operations: creating the tabletop as a scaled cube, positioning four cylinder legs beneath it, and applying a brown material.

### Example 3: Inspect and modify existing objects

**Prompt:**

> "What objects are currently in the scene? Delete the default cube and move the camera closer to the origin."

Claude uses `get_scene_info` first, then `execute_blender_code` to delete objects and reposition the camera.

### Example 4: Work with materials and lighting

**Prompt:**

> "Add a plane below all objects as a floor. Make it a dark gray matte material. Then add a second point light with warm yellow color above the scene."

### Example 5: Low-poly scene creation

**Prompt:**

> "Create a low-poly landscape scene with:
> - A green terrain plane with some simple hills
> - Three low-poly pine trees at different positions
> - A small blue pond
> - Set the background to a light blue sky color"

This is where Blender MCP really shines — it will generate multiple objects, shape them with vertex manipulation, and compose a full scene.

### Example 6: Use Poly Haven assets (if enabled)

Make sure the Poly Haven checkbox is enabled in the Blender MCP addon settings.

**Prompt:**

> "Create a beach vibe scene using HDRIs, textures, and models like rocks and vegetation from Poly Haven"

Claude will download environment maps, textures, and 3D models from Poly Haven's free library and integrate them into your scene.

### Example 7: Iterative refinement

The real power of Blender MCP is the conversational loop. Start simple and refine:

1. "Create a basic house shape — a cube for the body, a triangular prism for the roof"
2. "Make the walls beige and the roof dark red"
3. "Add a door on the front face — a dark brown rectangle"
4. "Add two windows on the front, one on each side of the door"
5. "Add a chimney on the roof"
6. "Place the house on a green ground plane"

Each prompt builds on the previous scene state.

---

## Part 7 — Tips for Effective Prompting

Getting great results from Blender MCP is partly about how you phrase your requests.

**Be specific about dimensions and positions.** "Create a cube at (2, 0, 1) with scale (0.5, 0.5, 2)" works better than "create a tall thin box somewhere to the right."

**Break complex tasks into steps.** Instead of "create a fully furnished room," start with the room, then add furniture piece by piece. Claude handles step-by-step instructions much more reliably than monolithic requests.

**Reference existing objects by name.** After creating objects, you can say "move the Table_Top up by 0.5 units" or "change the color of Sphere.001 to blue." Use `get_scene_info` to see what's in your scene.

**Use descriptive material language.** "Glossy metallic gold," "matte concrete gray," "translucent glass with slight blue tint" — Claude translates these into appropriate Blender shader node setups.

**Ask Claude to execute Python directly when needed.** For complex operations, you can say "execute Python code in Blender to select all objects and apply a subdivision surface modifier with 2 levels."

**Save your work frequently.** The `execute_blender_code` tool can run arbitrary Python — powerful but potentially destructive. Save your `.blend` file before experimenting with complex operations.

---

## Part 8 — Available MCP Tools Reference

When connected, Claude has access to these tools:

| Tool | Purpose |
|---|---|
| `get_scene_info` | Returns detailed information about all objects, materials, lights, and cameras in the current scene |
| `create_object` | Creates a new mesh primitive (cube, sphere, cylinder, plane, torus, cone, etc.) at a specified location with optional scale |
| `modify_object` | Changes properties of an existing object (location, rotation, scale, visibility) |
| `set_material` | Creates or modifies materials on objects (color, metallic, roughness, etc.) |
| `delete_object` | Removes an object from the scene |
| `execute_blender_code` | Runs arbitrary Python code inside Blender — the most powerful and flexible tool |
| `get_polyhaven_status` | Checks if Poly Haven integration is enabled |
| `search_polyhaven_assets` | Searches the Poly Haven library for HDRIs, textures, or 3D models |
| `download_polyhaven_asset` | Downloads and applies a Poly Haven asset to the scene |

The `execute_blender_code` tool is especially powerful because it lets Claude write and run any valid Blender Python script, enabling operations like adding modifiers, keyframing animations, adjusting render settings, UV mapping, and anything else the `bpy` API supports.

---

## Part 9 — Advanced Workflows

### Combining with other MCP servers

You can run multiple MCP servers simultaneously. For example, combining Blender MCP with a "sequential thinking" server can help Claude plan complex modeling tasks step by step before executing them.

### Reference image to 3D

If your MCP client supports image input, you can share a reference image and ask Claude to recreate it in Blender. For example: "Here's a photo of a cozy cabin. Recreate this scene in Blender using basic shapes."

### Export to other formats

Ask Claude to execute Python code to export your scene:

> "Export the current scene as a GLTF file to my desktop"

Claude will write and execute the appropriate `bpy.ops.export_scene` command.

---

## Part 10 — Using Blender MCP with Local LLMs (Free Alternative)

Claude is powerful but requires a paid subscription. The good news: you can run Blender MCP entirely for free using local models on your own hardware via **Ollama**. This section covers three approaches, with a full step-by-step walkthrough for the most recommended one.

### Understanding Your Options

| Approach | Cost | Hardware Needed | Quality | Setup Difficulty |
|---|---|---|---|---|
| **Blender Open MCP + Ollama** | Free | GPU with 8GB+ VRAM | Good | Medium |
| **Original Blender MCP + Roo Code + Ollama** | Free | GPU with 8GB+ VRAM | Good | Medium |
| **Original Blender MCP + OpenRouter free models** | Free | No GPU needed | Good–Very Good | Easy |

---

### Option A: Blender Open MCP + Ollama (Recommended — Fully Local)

This is a dedicated fork ([github.com/dhakalnirajan/blender-open-mcp](https://github.com/dhakalnirajan/blender-open-mcp)) purpose-built for running Blender MCP with local models through Ollama. Everything runs on your machine — no cloud, no API keys, no cost.

#### Step 1: Install Ollama

Ollama is a tool that makes running LLMs locally as simple as pulling Docker images.

**Windows:**

1. Go to [ollama.com](https://ollama.com) and download the Windows installer
2. Run the installer — it sets up Ollama as a background service
3. Verify it's running by opening PowerShell and typing:

```powershell
ollama --version
```

**macOS:**

```bash
brew install ollama
ollama serve    # Start the service (runs on port 11434)
```

**Linux:**

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama serve
```

#### Step 2: Choose and Download a Model

This is the most important decision. The model must be good at **tool calling** (function calling) and **Python code generation** since it needs to write valid Blender Python scripts.

**Recommended models by hardware:**

| Your GPU VRAM | Recommended Model | Pull Command | Quality |
|---|---|---|---|
| **16GB+** (RTX 4080/4090, etc.) | Qwen 2.5 Coder 32B | `ollama pull qwen2.5-coder:32b` | Excellent — closest to Claude |
| **10–16GB** (RTX 3080/4070, etc.) | Qwen 2.5 Coder 14B | `ollama pull qwen2.5-coder:14b` | Very good |
| **8–10GB** (RTX 3070/4060, etc.) | Qwen 2.5 Coder 7B | `ollama pull qwen2.5-coder:7b` | Good — best bang for buck |
| **6–8GB** (RTX 3060, etc.) | Llama 3.2 3B | `ollama pull llama3.2:3b` | Basic — simple tasks only |

**The sweet spot for most people is Qwen 2.5 Coder 7B.** Run:

```powershell
ollama pull qwen2.5-coder:7b
```

This will download roughly 4.5 GB. Verify it's ready:

```powershell
ollama list
```

You should see `qwen2.5-coder:7b` in the list.

#### Step 3: Clone the Blender Open MCP Repository

Open PowerShell and run:

```powershell
git clone https://github.com/dhakalnirajan/blender-open-mcp.git
cd blender-open-mcp
```

If you don't have Git, download the ZIP from GitHub and extract it.

#### Step 4: Set Up the Python Environment

You already have `uv` installed from the earlier setup. Use it to create an isolated environment:

```powershell
uv venv
.venv\Scripts\activate
uv pip install -e .
```

This installs the Blender Open MCP server and all its dependencies.

#### Step 5: Install the Blender Open MCP Addon

**Important:** This is a *different* addon from the original `blender-mcp` one. If you have the original addon enabled, disable it first to avoid conflicts.

1. Open Blender
2. Go to **Edit → Preferences → Add-ons**
3. Click **"Install..."**
4. Navigate to the `blender-open-mcp` folder you just cloned and select **`addon.py`**
5. Enable the addon by checking the box next to **"Blender MCP"**

#### Step 6: Start Everything (Connection Sequence)

The order matters. Follow this sequence:

**1. Make sure Ollama is running:**

```powershell
ollama serve
```

On Windows, Ollama usually auto-starts as a service. You can verify by running `ollama list` — if it responds, it's running.

**2. Start the Blender Open MCP server:**

Open a new PowerShell window, navigate to your project folder, activate the environment, and start the server:

```powershell
cd blender-open-mcp
.venv\Scripts\activate
blender-mcp --host 127.0.0.1 --port 8001 --ollama-url http://localhost:11434 --ollama-model qwen2.5-coder:7b
```

Replace `qwen2.5-coder:7b` with whatever model you pulled.

**3. Connect in Blender:**

In Blender, press **N** to open the sidebar → click the **"Blender MCP"** tab → click **"Start MCP Server"**

**4. Test it:**

Use the MCP command-line tool or send a prompt through your connected client:

> "Get information about the current scene"

If it returns your scene data, everything is connected.

#### Step 7: Start Creating

Try these prompts:

- "Create a sphere at the origin"
- "Make it red with a glossy material"
- "Add a plane below it as a floor"
- "Add a point light above the scene"

---

### Option B: Original Blender MCP + VS Code + Roo Code + Ollama

If you want to keep using the **original blender-mcp** addon you already have installed and just swap the AI backend, this approach works well.

#### Setup:

1. **Install VS Code** if you don't have it: [code.visualstudio.com](https://code.visualstudio.com)

2. **Install the Roo Code extension** from the VS Code marketplace (search "Roo Code")

3. **Configure Roo Code to use Ollama:**
   - Open Roo Code settings in VS Code
   - Select **"Ollama"** as the API provider
   - Set the Base URL to `http://localhost:11434`
   - Enter your model name (e.g., `qwen2.5-coder:7b`)

4. **Configure the MCP server in VS Code** — add the blender-mcp server config just like you would for Claude Desktop

5. **Start the addon in Blender** (N panel → BlenderMCP → Connect)

6. **Start prompting** in the Roo Code chat interface in VS Code

This way you keep your existing Blender addon setup and just change which brain is driving it.

---

### Option C: Original Blender MCP + OpenRouter Free Models (No GPU Needed)

If your hardware can't handle running models locally, OpenRouter gives you access to free cloud-hosted models. This is a great middle ground — no subscription, no GPU requirement.

#### Setup:

1. Go to [openrouter.ai](https://openrouter.ai) and create a free account
2. Generate an API key in your account settings
3. Install **VS Code** + **Roo Code** extension (or use Cursor)
4. Configure Roo Code with:
   - API provider: **OpenRouter**
   - API key: your OpenRouter key
   - Model: pick a free model like `google/gemini-2.0-flash-thinking-exp:free` or `deepseek/deepseek-r1-distill-llama-70b:free`
5. Add the blender-mcp server config to your VS Code/Cursor MCP settings
6. Connect in Blender and start prompting

The advantage here is that models like Gemini Flash and DeepSeek R1 70B are significantly more capable than what most people can run locally, and they're free on OpenRouter.

---

### Choosing a Local Model — Detailed Comparison

For Blender MCP specifically, the model needs two critical skills: **tool calling** (understanding and responding to MCP tool schemas) and **Blender Python code generation** (writing valid `bpy` API calls). Not all models are equal here.

| Model | Size | Tool Calling | Code Quality | Best For |
|---|---|---|---|---|
| **Qwen 2.5 Coder 32B** | ~18 GB | Excellent | Excellent | Complex scenes, multi-step tasks |
| **Qwen 2.5 Coder 14B** | ~8 GB | Very Good | Very Good | Most tasks with good hardware |
| **Qwen 2.5 Coder 7B** | ~4.5 GB | Good | Good | Best quality-to-size ratio |
| **Qwen3-Coder 35B** | ~20 GB | Excellent | Excellent | Cutting edge, needs top hardware |
| **Devstral Small** | ~4 GB | Good | Good | Multi-step code tasks |
| **Codestral 22B** | ~12 GB | Good | Very Good | Fast prototyping |
| **Llama 3.2 3B** | ~2 GB | Basic | Basic | Very light hardware only |

**General rule:** Bigger models = better results but slower and more VRAM hungry. The Qwen 2.5 Coder family is currently the best for this use case because Alibaba specifically optimized them for tool calling and code generation.

---

### Tips for Getting Good Results with Local Models

Local models are less forgiving than Claude. Follow these tips to get the best experience:

**Be extremely specific.** Instead of "create a table," say "create a cube at (0, 0, 1) with scale (2, 1, 0.1) as the tabletop." Local models struggle with vague or creative instructions.

**One step at a time.** Never ask for a full scene in one prompt. Create one object, position it, then move on. Chain small successes together.

**Use exact Blender terminology.** Say "UV Sphere" not "ball." Say "Principled BSDF" not "shiny material." The closer your language matches Blender's API, the better the generated code.

**Check and correct.** Local models generate invalid code more often. If something fails, rephrase the request more simply or break it into smaller parts.

**Start with the 7B model, upgrade if needed.** The 7B Qwen Coder handles 80% of basic tasks well. Only invest in downloading a 32B model if you find the 7B too limiting for your workflow.

**Keep Blender's console open.** In Blender, go to **Window → Toggle System Console** (Windows) to see Python errors in real time. This helps you understand what went wrong and rephrase your prompt.

---

### Local LLM Quick-Start Checklist

- [ ] Install **Ollama** from [ollama.com](https://ollama.com)
- [ ] Verify: `ollama --version` returns a version number
- [ ] Pull a model: `ollama pull qwen2.5-coder:7b`
- [ ] Verify: `ollama list` shows the model
- [ ] Clone **blender-open-mcp**: `git clone https://github.com/dhakalnirajan/blender-open-mcp.git`
- [ ] Set up the environment: `uv venv` → activate → `uv pip install -e .`
- [ ] Install the **blender-open-mcp addon** in Blender (not the original one)
- [ ] Start Ollama (`ollama serve` or verify it's running)
- [ ] Start the server: `blender-mcp --host 127.0.0.1 --port 8001 --ollama-url http://localhost:11434 --ollama-model qwen2.5-coder:7b`
- [ ] In Blender: Press **N** → BlenderMCP tab → **Start MCP Server**
- [ ] Test: Send "Get information about the current scene"
- [ ] Start creating!

---

## Quick-Start Checklist (Claude Desktop)

Here's a summary of the Claude Desktop setup from Parts 1–4:

- [ ] Install **Blender** (3.6 or newer recommended)
- [ ] Install **uv** (`brew install uv` / PowerShell script / curl script)
- [ ] Verify: `uvx --version` returns a version number
- [ ] Download **addon.py** from the GitHub repo
- [ ] In Blender: **Edit → Preferences → Add-ons → Install** → select `addon.py` → enable it
- [ ] In Blender: Press **N** → BlenderMCP tab → click **Connect**
- [ ] Configure your MCP client (Claude Desktop / Cursor / VS Code) with the JSON config
- [ ] On Windows: use the full path to `uvx.exe` in the config (find it with `Get-Command uvx` in PowerShell)
- [ ] Restart your MCP client
- [ ] Test: Start a new conversation and type "Get information about the current Blender scene"
- [ ] Create your first object: "Create a red sphere at the origin"
- [ ] Start building scenes and experimenting!

---

## Resources

- **Official Blender MCP:** [github.com/ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp)
- **Blender Open MCP (local models):** [github.com/dhakalnirajan/blender-open-mcp](https://github.com/dhakalnirajan/blender-open-mcp)
- **Ollama:** [ollama.com](https://ollama.com)
- **OpenRouter (free cloud models):** [openrouter.ai](https://openrouter.ai)
- **Roo Code (VS Code extension):** Search "Roo Code" in VS Code marketplace
- **PyPI package:** [pypi.org/project/blender-mcp](https://pypi.org/project/blender-mcp/)
- **uv installer:** [docs.astral.sh/uv](https://docs.astral.sh/uv/)
- **Claude Desktop:** [claude.ai](https://claude.ai)
- **Poly Haven (free assets):** [polyhaven.com](https://polyhaven.com/)

---

*This tutorial was written on February 15, 2026, based on blender-mcp v1.5.x and blender-open-mcp. Check the respective GitHub repos for the latest updates and features.*