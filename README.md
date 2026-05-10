# 🎨 Prompt-Guided Style Transfer

A GenAI image-editing project for prompt-guided style transfer.

The goal was to take a source image and a text prompt, then generate an edited image that keeps the original content while applying the requested style. I built this as a research-style ML project to compare a few latent-space image editing approaches, including diffusion, flow matching, and style-content decoupling.

## 📄 Final Report

The full writeup is included here:

[PromptGuidedStyleTransfer.pdf](PromptGuidedStyleTransfer.pdf)

## 🚦 How to Run

This project was originally trained on an AWS GPU setup, so some paths may need to be updated if you run it locally or on a new machine.

### 1. Clone the repo

```bash
git clone https://github.com/Danny625/Prompt-Guided-Style-Transfer-GenAI.git
cd Prompt-Guided-Style-Transfer-GenAI
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Prepare the data

The training and evaluation scripts expect a metadata CSV with source images, target images, prompts, style labels, and split labels.

Example columns:

```text
id, source_image_path, target_image_path, prompt, style_label, split
```

### 4. Train a model

```bash
python src/train.py \
  --data_path path/to/metadata.csv \
  --output_dir outputs/diffusion \
  --model_type diffusion
```

Other model options:

```bash
--model_type flow
--model_type decouple
```

### 5. Evaluate a model

```bash
python src/eval.py \
  --model-id diffusion \
  --model-dir outputs/diffusion/final_model.pt \
  --metadata-path path/to/metadata.csv \
  --output-dir results/diffusion \
  --split test
```

Evaluation outputs include generated images, comparison grids, metric CSVs, and summary results.

## 📦 Technologies

- Python
- PyTorch
- Hugging Face Diffusers
- Transformers
- Stable Diffusion / InstructPix2Pix
- CLIP, DINO, LPIPS
- Weights & Biases
- AWS GPU training

## 🦄 What This Project Does

### 🖼️ Prompt-Guided Image Editing

Given a source image and an edit prompt, the model tries to generate a styled output while preserving the structure and content of the original image.

Example:

```text
source image + "turn this into a watercolor painting" → styled output image
```

### 🧪 Multiple Model Variants

The project compares different image-editing approaches:

- **Baseline:** InstructPix2Pix-style editing
- **Latent Diffusion:** noise prediction in latent space
- **Latent Flow Matching:** velocity prediction in latent space
- **Style-Content Decoupling:** separates content and style directions more explicitly

### 📊 Evaluation Pipeline

The evaluation script generates outputs, saves comparison grids, and reports metrics for style alignment and content preservation.

Main metrics:

- CLIP text similarity for prompt alignment
- CLIP image similarity for style/source-output similarity
- DINO similarity for content preservation
- LPIPS distance for perceptual change

## 🧠 Project Structure

```text
Prompt-Guided-Style-Transfer-GenAI/
├── README.md
├── requirements.txt
├── LICENSE
├── PromptGuidedStyleTransfer.pdf
└── src/
    ├── train.py          # Main training script
    ├── eval.py           # Evaluation and metric reporting
    ├── run.sh            # Training shell script
    ├── run_eval.sh       # Evaluation shell script
    ├── baseline/         # Baseline editing method
    ├── dataset/          # Dataset loading and preprocessing
    ├── methods/          # Diffusion, flow, and decoupled model code
    └── metrics/          # CLIP, DINO, and LPIPS evaluation logic
```

## 🧩 How It Works

At a high level:

1. The dataset loader reads source images, target images, prompts, and style labels from metadata.
2. The training script loads one of the model variants.
3. The model learns to edit the source image in latent space based on the prompt.
4. During evaluation, the model generates edited outputs for test samples.
5. The evaluation script saves outputs, creates comparison grids, and computes metrics.

## 👩‍🍳 The Process

I started with the basic question: can we change the style of an image from a text prompt without losing the original content?

From there, I built a shared pipeline for loading data, training models, generating samples, and evaluating results. That made it easier to compare multiple methods without rewriting the workflow each time.

The hardest part was balancing style transfer with content preservation. If the model follows the prompt too strongly, it can lose the structure of the original image. If it preserves content too much, the style change becomes weak. A lot of the project came down to testing that tradeoff across model design, loss functions, and inference settings.

## 📚 What I Learned

- How to structure a larger ML project across data, training, evaluation, and model code
- How latent diffusion-style image editing works under the hood
- How flow matching can be used as another image generation objective
- How CLIP, DINO, and LPIPS capture different parts of image-editing quality
- Why generative model evaluation is hard because visual quality, prompt alignment, and content preservation do not always move together

## 💭 How It Can Be Improved

If I were continuing this project, I would probably:

- Add a small sample dataset or example metadata file
- Add example outputs directly in the README
- Make paths less tied to the original AWS/EC2 setup
- Add a simple one-image inference script
- Add a cleaner config system for training and evaluation
- Document each model variant more deeply

## ⚠️ Notes

The full training data and model checkpoints are not included because they are large and environment-specific. To reproduce the full workflow, you will need your own image-prompt metadata file, image folders, and a GPU environment for training.
