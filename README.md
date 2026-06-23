# moss-anthropic

Cryptographic signing for Anthropic SDK (Claude) outputs using ML-DSA-44 post-quantum signatures.

[![PyPI](https://img.shields.io/pypi/v/moss-anthropic)](https://pypi.org/project/moss-anthropic/)
[![License](https://img.shields.io/badge/license-BSL--1.1-blue)](LICENSE)

## Overview

moss-anthropic integrates MOSS cryptographic signing into your Anthropic SDK usage. Every tool use, response, and text block gets a tamper-evident signature using ML-DSA-44 (NIST FIPS 204), the post-quantum cryptographic standard. This creates an immutable audit trail for compliance, debugging, and accountability.

## Installation

```bash
pip install moss-anthropic
```

## Quick Start

```python
from anthropic import Anthropic
from moss_anthropic import sign_tool_use, sign_response

client = Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "What's the weather?"}],
    tools=[{"name": "get_weather", "description": "Get weather", ...}]
)

# Sign the full response
result = sign_response(response, agent_id="weather-bot")
print(f"Signed: {result.signature[:20]}...")
```

## Features

- **ML-DSA-44 signatures** - Post-quantum cryptographic standard (NIST FIPS 204)
- **Tool use signing** - Sign every Claude tool use block
- **Response signing** - Sign full Message responses
- **Text signing** - Sign individual text blocks
- **Policy enforcement** - Block high-risk actions with enterprise policies
- **Offline verification** - Verify signatures without network access

## Usage Examples

### Basic Usage

```python
from moss_anthropic import sign_tool_use, sign_response, verify_envelope

# Sign individual tool use blocks
for block in response.content:
    if block.type == "tool_use":
        result = sign_tool_use(block, agent_id="my-bot")

# Verify any envelope
verify_result = verify_envelope(result.envelope)
print(f"Valid: {verify_result.valid}, Subject: {verify_result.subject}")
```

### With Policy Enforcement

```python
import os
os.environ["MOSS_API_KEY"] = "your-api-key"

from moss_anthropic import sign_tool_use

result = sign_tool_use(
    tool_use_block,
    agent_id="finance-bot",
    context={"user_id": "u123"}
)

if result.blocked:
    print(f"Blocked by policy: {result.policy.reason}")
```

## API Reference

| Function | Description |
|----------|-------------|
| `sign_tool_use()` | Sign a tool use block |
| `sign_tool_use_async()` | Async version |
| `sign_response()` | Sign a full Message response |
| `sign_response_async()` | Async version |
| `sign_text()` | Sign a text block |
| `sign_text_async()` | Async version |
| `sign_message()` | Alias for sign_response |
| `sign_message_async()` | Async version |
| `verify_envelope()` | Verify a signed envelope |
| `enterprise_enabled()` | Check if enterprise mode is active |

## Configuration

| Environment Variable | Description |
|---------------------|-------------|
| `MOSS_API_KEY` | API key for enterprise features (policy enforcement, SIEM) |
| `MOSS_API_URL` | Custom API endpoint (default: api.mosscomputing.com) |

## Links

- [Documentation](https://docs.mosscomputing.com/sdks/anthropic)
- [Dashboard](https://app.mosscomputing.com)
- [PyPI](https://pypi.org/project/moss-anthropic/)

## License

Business Source License 1.1 - Production use requires a [MOSS subscription](https://mosscomputing.com/pricing).
