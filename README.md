# E-commerce Customer Query Classifier (Prompt)

A prompt that classifies multilingual customer queries from an e-commerce company into one predefined category. The output is always **exactly one word**, with no explanation, punctuation, or extra text.

## Problem

An e-commerce company receives a large volume of customer queries about orders, deliveries, payments, refunds, cancellations, and product defects. Queries are written in natural language and in many languages (including mixed languages such as Hinglish). Each query needs to be routed to the right category automatically.

## Categories

| Category | Covers |
|---|---|
| **Order** | Placing or modifying an order, order status, order confirmation |
| **Delivery** | Shipping delays, tracking, courier issues, package not received |
| **Payment** | Failed or declined payments, double charges, coupons, invoices |
| **Refund** | Requests for money back, refund status or delays |
| **Cancellation** | Cancelling an order or subscription |
| **Defect** | Damaged, faulty, expired, or wrong items received |
| **Other** | Greetings, general questions, account issues, spam, unclear text |

## Repository Contents

| File | Description |
|---|---|
| `prompt.txt` | The classification prompt |
| `test_cases.csv` | 46 test cases with expected categories |
| `README.md` | This file |

## How to Use

1. Open `prompt.txt`.
2. Replace `{{customer_query}}` at the end with the customer's message.
3. Send the full prompt to your LLM of choice.
4. The model replies with one category word, for example `Delivery`.

**Recommended settings:** temperature `0` for consistent, deterministic output.

## Example

**Input**
```
Mera package abhi tak nahi aaya, 10 din ho gaye
```

**Output**
```
Delivery
```

## Prompt Design

- **Closed label set:** the model can only answer with one of the listed categories.
- **Strict output format:** one word, no punctuation, no quotes, no explanations.
- **Multilingual:** the prompt tells the model to understand any language, mixed language, or transliterated text before classifying. Examples in several languages reinforce this.
- **Primary-intent rule:** when a query touches several categories, the customer's main request decides the label.
  - "Cancel my order and refund me" → `Cancellation`
  - "Item arrived broken, I want my money back" → `Defect`
- **Prompt-injection guard:** instructions inside a customer query are treated as text to classify, not commands.
- **Fallback category:** `Other` prevents forced, wrong labels for unclear input.

## Testing

`test_cases.csv` contains the following columns:

| Column | Meaning |
|---|---|
| `id` | Test case ID (TC01 to TC46) |
| `customer_query` | Input sent to the classifier |
| `expected_category` | Correct output |
| `language` | Language of the query |
| `test_type` | Standard, Edge, Ambiguous, Prompt injection, or Format robustness |

**To run the tests:**

1. Send each `customer_query` through the prompt.
2. Compare the model's output with `expected_category`.
3. A test passes only if the output matches exactly (one word, correct category, no extra characters).
4. Review failures by `test_type` to see which rules need tuning.

**Coverage:** all six main categories plus `Other`, 11 languages, ambiguous multi-intent queries, spam and gibberish, prompt-injection attempts, and queries that try to make the model explain itself.

## Limitations

- Expected labels for ambiguous queries follow the rules in the prompt. Adjust them if your business rules differ.
- Very short or unclear queries may be classified as `Other`.
- In production, validate that the output is one of the allowed categories, and route anything else to a human or retry.

## Customizing

- Edit the category names and definitions in `prompt.txt` to match your company's list.
- Add examples from real support tickets to improve accuracy.
- Update `test_cases.csv` whenever you change categories or tie-break rules.
