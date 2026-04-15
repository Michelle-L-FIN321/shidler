# Scenario 4 Memo 
**Created by:**  Michelle Luo
**Updated by:**  Michelle Luo
**Date Created:**  4/13/2026
**Date Updated:**  4/13/2026
**Version:**  0.0.1


---
## Executive Summary 
Adam,
I wanted to flag an open currency exposure on our books that warrants a decision before settlement. This memo covers what the position is, what’s at stake, and three hedging paths we can take. Happy to walk through the numbers together whenever works for you.



---
## Exposure
We are expecting to receive €20.0 million from a European aerospace customer, with settlement anticipated in the coming months. At today’s EUR/USD forward rate of 1.0930, that translates to a base case of $21.86 million in USD proceeds.
The exposure is straightforward: we invoice in euros, but we report earnings and fund operations in dollars. Until that payment converts, we are carrying open FX risk on the full €20M notional.


---
## The Risk
EUR/USD is not a sleepy pair. Six-month historical volatility typically runs 8–12 big figures, meaning a 10-cent adverse move is well within normal range — not a tail event. On €20M, that kind of shift costs over $1 million in lost USD proceeds.
To put it concretely:
If EUR falls to 1.04 at settlement, we receive only $20.8M — a $1.06M shortfall vs. today’s rate.
If EUR falls to 1.00 (not unheard of in stress scenarios), the gap widens to $1.86M.
On a contract of this size, leaving it unhedged is a meaningful P&L decision, not just a treasury housekeeping item.


---
## Hedging Options
Here’s a quick side-by-side of the strategies we’re evaluating:

Strategy: Forward Contract
Pros: Locks in $21.86M today. No premium cost. Simple to execute.
Cons: Zero upside if EUR strengthens. Counterparty credit exposure.

Strategy: Vanilla Put Option
Pros:Floor on downside. Full upside participation if EUR rallies.
Cons: Upfront premium (~1-2% notional, ~$220-440K). Strike selection trade-off.

Strategy: Zero-Cost Collar
Pros: No net premium. Protected below floor. Partial upside retained.
Cons: Upside capped. More structuring complexity than a plain forward.


My current lean is toward the forward contract given its simplicity and zero premium cost, but I’d want to stress-test all three scenarios in the Excel model we’re building before making a final recommendation.

---
## Next Steps
I’ll have the full payoff model (Stage 2) ready for your review shortly, with a scenario table showing USD outcomes across spot rates from 1.00 to 1.20. From there we can align on a hedge ratio and execute before the window tightens.
Let me know if you’d like to connect ahead of the model — I’m available whenever is convenient.

Best,
Michelle Luo, Treasury Risk Management 

