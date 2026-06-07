[![Live Demo](https://img.shields.io/badge/Live%20Demo-Netlify-00C7B7?style=for-the-badge&logo=netlify&logoColor=white)](https://fjord-fleet-dashboard.netlify.app/)

# Fjord Motor Fleet Pricing Tool

A browser-based analytics dashboard for optimizing fleet bid prices at Fjord Motor Company. The tool estimates win probability by customer segment, order size, and bid price, then recommends the price that maximizes expected contribution per vehicle.

## Business Problem

Fjord Motor sells the Coronet Elizabeth sedan to fleet customers through sealed bids, mainly police departments and corporate buyers. Orders range from 10 to 60 vehicles, with a manufacturing cost of $15,000 per car and a $25,000 MSRP. Historically, sales reps priced bids using experience and intuition, while incentives were tied to revenue rather than contribution margin. In Q4 2007, this discretionary pricing approach generated $171.8 million in contribution, leaving a large opportunity for more disciplined, model-driven pricing.

## Model Architecture

| Model | New feature added | Pseudo-R^2 | vs. previous model |
|---|---:|---:|---|
| Model 1 | Pooled logistic regression with one response curve for all buyers | 9.44% | Baseline |
| Model 2 | Separate police and corporate response functions | 30.86% | Main analytical breakthrough |
| Model 3 | Order size as a shared covariate | 31.47% | Improves fit |
| Model 4b | Price x order size interaction | 31.60% | Significant LR test vs. Model 3 |
| **Model 5** | Police-specific quadratic price term | **31.78%** | **Final pricing engine; significant LR test vs. Model 4b** |

Model 5 captures the non-linear budget-ceiling behavior of public procurement: police buyers accept bids comfortably below budget but reject bids near the ceiling sharply. Corporate buyers remain modeled with a linear price response.

## How the Optimisation Works

For each scenario, the tool evaluates the expected contribution of a bid as the product of win probability and unit margin. It searches the feasible price range from cost to MSRP and returns the unit price with the highest expected contribution.

```text
p* = argmax price in [cost, MSRP] of:

win_probability(segment, price, order_size) x (price - cost)
```

Because the Model 5 police response includes a quadratic price term, there is no simple closed-form solution. The dashboard uses Golden Section Search, a fast one-dimensional optimization method, to find the best price in the $15,000-$25,000 feasible range.

## Tool Features

- Model selector: Model 3, Model 4b, Model 5
- Customer segment toggle: Police / Corporate
- Order size input: 10-60 vehicles
- Police cost adjustment toggle for Q4 cost conditions
- KPI outputs for optimal price, win probability, expected contribution per vehicle, and total expected contribution
- Interactive contribution margin and win probability chart
- Dynamic executive insight sentence
- Print/PDF export for the current dashboard state

## Key Results

The historical discretionary pricing approach generated $171.8 million in contribution. Segment-specific model-driven pricing would have increased expected contribution to $308.7 million, an uplift of about $137 million per quarter.

| Scenario | Optimal price | Win probability | Expected contribution / vehicle |
|---|---:|---:|---:|
| Police, 20 vehicles | $17,774 | 52.7% | $1,463 |
| Corporate, 40 vehicles | $22,546 | 87.0% | $6,577 |

Model 5 adds a further $2.7 million in expected contribution versus Model 4b while improving the fit for police procurement behavior.

## Tech Stack

- HTML
- CSS with Tailwind CSS
- JavaScript
- Chart.js
- Netlify

## Project Context

This project was completed as a university analytics project at the University of Mannheim in FSS 2026, based on the "Fleet Sales Pricing at Fjord Motor" case by Columbia CaseWorks.
