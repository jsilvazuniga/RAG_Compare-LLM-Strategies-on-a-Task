# MP1 Prompt Strategy Comparison

A comparison of four prompting strategies for extracting `company`, `role`, and `years_experience_required` from job snippets.

## Summary

| Strategy | Accuracy<br>(mean of 3) | Parse rate | Judge score<br>(1-4) | Total cost | Median latency | Result |
|:--|--:|--:|--:|--:|--:|:--|
| **Few-shot** | **3.0** | **100%** | **4.0** | **$0.000494** | 4.093032 s | **Best judge score** |
| **Structured** | **3.0** | **100%** | **3.8** | **$0.000382** | 4.389013 s | **Best accuracy/cost balance** |
| Zero-shot | 2.6 | 100% | 3.6 | $0.000362 | **3.489297 s** | Fastest and cheapest |
| Chain-of-thought | 2.7 | 100% | 3.8 | $0.001054 | 5.967515 s | Slowest and most expensive |

## What The Results Show

- **Few-shot and structured prompting tied for best accuracy**, extracting all three fields correctly on average.
- **Few-shot had the highest judge score** at 4.0.
- **Structured had the best accuracy/cost balance**, costing $0.000382 for the full strategy run.
- **All strategies parsed successfully** on every test case.
- **Zero-shot was fastest and cheapest overall**, but its accuracy was lower at 2.6.
- **Chain-of-thought was slowest and most expensive**, without matching the accuracy of few-shot or structured prompting.

## Recommendation

Use the **structured strategy** as the default when accuracy and predictable cost matter most. Few-shot is also a strong option because it tied for accuracy and received the highest judge score, while zero-shot is useful as a very cheap baseline.

> Results are based on 10 job snippets and 40 strategy calls. Costs are shown to **six decimal places** so the small non-zero values remain visible.
