# Examples — Tech Doc Agent

Positive and negative examples for the documentation behaviors most subject
to inconsistency in practice.

---

## 9.1 Function Documentation — API Reference Voice

**✅ Correct (engineering audience, precise, grounded):**

```markdown
### `get_stream(stream_id: str) -> Stream`

Returns the metadata record for a single stream. Raises `StreamNotFoundError`
if `stream_id` does not exist for the authenticated API key.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `stream_id` | `str` | Yes | Unique stream identifier, prefixed `stm_`. |

**Returns:** `Stream` — dataclass with `id`, `title`, `status` (`"live"`, `"idle"`, `"error"`),
`created_at` (UTC datetime), and `viewer_count` (int, live streams only; `None` otherwise).

**Raises:**
| Exception | Condition |
|-----------|-----------|
| `StreamNotFoundError` | `stream_id` does not match any stream owned by the API key. |
| `AuthenticationError` | API key is missing, malformed, or revoked. |

**Example:**
```python
stream = client.get_stream("stm_9fXz2k")
print(f"{stream.title} is {stream.status} with {stream.viewer_count} viewers")
```
```

**❌ Incorrect (vague description, no error docs, toy example):**

```markdown
### get_stream

This function gets a stream. It takes an ID and returns some information about it.
You can use it like this:

```python
result = client.get_stream("test")
print(result)
```
```

---

## 9.2 Voice — Customer Integration Guide vs Internal API Reference

The same feature must be described differently depending on audience.

**✅ Customer integration guide voice** (external developer integrating the library):

```markdown
## Handling Sign-In Errors

When sign-in fails, `TokenManager.signIn()` throws an `AuthError`. Your app should
handle each case explicitly rather than catching the base error type.

The most common failure in production is `.networkUnavailable` — this happens when the
user has no connectivity and should surface a retry prompt, not an error message.
`.invalidCredentials` means the user typed the wrong password; prompt them to re-enter
it or use "Forgot password".

```swift
do {
    let session = try await tokenManager.signIn(with: credentials)
    navigateToDashboard(session: session)
} catch AuthError.invalidCredentials {
    showAlert("Incorrect email or password. Please try again.")
} catch AuthError.networkUnavailable {
    showRetryPrompt("No internet connection. Check your network and try again.")
} catch AuthError.accountSuspended {
    showAlert("Your account has been suspended. Contact support.")
}
```
```

**❌ Internal API reference voice** (wrong for a customer-facing guide):

```markdown
## AuthError Enumeration

`AuthError` is a Swift enum conforming to `LocalizedError`. It is defined in
`Sources/MediaAuth/AuthError.swift`. Cases: `.invalidCredentials`,
`.networkUnavailable`, `.tokenExpired`, `.accountSuspended`, `.serverError(Int)`,
`.unknown`. The `errorDescription` computed property returns a localized string
for each case using `NSLocalizedString`.
```

The internal reference form is correct for an SDK API reference doc but wrong for a
customer integration guide. The customer guide must explain what to do, not what the
enum is.

---

## 9.3 Onboarding Guide — Opening Orientation

A getting-started guide for a new engineer must orient before it instructs.

**✅ Correct (explains what and why, then how):**

```markdown
# Media Processor — Getting Started

The media processor is the transcoding backbone of the platform. When a video is
uploaded anywhere in the system — CMS, mobile apps, or the ingest API — it ends up
here. The processor picks it up from a Redis queue, runs it through FFmpeg, and
deposits the output (HLS segments + manifest) into S3.

If a transcode fails, the processor retries up to three times with exponential
backoff and then marks the job `failed`. The original file is never deleted.
Understanding this lifecycle is important before you touch any of the three
main modules, because bugs here affect every video on the platform.

## Running It Locally

Prerequisites: Node.js 20+, Redis 7.x, and FFmpeg 6.x on your PATH.
```

**❌ Incorrect (starts with setup before explaining context):**

```markdown
# Getting Started

## Installation

Run `npm install` to install dependencies.

## Environment Variables

Set `REDIS_URL` to your Redis connection string.
Set `AWS_REGION` to the AWS region.
```

The incorrect form is a setup checklist, not an orientation. A new engineer following
it will complete setup without understanding what they are setting up or why it matters.
