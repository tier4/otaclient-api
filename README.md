# otaclient-api

OTA (Over-the-Air) Client Service API definitions for vehicle ECU software updates.

## Overview

This is the API definition for [ota-client](https://github.com/tier4/ota-client).

## Installation

### From source

```shell
pip install git+https://github.com/tier4/otaclient-api.git
```

## Building from source

1. Install build dependencies:

```shell
pip install -U pip build
```

2. Build the wheel package:

```shell
python -m build --wheel
```

3. The built package will be placed in the `./dist` directory.

## Generating Documentation

Generate API documentation from proto files using Docker:

```shell
docker run --rm \
  -v $(pwd)/docs:/out \
  -v $(pwd)/proto:/protos \
  pseudomuto/protoc-gen-doc \
  --doc_opt=markdown,SERVICES.md
```

The generated documentation will be placed in `./docs/SERVICES.md`.

## Usage

```python
import grpc
from otaclient_pb2.v2 import otaclient_v2_pb2, otaclient_v2_pb2_grpc

# Create a gRPC channel
channel = grpc.insecure_channel('localhost:50051')

# Create a stub (client)
stub = otaclient_v2_pb2_grpc.OtaClientServiceStub(channel)

# Make a request
response = stub.Status(otaclient_v2_pb2.StatusRequest())
```

## Requirements

- Python >= 3.8

## License

Apache License 2.0 - see [LICENSE](LICENSE) for details.
