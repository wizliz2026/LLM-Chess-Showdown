# DATASHEET

#### Dataset Summary

The dataset gathered is 815 datapoints from 150 chess games between OpenAI's gpt-4-turbo and Anthropic's claude-sonnet-4-5-20250929.

This dataset is designed to be used to determine which AI is smarter, or better at playing chess. The dataset was gathered by having
gpt-4-turbo and claude-sonnet-4-5-20250929 play 150 chess games to win, lose, or draw. 35 data categories were organized to present
815 moves or datapoints from all games played.

#### Dataset Statistics

total moves: 815

75 control games (WB_keep):

White won 18 games (AI: gpt-4-turbo)

Black won 57 games (AI: claude-sonnet-4-5-20250929)

Illegal moves by AI dataset for control:

&nbsp;	White AI playing White: 210

&nbsp;	Black AI playing Black: 192


75 swap games (WB_swap):

Illegal moves by AI for swap dataset:

&nbsp;	White AI playing White: 223

&nbsp;	Black AI playing Black: 190


The most frequent move is: e2e4, played 150 times.

Most favored moves by White AI:

AI: claude-sonnet-4-5-20250929 - Most frequent move: e2e4 (Played 50 times)

AI: gpt-4-turbo - Most frequent move: e2e4 (Played 100 times)

Most favored moves by Black AI:

AI: claude-sonnet-4-5-20250929 - Most frequent move: e7e5 (Played 95 times)

AI: gpt-4-turbo - Most frequent move: e7e5 (Played 47 times)

## Data Fields

dataset: This field differentiates between the two experimental conditions: 'WB\_keep' (control group) and 'WB\_swap' (experimental group).

game\_number: Used to identify individual games and is crucial for grouping game-level statistics.

white\_ai and black\_ai: These fields identify which AI (gpt-4-turbo or claude-sonnet-4-5-20250929) was playing as White and Black, respectively, in each game.

winner\_ai: Indicates the AI that won the game, used to calculate win/loss records.

move\_\_player: Specifies whether a particular move was made by the 'white' player or the 'black' player.

move\_\_time: Records the time taken for each individual move, which was used to calculate the median time per turn.

failed\_player\_move: This field contains data when a move was illegal (non-null values indicate an illegal move), allowing for the counting of illegal moves.

move\_\_move: Stores the actual UCI (Universal Chess Interface) notation for each move, used to determine the most frequent or 'favorite' opening moves.

#### Language

One language: English

#### Personal and Sensitive Information

Personal information such as file paths and API keys have been removed from this program.

#### Collection Process

The dataset was collected from 150 games that took 1 hour to complete for processing.

#### Known Limitations

The program attempts limit AIs to only be able to communicate UCI moves, and comments regarding those moves. All other content is filtered by the data collection code.

A potential issue may arise if or when rate limits for the two AIs change, or if the AI models used for this dataset are retired. With every update, there
is a possibility that the AIs may improve or degrade in performance and ability to play a chess game.