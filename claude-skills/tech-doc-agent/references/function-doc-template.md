# Function Documentation Template

Use this template when formatting each public function or method entry. Document
in the order shown: signature, description, parameters, returns, errors, example.

---

## Template

````markdown
### `issue_playback_token(stream_id: str, viewer_id: str, ttl_seconds: int = 3600, geo_restriction: list | None = None) -> PlaybackToken`

Issues a signed, single-use JWT authorizing playback of one stream.
Tokens expire on first use or after `ttl_seconds`, whichever comes first.

**Parameters:**
| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `stream_id` | `str` | Yes | — | Stream to authorize playback for. |
| `viewer_id` | `str` | Yes | — | Caller-supplied viewer identifier. Max 128 chars. |
| `ttl_seconds` | `int` | No | `3600` | Token validity in seconds. Range: 60–86400. |
| `geo_restriction` | `list[str] \| None` | No | `None` | ISO 3166-1 alpha-2 country codes. `None` = no restriction. |

**Returns:** `PlaybackToken` — contains `token` (signed JWT string) and
`expires_at` (UTC datetime).

**Raises:**
| Exception | Condition |
|-----------|-----------|
| `StreamNotFoundError` | `stream_id` does not exist for this API key. |
| `AuthenticationError` | API key is invalid or revoked. |
| `ValueError` | `ttl_seconds` outside 60–86400, or `viewer_id` exceeds 128 chars. |

**Example:**
```python
token = client.issue_playback_token(
    stream_id="stm_9fXz2k",
    viewer_id="user_42",
    ttl_seconds=1800,
    geo_restriction=["US", "CA", "GB"],
)
print(f"Token expires at {token.expires_at}: {token.token[:20]}...")
```
````

---

## Field Descriptions

**Signature** — Exact as declared in the source language. Do not paraphrase or simplify.

**Description** — One paragraph: what the function does, not how it works internally. Describe side effects, preconditions, and postconditions if present.

**Parameters** — One row per parameter. Include:
- `Name`: exact parameter name as declared
- `Type`: exact type annotation; use `None` not `null` for Python, `nil` for Swift, etc.
- `Required`: Yes / No
- `Default`: exact default value, or `—` if required
- `Description`: constraint or valid range if applicable; do not restate the type

**Returns** — Type and description on one line. Explicitly state null/nil/None semantics when the return can be absent.

**Raises** — One row per error or exception. Include the condition that triggers it, not just the type name. Error documentation is mandatory — an entry without a Raises section is incomplete regardless of the quality of other fields.

**Example** — One minimal, working call with representative inputs and the expected output or side effect. Use realistic values, not `foo`, `bar`, or `test`. If the example cannot be verified as runnable, mark it `# Illustrative — not tested`.

---

## Language Adaptation Notes

This template is language-agnostic. Adapt as follows:

- **Python**: use `| None` union syntax, `Raises:` section header, ` ```python ` fence
- **Swift**: use `throws` annotation, `throws` section header listing error cases, ` ```swift ` fence; parameters table columns stay the same
- **TypeScript/JavaScript**: use TypeScript type annotations where available; `Throws:` or `Rejects with:` for Promise rejections; ` ```typescript ` fence
- **Go**: document named return values; error return in `Returns`; no exceptions — errors are return values
- **Shell/CLI**: replace Parameters with `Options/Flags` table; use ` ```bash ` fence
