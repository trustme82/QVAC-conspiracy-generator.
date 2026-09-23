# QVAC Conspiracy Generator

Pick a mundane object — the office stapler, garden gnomes, vending
machines — and an on-device AI reveals an obviously absurd, clearly
fictional "conspiracy theory" about it, purely for laughs. Entirely on
your machine with [Tether's QVAC SDK](https://github.com/tetherto/qvac).
No cloud call, no API key, no bill. **100% satire — see the safety
note below.**

It calls the QVAC SDK's `loadModel()`, `unloadModel()`, and
`completion()` functions directly. The topic is **deliberately not a
free-text field** — it's restricted to a fixed list of ten silly,
harmless objects, both in the UI dropdown and re-validated
server-side, so the app has no way to be pointed at a real-world
sensitive topic (politics, health, tragedies, real people). The system
prompt additionally instructs the model to keep every theory
impossible-sounding and free of real references. A deterministic
"absurdity meter" scores the output from playful keyword presence —
flavor only.

## What it does

```bash
npm run gui
```

Pick an object from the dropdown, click "Reveal the Truth," and get a
short, silly fictional theory plus an absurdity score.

## SDK version

Built against `@qvac/sdk` **v0.19.1** (see [package.json](package.json)).

## Requirements

- Node.js `>= 22.17`
- A machine that meets [QVAC's system requirements](https://docs.qvac.tether.io/system-requirements)
- ~780 MB free disk space for the LLM weights on first run

## Install

```bash
npm install
```

## GUI mode

```bash
npm run gui
```

Loads the model once at startup, then starts a local server
(`http://localhost:29317` by default, override with `PORT=8080 npm run gui`).

## How it uses QVAC

```js
import { loadModel, completion, LLAMA_3_2_1B_INST_Q4_0 } from "@qvac/sdk";

const modelId = await loadModel({ modelSrc: LLAMA_3_2_1B_INST_Q4_0 });

const run = completion({
  modelId,
  history: [
    { role: "system", content: `Write one obviously absurd, silly, CLEARLY FICTIONAL conspiracy theory about ${object}... never referencing real people, real events, politics, or anything sensitive.` },
    { role: "user", content: `Topic: ${object}` },
  ],
  stream: true,
  completionOpts: { temperature: 0.95, maxTokens: 150 },
});
```

See [src/conspiracy.js](src/conspiracy.js) for the full implementation.

## Why I built this — and the safety design behind it

"Conspiracy theory" is a genre that's genuinely funny in fiction and
genuinely harmful when pointed at real events or people. Rather than
trying to filter that risk out of a free-text field after the fact,
this app removes the risk at the input layer entirely: there is no way
for a user (or a scripted request) to submit an arbitrary topic. The
server re-checks the submitted object against the same fixed whitelist
the dropdown uses, so even a direct API call can't bypass the UI
restriction. This is a different safety strategy than the deny-list
approach used in this series's Roast Me app — an allow-list at the
input boundary, rather than a block-list on the output.

## Notes on this build

This app was built and scaffolded in this session but not run
end-to-end yet — no "verified output" section is included here on
purpose, to avoid claiming a test that didn't happen. The design
mirrors patterns (streaming completion, deterministic scoring)
already verified working in this series's other apps, with the input
allow-list as this app's specific safety addition.

## License

[MIT](LICENSE)
# QVAC-conspiracy-generator.
