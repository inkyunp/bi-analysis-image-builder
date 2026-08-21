# BI Analysis Image Builder

A Codex skill for designing, building, extending, and validating reproducible container images for bioinformatics analysis pipelines.

It supports Docker/OCI and Singularity/Apptainer workflows, existing image extensions, new image builds, environment specifications, tool installation, and pipeline smoke tests.

## Key behavior

- Interviews the user before inspecting files or running tools.
- Always asks for the desired deliverable; Docker is the default.
- Accepts Dockerfiles, Apptainer/Singularity definition files, Conda environments, Python and R lockfiles, package lists, pipeline scripts, and test inputs.
- Preserves user-supplied versions and unrelated tools when extending an image.
- Keeps full build logs in files and reports concise progress.
- Separates image build success from tool and pipeline validation.

## Validation statuses

- `image_build`: the image artifact was built successfully.
- `tool_validation`: requested tool versions, CLIs, and core APIs passed.
- `pipeline_validation`: a supplied pipeline or representative smoke test passed. It is reported as `not_run` when no pipeline test is available.

A successful image build is never treated as proof that the analysis pipeline works.

## Install

Copy this repository into your personal Codex skills directory:

```bash
git clone https://github.com/inkyunp/bi-analysis-image-builder.git ~/.codex/skills/bi-analysis-image-builder
```

## Use

Invoke the skill in Codex:

```text
$bi-analysis-image-builder
```

Then describe the analysis purpose, required tools, target platform, desired image format, and any pipeline script or test data available for validation.
