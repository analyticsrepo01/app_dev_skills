---
name: nanobana
description: High-fidelity image generation using Google Imagen 3 on Vertex AI (codenamed nanobana). Activate this skill when the user asks to generate, edit, or modify images, visual slides, or diagrams using Gemini and Vertex AI.
---

# 🎨 Nanobana: Google Imagen 3 Image Generation Skill

Use this skill when you need to programmatically generate or edit high-fidelity images using Google's **Imagen 3** on Vertex AI.

---

## 1. Prerequisites & Setup

Install the required Google Cloud Vertex AI Python SDK:
```bash
pip install google-cloud-aiplatform
```

Ensure your environment is authenticated with Google Cloud:
```bash
gcloud auth application-default login
```

---

## 2. Programmatic Python Implementation

The following code is a template to load the **Imagen 3** model (`imagen-3.0-generate-002` or equivalent) and generate a high-quality 16:9 widescreen presentation slide graphic.

```python
import os
from vertexai.preview.vision_models import ImageGenerationModel
import vertexai

def generate_nanobana_image(
    prompt: str, 
    output_path: str = "output_slide.png", 
    aspect_ratio: str = "16:9"
):
    # Initialize Vertex AI with environment configurations (no hardcoded credentials)
    project_id = os.getenv("GCP_PROJECT")
    location = os.getenv("GCP_LOCATION", "us-central1")
    
    if not project_id:
        raise ValueError("Please set the 'GCP_PROJECT' environment variable.")
        
    vertexai.init(project=project_id, location=location)

    # Load the Imagen 3 generation model
    model = ImageGenerationModel.from_pretrained("imagen-3.0-generate-002")

    print(f"Sending prompt to Imagen 3: {prompt}")
    
    # Generate image with parameters
    response = model.generate_images(
        prompt=prompt,
        number_of_images=1,
        aspect_ratio=aspect_ratio,
        output_mime_type="image/png",
        person_generation="allow_adult",
    )

    # Save the generated image locally
    if response.images:
        response.images[0].save(location=output_path, include_metadata=True)
        print(f"🎉 Success! Image saved to {output_path}")
    else:
        print("❌ Image generation failed.")

if __name__ == "__main__":
    prompt = (
        "A premium presentation slide, dark mode minimalist design with a "
        "sleek blue-purple gradient background, clean infographics, and high-fidelity typography."
    )
    generate_nanobana_image(prompt=prompt, output_path="slide_preview.png")
```

---

## 3. Vertex AI REST API Endpoint Reference

Alternatively, you can call the Vertex AI endpoint directly via curl:

*   **HTTP Method:** `POST`
*   **Endpoint:** `https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID/locations/us-central1/publishers/google/models/imagen-3.0-generate-002:predict`
*   **Headers:**
    *   `Authorization: Bearer $(gcloud auth print-access-token)`
    *   `Content-Type: application/json`
*   **Payload Template:**
    ```json
    {
      "instances": [
        {
          "prompt": "A premium presentation slide layout, 16:9 widescreen, dark mode, high-fidelity analytics dashboard"
        }
      ],
      "parameters": {
        "sampleCount": 1,
        "aspectRatio": "16:9",
        "outputMimeType": "image/png"
      }
    }
    ```
