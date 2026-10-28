# Gemini 4 Argon Quickstart

Minimal examples for calling Gemini 4 Argon through CometAPI using a Gemini-compatible API.

## Before you start

Check CometAPI’s current documentation to confirm model availability, the model ID, supported features, and pricing.

These examples use:

- Model ID: `gemini-4-argon`
- Endpoint: `https://api.cometapi.com/v1beta/models/gemini-4-argon:generateContent`
- Authentication header: `x-goog-api-key`

## Setup

Set your CometAPI key as an environment variable:

```bash
export COMETAPI_KEY="your_key_here"
```

Keep your API key private. Don’t commit it to your repository.

## cURL

```bash
curl --fail-with-body \
  "https://api.cometapi.com/v1beta/models/gemini-4-argon:generateContent" \
  -H "x-goog-api-key: $COMETAPI_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "parts": [
          {"text": "Explain what an API is in one sentence."}
        ]
      }
    ],
    "generationConfig": {
      "maxOutputTokens": 256
    }
  }'
```

## Python

Install the dependency:

```bash
python -m pip install requests
```

Run:

```python
import os

import requests

response = requests.post(
    "https://api.cometapi.com/v1beta/models/gemini-4-argon:generateContent",
    headers={
        "x-goog-api-key": os.environ["COMETAPI_KEY"],
        "Content-Type": "application/json",
    },
    json={
        "contents": [
            {
                "parts": [
                    {"text": "Explain what an API is in one sentence."}
                ]
            }
        ],
        "generationConfig": {"maxOutputTokens": 256},
    },
    timeout=120,
)
response.raise_for_status()

data = response.json()
candidates = data.get("candidates", [])

if not candidates:
    raise RuntimeError(f"No candidates returned: {data}")

parts = candidates[0].get("content", {}).get("parts", [])
text = "".join(part.get("text", "") for part in parts)

if not text:
    raise RuntimeError(f"No text returned: {data}")

print(text)
```

## JavaScript (Node.js 18+)

Save this example as `quickstart.mjs`, then run `node quickstart.mjs`.

```javascript
const apiKey = process.env.COMETAPI_KEY;

if (!apiKey) {
  throw new Error("Set the COMETAPI_KEY environment variable first.");
}

const response = await fetch(
  "https://api.cometapi.com/v1beta/models/gemini-4-argon:generateContent",
  {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "x-goog-api-key": apiKey,
    },
    body: JSON.stringify({
      contents: [
        {
          parts: [
            { text: "Explain what an API is in one sentence." },
          ],
        },
      ],
      generationConfig: {
        maxOutputTokens: 256,
      },
    }),
    signal: AbortSignal.timeout(120_000),
  }
);

if (!response.ok) {
  throw new Error(
    `Request failed (${response.status}): ${await response.text()}`
  );
}

const data = await response.json();
const candidate = data.candidates?.[0];

if (!candidate) {
  throw new Error(`No candidates returned: ${JSON.stringify(data)}`);
}

const text = (candidate.content?.parts ?? [])
  .map((part) => part.text ?? "")
  .join("");

if (!text) {
  throw new Error(`No text returned: ${JSON.stringify(data)}`);
}

console.log(text);
```

## Cost tips

- Set an output token limit appropriate for your task.
- Check the provider’s caching requirements before assuming repeated prompts qualify for discounted reads.
- Use a smaller model for routine tasks when it meets your quality requirements.
- Monitor actual usage and billing, especially for long inputs and outputs.

## Benchmarks and limitations

Vendor-published benchmarks can provide useful context, but test the model on your own workload before relying on it.

Confirm the following in the current documentation:

- Context window and maximum output length
- Supported input formats
- Availability and access restrictions
- Rate limits and timeout behavior
- Pricing, caching eligibility, and any additional charges

## Links

- [CometAPI model page](https://www.cometapi.com/models/google/gemini-4-argon/)
- [CometAPI documentation](https://apidoc.cometapi.com)

## License

MIT. See [LICENSE](LICENSE) for the full license text.
