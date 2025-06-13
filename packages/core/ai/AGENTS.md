## 🎯 Purpose
Translate raw media-item data into **deletability scores** and human-readable reasons.

## 📐 Required JSON Schema
The OpenAI completion **must** return an array of:
```json
[
  {
    "id": 123,
    "score": 0.12,
    "reason": "Duplicate 4K remux; users watched 100 %"
  }
]
````

* `score` range 0–1 (lower = safer to delete)
* Always valid JSON – use `response_format: { type: "json_object" }`.

## 📝 Prompt Contract

* System message lives in `src/prompt.ts`; keep it < 8 k tokens.
* Few-shot examples under `tests/fixtures/`.
* When editing the system prompt **do not** change its title line (`"You are a media-server cleanup assistant ..."`)—integration tests depend on it.

## 🔬 Tests

* Unit: `src/*.test.ts`
* Integration: mocked OpenAI in `tests/integration/`.
* Add a snapshot test when you modify few-shots.

## ⛑️ Safety

* Flag any item with `size > 50 GB` and `plays === 0` but **never** score it lower than 0.15 unless `duplicate === true`.
* If the model returns malformed JSON: throw `MalformedCompletionError`—executor will retry.

## 🚫 Forbidden Changes in This Folder

* Do not hard-code API keys or URLs.
* Do not import from `process.env` directly—use the injected `Config` object.

## 🔄 Local Dev Shortcut

```bash
yarn workspace @core/ai test --watch
```

Run this before committing AI prompt tweaks.
