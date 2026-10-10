# Wordle Solver: Information Gain & Strategy Comparison

A Wordle environment and four solving agents, compared on all 2,315 official answer words. The best agent, a hybrid of entropy search and endgame minimax, solves **98.9%** of games within six guesses.

Group project for UCI CS 175 with Bhakti Dave and Duy Nguyen. The project started as a reinforcement learning idea (REINFORCE, DQN), but after more research and professor feedback we switched to an information-theory approach.

## Notes on reinforcement learning

REINFORCE and DQN agents were prototyped early in the project but are not part of the final evaluation. They were dropped in favor of the information-gain approach, which was a better fit for the problem.

## Agents

| Agent | Strategy |
|---|---|
| **Random** | Guesses a random remaining candidate. Used to validate the environment. |
| **Frequency** | Scores each candidate by the sum of its unique letter frequencies among remaining words. |
| **Entropy** | Simulates feedback for each guess against all candidates and picks the guess with the highest Shannon entropy (expected information gain). |
| **Hybrid** | Opens with "slate", uses entropy search through the midgame, then switches to minimax (minimize worst-case remaining candidates) when 5 or fewer candidates remain. A feedback cache avoids recomputing (guess, target) pairs. |

## Results

Each agent was run against all 2,315 answer words.

| Agent | Win rate | Avg guesses (wins) | Avg runtime / game |
|---|---|---|---|
| Random | ~0% | n/a | 0.0043 s |
| Frequency | 79.14% | 4.54 | 0.0044 s |
| Entropy | 95.77% | 3.59 | 0.0242 s |
| **Hybrid** | **98.88%** | 3.62 | 0.0033 s |

The Entropy Agent often picked its final guess at random once only 4-5 candidates remained. The Hybrid Agent's minimax endgame fixed this, and its feedback cache made it faster than plain entropy search.

## How Wordle works

The agent has six guesses to find a hidden five-letter word. After each guess, every letter is marked gray (not in the word), yellow (in the word, wrong position), or green (correct position). Duplicate letters are handled the way real Wordle does it: greens are assigned first, then yellows only for unmatched remaining letters.

## Project structure

- `wordle_env.py`: game environment, feedback generation, candidate filtering
- `random_agent.py`, `frequency_agent.py`, `entropy_agent.py`, `hybrid_agent.py`: the four agents
- `evaluate.py`, `benchmark.py`: run agents on all answer words and report win rate, guess distribution, and runtime
- `play.py`: step-by-step visualization of a single game
- word lists: 14,855 valid guesses and 2,315 answers

## Run

```bash
python benchmark.py   # compare all agents
python play.py        # watch a single game
```
