# Snowflake Code Anonymizer

A browser-based tool that strips company-specific table, schema, database, column, and variable names out of Snowflake SQL before you paste it into an AI tool for documentation, debugging, or review — then restores the real names once you get the AI's output back.

Runs entirely client-side. Nothing you paste in is sent anywhere.

## Why

AI tools are great at generating documentation, explanations, and comments for SQL, but you often can't paste in code that contains real table names, schema names, or business-specific variable names. This tool lets you scrub that out first, work with the AI safely, then map the generic names back to the real ones afterward.

## How it works

1. **Paste your SQL** into the input box and click "Scan for identifiers."
2. The tool detects table, schema, database, column, and variable names (filtering out SQL keywords and built-in functions), and guesses a category for each based on context — e.g. a name after `FROM` or `JOIN` is tagged as a Table, a qualified `db.schema.table` reference is split into Database/Schema/Table correctly.
3. **Review the list**: uncheck anything you don't want changed, fix a category or generic name if needed. You can also add manual find-and-replace pairs for text buried in string literals or comments (e.g. a company name).
4. Click **Generate anonymized code** to get:
   - The anonymized SQL, safe to paste into an AI tool.
   - A dictionary mapping each generic name back to the real one — keep this private, don't paste it into the AI.
5. Once the AI gives you back documentation using the generic names, go to the **Restore** tab, paste that text in, and it swaps the generic names back to your real ones using the saved dictionary.

## Notes

- The same identifier is always mapped to the same generic name throughout the file, even if it appears with different casing (e.g. `customer_id` and `Customer_ID` are treated as the same variable).
- The dictionary is remembered in your browser between sessions (local storage only) so you can come back and restore later. Use "Clear saved dictionary" in the Restore tab to wipe it.
- This is a heuristic scanner, not a full SQL parser — always review the detected identifier list before generating, especially for anything unusual in your code style.

## Usage

Open `index.html` in any browser. No install, no dependencies, no server required.