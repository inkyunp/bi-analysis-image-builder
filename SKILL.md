---
name: bi-analysis-image-builder
description: Design, build, extend, and validate reproducible container images for bioinformatics analysis pipelines. Use for Docker/OCI or Singularity/Apptainer images, environment specifications, tool installation, and pipeline runtime validation; not for interpreting biological results.
---

# BI Analysis Image Builder

Build the smallest reproducible image that satisfies the user's bioinformatics workflow. Support either extending an existing image or creating a new one. Treat the user's files and version constraints as authoritative.

## Interview before inspection or execution

Before calling tools, ask the user one compact questionnaire and wait for the answer. Reuse information already supplied, but always ask them to confirm the desired deliverable; default to Docker when omitted.

Ask:

- What analysis or pipeline will the image run?
- Which core tools and versions must be installed?
- Should an existing image be extended, or should a new image be built?
- What deliverable is required: Docker/OCI image, Dockerfile, Singularity/Apptainer SIF, definition file, or another artifact? State that the default is Docker.
- Is the target CPU or GPU, and where will it run?
- Is there a pipeline script, test command, or small test dataset for validation?
- Are there existing specifications or lockfiles to use?

Accept inputs such as an existing image or image path, Dockerfile, Compose file, Singularity/Apptainer `.def`, Conda `environment.yml` or explicit spec, `requirements.txt`, `pyproject.toml`, Python lockfiles, `renv.lock`, R `DESCRIPTION`, package lists, installation scripts, pipeline scripts, and test inputs.

If answers are incomplete, infer from supplied artifacts and apply these defaults:

- Deliverable: Docker image.
- Existing usable image supplied: extend it; otherwise create a new image.
- Platform: CPU and the current host architecture.
- Versions: honor supplied pins; otherwise choose compatible stable releases and record the resolved versions.
- Sources: prefer official projects and package channels.
- Scope: install only requested tools and required dependencies.
- No pipeline test supplied: run tool-level smoke tests only and mark pipeline validation `not_run`.

Do not guess when a missing choice materially changes compatibility, cost, security, GPU support, or the deliverable. Ask again.

## Plan from the supplied contract

- If extending an image, inspect its OS, architecture, runtimes, package managers, installed tools, entrypoints, wrappers, and relevant manifests before changing it.
- If building anew, select a minimal compatible base from the user's runtime and tool requirements.
- Prefer user-provided Dockerfiles, definition files, environment files, lockfiles, and version lists over reconstructing environments manually.
- Check compatibility across OS libraries, architecture, CPU/GPU runtime, R, Python, Java, CUDA, and the requested tools.
- Isolate conflicting packages with separate environments, libraries, or explicit wrappers when needed.
- Record the base image, resolved versions, source URLs or channels, commit/tag where relevant, and checksums for downloaded artifacts when practical.
- Preserve unrelated tools and versions when extending an existing image.

## Probe before a full build

Use the cheapest relevant checks first: configuration syntax, lock/spec consistency, source/archive layout, checksums, dependency resolution, and a temporary install or API probe when compatibility is uncertain. Do not start an expensive full build while a cheaper decisive check is failing.

## Build with bounded logs

- Store complete build and validation logs in files.
- Report concise progress; on failure inspect only the smallest useful tail, normally 120-150 lines.
- Diagnose the first actionable failure and make one targeted correction at a time.
- Avoid rebuilding when a metadata, checksum, or publication step can be repaired independently.
- Do not change unrequested core runtimes or tools merely to make a build pass.

## Validate the requested contract

Validate every user-named core tool with its installed version and a meaningful CLI command or core API call. Also validate explicit user requirements, entrypoints, wrappers, environment isolation, and requested CPU/GPU behavior.

When a pipeline script or test input is supplied, run the smallest representative pipeline smoke test that can establish runtime integration. Do not interpret its biological result unless separately requested.

Keep these outcomes separate:

- `image_build`: whether the image artifact was built successfully.
- `tool_validation`: whether requested tools, versions, CLIs, and core APIs passed.
- `pipeline_validation`: whether a supplied real pipeline or representative smoke test passed; use `not_run` when none was supplied or execution was unavailable.

Never report a successful image build as proof that the analysis pipeline works.

## Report

Return a compact result containing:

- Deliverable type and artifact path or image tag.
- Base image, platform, and major runtimes.
- Requested tools with resolved versions.
- Separate `image_build`, `tool_validation`, and `pipeline_validation` statuses and evidence.
- Configuration/lock files needed to reproduce the image.
- Image digest or checksum when available.
- Known compatibility caveats and anything not run.
