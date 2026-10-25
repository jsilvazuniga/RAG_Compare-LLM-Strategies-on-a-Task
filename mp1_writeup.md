# MP1 Prompt Lab Writeup

## 1. Which strategy won, and on what dimension?

Few-shot and structured tied for the best accuracy, both scoring 3.0 out of 3 fields on average. Few-shot got the highest judge score at 4.0, while structured was cheaper at $0.000382 for the full run. All four strategies had a 100% parse rate. Zero-shot was fastest at 3.489297 seconds median latency, but its accuracy was lower. For me, structured is the best overall balance, while few-shot is the winner if judge score is the main thing.

## 2. What surprised you?

I expected chain-of-thought to do better because it gets extra space to analyse the job snippet. It scored 2.7 out of 3, which was still below few-shot and structured. It was also the slowest strategy at 5.967515 seconds and the most expensive at $0.001054. That was a good reminder that longer prompts do not automatically mean better extraction.

Another nice result was that the parser worked for every response, including responses wrapped in JSON code fences. I also liked that few-shot reached perfect judge score, while structured stayed cheaper and matched it on strict accuracy. The difference between those two is more useful than just saying one strategy won everything.

## 3. For your capstone domain, which strategy would you reach for first?

I would start with the structured strategy for my capstone domain. The system prompt gives the model a clear role, an exact schema, and rules for missing values, so the output should be easier to validate and use in the rest of the pipeline. I would use few-shot when the domain has confusing examples that need to be demonstrated instead of just described.

## 4. If you had another day, what would you try next?

I would test the prompts on more snippets before deciding between few-shot and structured. Ten examples is useful, but it is still a small test set. I would add harder cases with conflicting requirements, missing fields, abbreviations, and ranges. I would also compare a couple of models using the same prompts and check whether the judge-score difference is stable or just noise from one run.
