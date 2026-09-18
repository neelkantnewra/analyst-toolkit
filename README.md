# analyst-toolkit

Small browser-based tools for data analysts — no backend, no installs, runs entirely client-side. Open the HTML file (or the GitHub Pages link) and use it directly in your browser; nothing you paste in ever leaves your machine.

## Tools

| Tool | Description |
|---|---|
| [snowflake-code-anonymizer](./snowflake-code-anonymizer) | Scans Snowflake SQL for table, schema, database, column, and variable names and swaps them for generic placeholders (`Table_1`, `Variable_2`, etc.) before you paste code into an AI tool. Generates a dictionary so you can restore the real names in the AI's output afterward. |

More tools will be added here over time — each lives in its own folder with a short README.

## Usage

Each tool is a single self-contained `.html` file:

1. Download the file (or clone the repo) and open it in any browser, **or**
2. Use the live version via GitHub Pages: `https://neelkantnewra.github.io/analyst-toolkit/<tool-folder>/`

No build step, no dependencies to install.

## Contributing

Suggestions and pull requests are welcome. If you add a new tool, keep it as a single-file, dependency-free HTML page where possible, and add a row to the table above.

## License

MIT — see [LICENSE](./LICENSE).