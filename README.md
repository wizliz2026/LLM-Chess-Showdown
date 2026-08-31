# Chess AI Showdown

Simulates and analyzes chess games between two LLMs (OpenAI's gpt-4-turbo and Anthropic's claude-sonnet-4-5-20250929) to determine which plays better chess. Reorganized as a portfolio project, with some outdated code and known quirks intentionally left in place as a record of the original work.

The idea started after noticing ChatGPT's inconsistent play in a casual game of tic-tac-toe, which raised the question of whether it could hold up in a more complex game like chess — and whether two different AI models could be pitted against each other to compare.


## Data Quality Note

While preparing this repo for GitHub, I found that the "Illegal Moves by AI" figures in `DATASHEET.md` (210/192/223/190) don't actually measure illegal moves. `failed_player_move` is a game-level field (the move that triggered a three-strike forfeit), but it got broadcast onto every move row for that game during flattening — and since every game in this dataset ended in forfeit, that field is non-null for 100% of rows. As a result, those numbers are really just total moves made by each color, not illegal move attempts.

The actual per-attempt data does exist in `raw_data.json`'s `ai_messages_for_turn` field for any move that succeeded after a retry — but the fatal turn of each forfeited game was never individually recorded (only the final failed move survives, as `failed_player_move`), so a fully accurate illegal-move count isn't recoverable from this dataset as collected.

The original DATASHEET numbers are left unchanged to preserve the project as it was submitted.


## Structure

chess-ai-showdown/
├── .env.example
├── .gitignore
├── README.md
├── DATASHEET.md
├── requirements.txt
├── notebooks/
│ ├── 01_data_collection.ipynb
│ └── 02_data_processing.ipynb
├── data/
│ ├── raw_data.json
│ └── dataset.csv
└── docs/
└── final_report.pdf


## Setup

1. `pip install -r requirements.txt`
2. Copy `.env.example` to `.env` and add your own `OPENAI_API_KEY` and `ANTHROPIC_API_KEY`.
3. **Working directory:** paths in both notebooks assume they're run from inside `notebooks/`, which is the default when opening a notebook file directly in Jupyter/VS Code. If your environment launches Jupyter from the repo root instead, these paths will fail — run `print(os.getcwd())` in a scratch cell to check, and adjust the `DATA_DIR` path in each notebook's Constants cell if needed.

## 01_data_collection.ipynb

Simulates chess games between the two AIs via API calls, validating moves and logging game data to `data/raw_data.json`. Each AI is given a system prompt instructing it to respond with a single UCI move, optionally followed by a one-line comment, e.g.:

> You are a chess engine playing as White. Your response must begin with a single, valid, and legal UCI chess move for White on the current board (e.g., "e2e4"). Optionally, you may follow the move with a brief comment about your move on a new line, starting with "Comment: ". DO NOT share your strategy.

To change which models play, edit `WHITE_AI_MODEL` / `BLACK_AI_MODEL` in the Constants cell.

On running: you'll be prompted for how many games to play and whether to swap sides after a win/draw.

## 02_data_processing.ipynb

Cleans and flattens `data/raw_data.json` into `data/dataset.csv`, then analyzes win/loss records, illegal move counts, median move time, and favorite opening moves.

## Findings

Claude-sonnet-4-5-20250929 won the large majority of games against gpt-4-turbo. Both AIs also frequently attempted illegal moves — often while contesting the center of the board — which needed retry logic and explicit "you lost due to an illegal move" feedback between games. See `docs/final_report.pdf` for the full write-up and discussion.

## Known Limitations & Notes

- **Prompt design:** the final prompt went through many rounds of iteration and ended up more over-engineered than it needed to be to reliably get a parseable UCI move — the models tended to explain their reasoning in prose rather than sticking to the expected format &mdash; which makes sence given the AIs are large language models.
- **Rate limits** are tied to both model choice and your API usage tier/plan — the hardcoded RPM/TPM values in the Rate Limit Handling section reflect the original account this project ran under, not a universal constant. Changing models or accounts requires updating those values. Next time, I'd have the AI models and their rate limits be user-input rather than hardcoded.
- **`test_api_connections()`** hardcodes model names separately from the `WHITE_AI_MODEL` / `BLACK_AI_MODEL` constants, so changing the constants won't update the connection test. Next time, I'd have this function reference those constants directly instead of hardcoding its own copy of the model names.
- **`if __name__ == "__main__":`** at the end of `01_data_collection.ipynb` is a holdover from the project's original script-style structure — it's a no-op in a notebook context.
- **WB_swap win/loss counting** assumes no draws occurred (true for this dataset) — a draw would be miscounted as a loss for both AIs.
- **Total Illegal Moves cell** in `02_data_processing.ipynb` was originally misplaced earlier in the notebook and referenced undefined variable names — it's been moved and corrected in this version.
- **Ethical use:** this data reflects each AI's behavior under specific models, prompts, and points in time — not a permanent or general measure of either AI's chess ability. Respect both providers' API terms of service, and avoid using this data to make misleading claims about either AI's capabilities.
- **Illegal move counts:** the "Illegal Moves by AI" figures in `DATASHEET.md` (210/192/223/190) don't actually measure illegal moves. `failed_player_move` is a game-level field (the move that triggered a three-strike forfeit), but it got broadcast onto every move row for that game during flattening — and since every game in this dataset ended in forfeit, that field is non-null for 100% of rows. As a result, those numbers are really just total moves made by each color, not illegal move attempts. I noticed this while preparing the repo for GitHub and left the original DATASHEET numbers as-is to preserve the project as it was submitted, rather than retroactively "fixing" the analysis.
- **Recovering the real count:** the actual per-attempt data does exist in `raw_data.json`'s `ai_messages_for_turn` field for any move that succeeded after a retry — but the fatal turn of each forfeited game was never individually recorded (only the final failed move survives, as `failed_player_move`), so a fully accurate illegal-move count isn't recoverable from this dataset as collected.


## What I'd Do Differently

- Have the AI models and their rate limits be user-input rather than hardcoded.
- Have `test_api_connections()` reference the `WHITE_AI_MODEL` / `BLACK_AI_MODEL` constants directly instead of hardcoding its own copy of the model names.
- Use a less restrictive prompt — the heavy prompt engineering likely shaped the AIs' behavior more than intended, rather than just getting them to output a clean UCI move.
- Design a clearer, flatter JSON schema from the start — or maybe multiple JSON files rather than forcing all the API data into one, since I was making calls to two different APIs with two different response formats. A tool like LangChain may have helped standardize that.