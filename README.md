Entropy as Hallucinogen
A small experiment on whether a language model's own uncertainty can tell you when it's about to be wrong.
The idea I wanted to test
The common assumption — including one I held going in — is: if the model seems "confident," it's probably right; if it seems "unsure," it's probably wrong. Entropy is the usual stand-in for that "unsure" feeling. The flatter the model's probability distribution over its next token, the higher the entropy, the more "uncertain" it's supposed to be.
So the question was simple: does that assumption actually hold up, and can it be turned into something useful?
How I tested it
I used FLAN-T5 (base) on TruthfulQA's multiple-choice questions. For each question, the model scores every answer choice two ways:
Log-probability — how likely the model thinks that answer is (the normal way to pick an answer)
Entropy — how spread out the model's uncertainty was while scoring that answer
Then I tried five ways of using that information to pick an answer:
Just use log-probability (the normal baseline)
Just use entropy (pick the lowest-entropy answer)
A simple rule that switches between the two
A tuned blend of both
An "oracle" — the best possible outcome if I always picked whichever signal happened to be right for that question. This isn't a real method, it's a ceiling to measure the others against.
What happened
Method
Accuracy
Log-probability only (baseline)
25.6%
Entropy only
23.4%
Rule-based switch
24.8%
Best tuned blend
26.0%
Oracle (best possible)
40.8%
�
Load image
Entropy on its own did worse than just using log-probability. Blending the two in, even after tuning, helped by less than half a percentage point — basically nothing. Correct answers did have slightly higher entropy than wrong ones on average, but the relationship was very weak (a correlation of 0.09, where 1.0 would mean a perfect match).
What I actually took from this
Entropy isn't nothing — there's a real, measurable link between it and correctness. It's just too weak and noisy to build a working "uncertainty detector" out of, at least in this setup. The most telling number here isn't any of the entropy methods, it's the gap between 26% (the best I could squeeze out of log-probability and entropy together) and 40.8% (the oracle ceiling). That gap says there's clearly some real signal in the choices about which answer is right — entropy and log-probability just aren't the features that capture it.
Setup
Model: FLAN-T5 base
Dataset: TruthfulQA (multiple-choice)
Entropy: measured per token, averaged across the answer
Scoring: sequence likelihood — no actual text generation involved, just scoring existing choices
Limitations
Only tested on one, fairly small model
Multiple-choice only — real-world hallucination shows up in free-form generation, which this doesn't test
Only token-level entropy — there are more sophisticated uncertainty measures (like semantic entropy) that might do better
The "best tuned blend" was tuned and evaluated on the same data, so that +0.4% should be read as noise, not a real improvement
Reproduce it
Run it on Kaggle: https://www.kaggle.com/code/blackoutcreed/entropy-as-hallucinogen
or import the notebook into your own environment (needs CUDA).
License
Apache 2.0
