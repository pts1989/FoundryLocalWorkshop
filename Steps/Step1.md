# Phase 1 & 2: The Brain (Setup & Model)

Goal: We need an AI model running on our "Colony Hardware" (your laptop) that doesn't depend on Earth (Internet). We will not just download it; we will optimize it using Microsoft Olive.

## Step 1: Install the Runtime

We need Foundry Local, a tool that creates a local API endpoint compatible with OpenAI's standards.

**Windows:**

```
winget install Microsoft.FoundryLocal
```


**MacOS:**

```
brew tap microsoft/foundrylocal
brew install foundrylocal
```

  *Verification:* Open a new terminal and type foundry --version. You should see the version output.

## Step 2: Install The Optimizer (Olive)

We use **Olive** to make the model smaller and faster (Quantization).

1. Create a folder for your workshop: `mkdir MarsColony && cd MarsColony`

2. Create a virtual environment (Recommended):

```
python -m venv .venv
source .venv/bin/activate  # Mac/Linux
.venv\Scripts\activate     # Windows

```


3. Install Olive and ONNX Runtime:

```
pip install olive-ai[cpu] onnxruntime huggingface_hub
```


## Step 3: Download & Optimize the Model

We will use `Qwen2.5-1.5B-Instruct`. It is a high-performance Small Language Model (SLM) perfect for edge devices.

1. **Run the optimization command:**
*(This downloads the model from Hugging Face, converts it to ONNX, and quantizes it to INT4 precision).*

```
olive auto-opt \
  --model_name_or_path Qwen/Qwen2.5-1.5B-Instruct \
  --output_path models/mars-brain \
  --device cpu \
  --precision int4
```


☕ *Coffee Break:* This process takes about 3-5 minutes depending on your internet and CPU.

## Step 4: Create the Chat Template

Foundry needs to know how to talk to this specific model.

1. Navigate to your new model folder: `models/mars-brain/model` (or similar path created by Olive).

2. Create a file named `inference_model.json` inside that folder.

3. Paste this content:

```
{
  "name": "mars-brain",
  "chat_template": "{% for message in messages %}{{'<|im_start|>' + message['role'] + '\n' + message['content'] + '<|im_end|>\n'}}{% endfor %}{% if add_generation_prompt %}{{ '<|im_start|>assistant\n' }}{% endif %}"
}
```


## Step 5: Ignition

Start the model server.

```
# Run this from your main MarsColony folder
foundry model run models/mars-brain/model
```


**Success Criteria:**
You should see output indicating the server is running at `http://localhost:xxxx/v1`.
*Keep this terminal open! This is your brain.* 
