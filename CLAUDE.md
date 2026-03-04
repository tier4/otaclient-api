# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

otaclient-api defines the gRPC/Protocol Buffer API for ota-client, software used for over-the-air updates of vehicle ECU (Electronic Control Unit) firmware. The package `otaclient_pb2` provides auto-generated Python bindings from proto definitions. It is maintained by TIER IV, Inc.

## Build Commands

```bash
# Build wheel (generates protobuf Python code via custom Hatchling hook)
pip install -U pip build
python -m build --wheel

# Run pre-commit checks (ruff, black, markdownlint)
pre-commit run --all-files

# Generate API documentation from proto files
docker run --rm \
  -v $(pwd)/docs:/out \
  -v $(pwd)/proto:/protos \
  pseudomuto/protoc-gen-doc \
  --doc_opt=markdown,SERVICES.md
```

There are no tests in this repository — it is a pure API/proto definition package.

## Architecture

- **`proto/otaclient_v2.proto`** — Single proto file defining the `OtaClientService` gRPC service with 5 RPCs (Update, Abort, Rollback, Status, ClientUpdate). This is the source of truth for the API.
- **`src/otaclient_pb2/`** — Python package. The `v2/` subdirectory contains auto-generated protobuf files (`*_pb2.py`, `*_pb2_grpc.py`, `*.pyi`) which are git-ignored and only exist in built wheels.
- **`script/hatch_build.py`** — Custom Hatchling build hook that invokes `grpc_tools.protoc` to compile proto files during `python -m build`. Generates Python messages, gRPC stubs, and type stubs.
- **`docs/SERVICES.md`** — Auto-generated API docs from proto files. Updated by CI when proto files change.

## Key Constraints

- **Python 3.8** is the minimum supported version (target for black, pyright).
- **grpcio-tools is pinned to 1.57.x** for Agent compatibility — do not update.
- **Never edit files in `src/otaclient_pb2/v2/`** — they are auto-generated from proto definitions.
- Version is managed dynamically via `hatch-vcs` from git tags.

## Code Style

- **black** (line-length 88, target py38) and **ruff** for Python formatting/linting.
- **markdownlint** for markdown files (`.markdownlint.yaml` config).
- Generated protobuf files are excluded from all linting.
