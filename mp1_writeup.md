Prompt Strategy Comparison
Which strategy won, and on what dimension?
Few-shot and structured prompting tied for the highest exact-match accuracy, averaging 2.8 out of 3 fields. All four strategies had a 100% parse rate, and their costs were nearly identical at the precision shown in the notebook. Structured prompting also tied for the highest judge score, while chain-of-thought was slightly faster by median latency. Structured prompting offered a strong balance of accuracy and judge score.

What surprised me?
The strategies performed much more similarly than I expected: even zero-shot reached 2.7 out of 3 on average, and every response parsed successfully. The more concrete issue was in my prompts: they instructed the model to return 0 when experience was not specified, while the reference answer expects null. That mismatch could cost accuracy on the deliberately ambiguous example, regardless of the prompting strategy.

Which strategy would I reach for first in my capstone domain?
For my RAG capstone, I would start with structured prompting. A consistent output shape should make it easier to pass retrieved information into later steps and check whether each answer is grounded in the source. I would pair it with an explicit rule to return null when the retrieved text does not support a value.

If I had another day, what would I try next?
I would first correct the missing-value instruction (like years_of_experience not being explicitely mentioned for one of the job snippet) and rerun the same comparison to see whether accuracy improves, especially on the unspecified-experience case. Then I would test the structured prompt on more varied snippets and on retrieved passages from my capstone domain, checking both extraction accuracy and whether the output stays supported by the source.

Github repository:
https://github.com/gayathrigs99/prompt-strategies.git