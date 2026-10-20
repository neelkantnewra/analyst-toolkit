# slm-dataset-builder

A distraction-free editor for hand-curating **supervised fine-tuning (SFT) datasets** for small language models such as Qwen2.5 / Qwen3, built around a Small and Medium Banking use case. It masks PII as you type and exports to the four most common training schemas.

Runs entirely in your browser. Nothing you type, paste or import leaves your machine.

## How it works

Each entry in the sidebar is one training sample. You write it in one of two modes:

- **Single Q&A:** a customer query (`user`), an optional context / reference policy (`input`), and the ideal assistant response (`assistant`).
- **Multi-turn:** a list of messages. Each turn can be toggled between `user` and `assistant`, moved up or down, or deleted.

Every sample also gets a category (`Retail Banking`, `SME / MSME Credit`, `KYC & Compliance`, `Cards & UPI Disputes`, `General Inquiry`) to help you keep the dataset balanced while you work.

A **global system prompt** (a compliant banking assistant persona by default) can be edited at the top of the editor and included in or excluded from every exported row with the header toggle.

All changes are saved automatically to your browser's `localStorage`.

## Usage

1. Open `index.html` (or the GitHub Pages link).
2. Write a sample. Use the token pills to insert placeholders such as `<ACCOUNT_NO>` at the cursor.
3. Press `Ctrl + Enter` (`Cmd + Enter` on Mac) to save it and start a fresh sample with the cursor already in the first field.
4. Repeat. Use the sidebar search to find samples later.
5. Choose an export format and file type in the header, then click **Download**.

### Keyboard shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl/Cmd + Enter` | Save the current sample and open a new one |
| `Alt + ↑` / `Alt + ↓` | Go to the previous / next sample |

The bottom bar also has **Duplicate Sample**, **Delete Sample** (click twice to confirm) and a **Live JSONL preview** of the active sample in the selected format.

## PII guardrails

Banking data is sensitive, so the editor checks the active sample for raw identifiers as you type. If it finds any, an amber banner lists what it found and offers **Auto-Mask PII**, which replaces every match with a placeholder. A red dot in the sidebar marks samples that still contain unmasked PII.

| Detected | Pattern | Replaced with |
|---|---|---|
| Card number | 16 digits, optional spaces or dashes | `<CARD_NO>` |
| Aadhaar | 12 digits, optional spaces or dashes | `<AADHAAR>` |
| Mobile | 10 digits starting 6-9, optional `+91` | `<MOBILE_NO>` |
| PAN | `AAAAA9999A` | `<PAN>` |
| IFSC | `AAAA0XXXXXX` | `<IFSC>` |
| Account number | 9 to 18 digits | `<ACCOUNT_NO>` |

Patterns are checked in this order, so a number is only counted once (a 16-digit card is not also reported as an account number).

The quick-insert pills also include `<AMOUNT>`, `<CUSTOMER_NAME>` and `<TXN_ID>` for placeholders that are not auto-detected.

## Export formats

Pick the schema in the header, then download as `.jsonl` (one sample per line) or `.json` (a single array).

### 1. OpenAI / Hugging Face messages (default)

For Unsloth and TRL `SFTTrainer`.

```json
{"messages": [{"role": "system", "content": "..."}, {"role": "user", "content": "..."}, {"role": "assistant", "content": "..."}]}
```

### 2. ShareGPT

For LLaMA-Factory and Axolotl.

```json
{"conversations": [{"from": "system", "value": "..."}, {"from": "human", "value": "..."}, {"from": "gpt", "value": "..."}]}
```

### 3. Alpaca

```json
{"instruction": "...", "input": "...", "output": "...", "system": "..."}
```

Multi-turn samples use the last user and assistant messages as `instruction` and `output`, and earlier turns go into a `history` array of `[user, assistant]` pairs.

### 4. Raw Qwen ChatML

```json
{"text": "<|im_start|>system\n...<|im_end|>\n<|im_start|>user\n...<|im_end|>\n<|im_start|>assistant\n...<|im_end|>\n"}
```

### Where the optional context goes

In Single Q&A mode, the context field is added to the user message as `Context:` followed by the text (OpenAI, ShareGPT and ChatML). In Alpaca it goes into the `input` field.

If the system prompt toggle is off, the system message is left out (Alpaca keeps `system` as an empty string).

## Importing

Click **Import** and choose a `.jsonl` or `.json` file in any of the four schemas above. The format is detected automatically and the rows are added to your workspace.

- Rows with exactly one user and one assistant message become Single Q&A samples; anything longer becomes a multi-turn sample.
- Imported samples are given the category `General Inquiry`.
- If the file contains a system message, the first one found replaces the global system prompt.

## Seed data

On first load the tool contains three example samples (an SME overdraft drawing power calculation, a multi-turn UPI fraud dispute, and a fixed deposit premature withdrawal). **Reset Demo Data** restores them and replaces everything else.

The rates, margins and penalties in the examples are illustrative. Replace them with your own bank's policy before training on them.

## Limitations

- Category is a working label only. It is not part of any export schema, so it is lost on export and set to `General Inquiry` on import.
- PII detection is regex-based and will produce false positives (for example, a long reference number may look like an account number) and misses names and free-text identifiers. Always review before training.
- Only samples with both a user and an assistant message are exported. Incomplete ones are skipped, and the export message tells you how many.
- Data lives in one browser's `localStorage`. Clearing site data or switching browsers loses it, so download regularly.
- Needs internet on first load to fetch the Inter and JetBrains Mono fonts. Without it, the system fonts are used.

## Development

It is a single `index.html` with no build step to run. The Tailwind CSS is pre-built and inlined in a `<style>` block, so if you add new Tailwind classes to the HTML, regenerate the CSS with the Tailwind CLI (v3.4) or write the new rules as plain CSS.