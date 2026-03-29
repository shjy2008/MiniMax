# Raj Game Agent using MiniMax algorithm

An agent designed to play the game of Raj, competing against agents including `random_agent.py`, `value_agent.py`, and `valueplus_agent.py`.

## Strategy

The agent uses a **depth-limited minimax algorithm** with **alpha-beta pruning**, supported by an elaborately designed heuristic evaluation function.

### Why Minimax?

Raj is not an alternating game — players reveal their cards simultaneously each round. However, minimax is still applicable by assuming the agent bids first and the opponent responds after seeing the agent's card. This models a worst-case scenario and allows the agent to make robust decisions even under adversarial conditions.

### Algorithm Overview

- The agent acts as **MAX**; the opponent acts as **MIN**.
- In MAX's move, the agent selects a card from its hand. A round ends after MIN's move, when the opponent selects a card, cards are compared, and both players' banks are updated.
- The algorithm uses **depth-first recursive search** starting from a root state built from the current game percepts.
- The `get_best_child()` function recursively finds the optimal action by evaluating leaf nodes and backing up values to the root.

### Search Depth Limit

A depth limit of **4 (two rounds)** is used for two reasons:

1. Deeper searches require significantly more processing time.
2. Since bidding items are chosen randomly, assuming their order beyond a short horizon reduces the benefit of deeper search. Testing confirmed that increasing depth beyond 4 provides minimal score improvement.

### Alpha-Beta Pruning

Alpha and beta values are passed as parameters (initialised to −∞ and +∞ respectively). When **alpha ≥ beta**, remaining sibling nodes are pruned — since the current branch cannot improve the guaranteed outcome for MAX or MIN, there is no need to evaluate it further.

This optimisation reduces running time from ~23 seconds to ~8 seconds per 1000 games.

### Heuristic Evaluation Function

The evaluation score has two components:

**1. Potential Value** — estimates the value the agent can gain in future rounds:

```
potential_value = items_left_abs_average × card_value_compare_score
```

- `items_left_abs_average`: the average absolute value of remaining items to bid on.
- `card_value_compare_score`: both players' hands are sorted and compared position by position. Each position where the agent's card exceeds the opponent's adds +1; each where it falls short adds −1.

**2. Bank Difference** — the agent's current bank minus the opponent's bank.

These are combined as:

```
total_score = potential_value + bank_diff × 1.2
```

A multiplier of 1.2 is applied to the bank difference because confirmed scores are slightly more valuable than estimated future gains.

---

## Performance

Tests were run against three opponents over 1000 games each, with random seeds 0, 1, and 2. Test environment: MacBook Pro 2019 (2.6 GHz 6-Core Intel Core i7).

| Opponent | Seed | Games | Run Time (s) | Agent Avg Score | Opponent Avg Score | Ratio |
|---|---|---|---|---|---|---|
| `random_agent.py` | 0 | 1000 | 8 | 1.86 | — | 2.65× |
| `random_agent.py` | 1 | 1000 | 8 | 1.90 | — | 2.62× |
| `random_agent.py` | 2 | 1000 | 8 | 1.80 | — | 2.77× |
| `value_agent.py` | 0 | 1000 | 8 | 2.36 | — | 1.56× |
| `value_agent.py` | 1 | 1000 | 8 | 2.42 | — | 1.56× |
| `value_agent.py` | 2 | 1000 | 8 | 2.40 | — | 1.56× |
| `valueplus_agent.py` | 0 | 1000 | 8 | 2.21 | — | 1.62× |
| `valueplus_agent.py` | 1 | 1000 | 8 | 2.27 | — | 1.61× |
| `valueplus_agent.py` | 2 | 1000 | 8 | 2.25 | — | 1.63× |

**Ratio** = agent average score ÷ opponent average score.

### Key Findings

- **Efficiency**: ~8 seconds per 1000 games, thanks to the shallow depth limit and alpha-beta pruning.
- **vs `random_agent.py`**: the agent scores approximately **2.7×** the opponent's score.
- **vs `value_agent.py` / `valueplus_agent.py`**: the agent scores approximately **1.6×** the opponent's score. The lower ratio reflects the greater strategic sophistication of these opponents.
- The agent wins all nine 1000-game test series.

---

## Conclusion

The agent performs well in terms of both efficiency and competitiveness. Minimax with worst-case opponent modelling provides a solid strategic foundation, while depth limiting and alpha-beta pruning keep the algorithm fast. The heuristic evaluation function — which accounts for both confirmed bank gains and estimated future value — is central to the agent's competitiveness.
