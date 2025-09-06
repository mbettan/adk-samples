# Persona Ad Gen - AI-Powered Advertising Scene Generator

An intelligent agent that transforms your photos into compelling advertising scenes through persona-driven storytelling.

## Overview

PersonaAd Gen is an ADK-based agent that creates personalized advertising content by combining user personas with creative image generation from existing images using Gemini 2.5 Flash Images (Preview). The agent collects detailed customer insights and transforms uploaded images into multiple advertising scenes tailored to specific target audiences.

### Key Components

- **PersonaAdGenAgent**: Main orchestrator that guides users through a story-driven brief collection process
- **CreativeAgent**: Sub-agent that generates 4 unique advertising scenes based on the collected persona
- **Headline Generator**: Automatically creates compelling headlines based on the persona story

## Features

✨ **Story-Driven Brief Collection**: Instead of traditional forms, build your ad's narrative step-by-step  
🎯 **Persona-Focused**: Centers on understanding your ideal customer's problems and desires  
🖼️ **Multi-Scene Generation**: Creates 4 distinct advertising scenes from a single uploaded image  
📝 **Automatic Headline Creation**: Generates compelling headlines based on your story  
💾 **Artifact Management**: Saves all generated content for easy access and download

## Prerequisites

- Python 3.9 or higher
- Poetry or pip for dependency management
- Google Cloud Project (for Vertex AI) or Google AI Studio API key
- GCS bucket for artifact storage

## Installation

```bash
# Install dependencies using Poetry
poetry install

# Or using pip
pip install google-adk google-genai pydantic python-dotenv google-cloud-aiplatform

# For evaluation capabilities, install with eval extras
pip install "google-adk[eval]"

# If using pipx for ADK installation, inject eval dependencies
pipx inject google-adk pandas tabulate rouge-score
```

## Configuration

### Environment Variables (.env)

Create a `.env` file in the project root with the following variables:

```bash
# Google Cloud Project Configuration
export GOOGLE_CLOUD_PROJECT=your-project-id
export GOOGLE_CLOUD_LOCATION=global

# Artifact Storage Configuration
export ADK_ARTIFACT_SERVICE_TYPE=GCS
export ADK_GCS_BUCKET_NAME=your-bucket-name

# Vertex AI Configuration (optional)
export GOOGLE_GENAI_USE_VERTEXAI=true
```

**Note**: 
- Replace `your-project-id` with your actual Google Cloud project ID
- Replace `your-bucket-name` with your GCS bucket for storing artifacts
- Set `GOOGLE_GENAI_USE_VERTEXAI=true` if using Vertex AI instead of direct Gemini API

## Usage

### Running the Agent

1. **Start the agent using ADK Web**:
```bash
adk web
```

2. **Alternative method** (if using the run script):
```bash
./run_agent.sh
```

3. **Direct Python execution**:
```bash
python -m adk.web_server --app persona_ad_gen
```

2. Follow the workflow:
   - Provide the 6 brief items when prompted:
     - Brand name
     - Product name  
     - Target location
     - Target age group
     - Target gender
     - Target interests
   - Upload a base image when requested
   - Confirm the brief details
   - The agent will generate 4 creative scenes

## Testing & Evaluation

### Running Evaluations

The project includes evaluation datasets to test the agent's responses:

```bash
# Run evaluation tests
adk eval persona_ad_gen eval/data/persona_ad_gen_evalset.test.json

# Run unit tests
pytest eval/test_eval.py
```

### Evaluation Setup

If you encounter issues with evaluation, ensure you have the required dependencies:

1. **For pip installations**:
```bash
pip install "google-adk[eval]"
```

2. **For pipx installations**:
```bash
pipx inject google-adk pandas tabulate rouge-score
```

The evaluation tests verify that the agent provides the correct introduction and follows the expected conversation flow.

## Workflow

1. **Brief Collection**: The main agent collects all necessary information
2. **Image Upload**: User uploads a base image that serves as the foundation
3. **Confirmation**: Agent confirms all details with the user
4. **Scene Generation**: Creative sub-agent creates a 4-scene plan
5. **Image Creation**: Each scene is generated as a new image variation

## Technical Details

### Tools

- `confirm_and_save_brief`: Saves the creative brief to session state
- `save_image_as_artifact`: Processes and saves uploaded images
- `edit_scene_image`: Generates new images based on edit prompts using the uploaded image as source

### Models

- Uses `gemini-2.5-flash-image-preview` for image generation with source image input
- Supports image-to-image editing and text-to-image generation
- Configured with `response_modalities=["TEXT", "IMAGE"]` for both text descriptions and image outputs

### Session State

The agent maintains session state with:
- `confirmed_brief`: The complete creative brief details
- `base_image_filename`: Reference to the uploaded base image

### Viewing Generated Images

Generated images are saved as artifacts and can be accessed through:
1. **ADK Web Interface**: Navigate to the artifacts section in your session
2. **Session Artifacts**: Look for files named like:
   - `scene_1_nyc_power_stance.png`
   - `scene_2_dynamic_city_drive.png`
   - `scene_3_athlete_companion.png`
   - `scene_4_command_the_city.png`
3. **API Response**: Each generation returns the artifact filename for programmatic access

### Image Generation Process

The agent follows this workflow:
1. Loads your uploaded image as the source material
2. Combines it with scene-specific editing prompts
3. Sends both to Gemini 2.5 Flash Image model
4. Processes the response to extract generated images
5. Saves results as downloadable artifacts

## Error Handling

The agent handles:
- Missing image uploads
- Failed image generation attempts
- Invalid brief information
- API errors with graceful fallbacks

## Development

To modify the agent:

1. Edit tool functions in `tools.py`
2. Adjust agent behavior in `agent.py` or `sub_agents/creative_agent.py`
3. Update models in `models.py` if needed
4. Test with `adk web`
5. Run evaluations with `adk eval` to verify changes

## Dependencies

- `google-adk`: Agent Development Kit
- `google-genai`: Gemini AI API client
- `pydantic`: Data validation
- `google-cloud-aiplatform`: Cloud AI platform integration
- `python-dotenv`: Environment variable management

### Evaluation Dependencies (optional)
- `pandas`: Data manipulation for evaluation metrics
- `tabulate`: Formatted output for evaluation results
- `rouge-score`: Text similarity metrics for evaluation

## License

Apache License 2.0
