# Document Intelligence with Nemotron Parse and StepFun

Build a document intelligence workflow that combines **Nemotron Parse**
for page layout extraction with **StepFun Step-3.7 Flash** for cropped
image transcription and final document question answering.

The notebook runs against hosted NVIDIA endpoints. No local GPU, Docker
container, or model weights are required.

## What It Does

The workflow processes four pages from three public PDFs:

1. Nemotron Parse extracts typed layout blocks and picture bounding boxes.
2. StepFun classifies and transcribes each cropped picture.
3. The notebook stitches text and picture transcriptions back into a
   reading-order Markdown context.
4. StepFun answers document-level questions with cited page evidence.

## Models And Endpoints

| Role | Model | Endpoint |
| --- | --- | --- |
| Layout extraction | `nvidia/nemotron-parse` | NVIDIA API Catalog chat completions |
| Picture transcription | `stepfun-ai/step-3.7-flash` | NVIDIA API Catalog chat completions |
| Document QA | `stepfun-ai/step-3.7-flash` | NVIDIA API Catalog chat completions |

The notebook defaults both models to NVIDIA's standard
`https://integrate.api.nvidia.com/v1/chat/completions` endpoint. If
needed, Parse and StepFun can still be pointed at separate endpoints with
the optional `PARSE_CHAT_COMPLETIONS_URL` and
`STEPFUN_CHAT_COMPLETIONS_URL` variables.

## Setup

Install dependencies with `uv`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv sync
```

Create your local `.env`:

```bash
cp .env.example .env
```

Edit `.env` and add your key:

```bash
NVIDIA_API_KEY=nvapi-your-key-here
NVAI_CHAT_COMPLETIONS_URL=https://integrate.api.nvidia.com/v1/chat/completions
STEPFUN_VLM_MODEL=stepfun-ai/step-3.7-flash
```

If Parse and StepFun require different credentials for your account, set
these optional values:

```bash
PARSE_API_KEY=nvapi-your-parse-key-here
STEPFUN_API_KEY=nvapi-your-stepfun-key-here
PARSE_CHAT_COMPLETIONS_URL=https://integrate.api.nvidia.com/v1/chat/completions
STEPFUN_CHAT_COMPLETIONS_URL=https://integrate.api.nvidia.com/v1/chat/completions
```

## Run

```bash
uv run jupyter lab stepfun_doc_intelligence_with_parse.ipynb
```

Run the notebook cells from top to bottom. The `data/documents/` folder
already contains the demo PDFs, so the notebook can start immediately.

## Project Structure

```text
.
├── README.md
├── .env.example
├── pyproject.toml
├── stepfun_doc_intelligence_with_parse.ipynb
└── data/
    └── documents/
        ├── 05-03-18-political-release.pdf
        ├── GPL-Graduate-Studies-Professional-Learning-Brochure-Jul-2021.pdf
        └── measuringsuccessonfacebooktwitterlinkedin-160317142140_95.pdf
```

Running the notebook writes generated `*.parse_stepfun.json` files under
`output_results/`; those artifacts are local run output and are not
required in source control.
