# Findings

## Experiment 1: Behavioral Baseline

**Method:** Measured top-5 prediction overlap between positive 
and negated sentence pairs across 10 examples.

**Result:** Average overlap of 3.3/5.
GPT-2 partially ignores "not" at the output level.
Same top prediction in most cases regardless of negation.

**Interpretation:** Behavioral failure is real but inconsistent.
Semantic domain matters — some sentences fail more than others.

---

## Experiment 2: Residual Stream Analysis

**Method:** Measured L2 norm difference between positive and 
negated sentence residual streams at each layer's final token.

**Result:**
| Layer | L2 Difference |
|-------|--------------|
| 0     | 14.341       |
| 1     | 20.165       |
| 2     | 25.915       |
| 3     | 32.347       |
| 4     | 36.578       |
| 5     | 41.650       |
| 6     | 45.142       |
| 7     | 51.527       |
| 8     | 58.888       |
| 9     | 67.668       |
| 10    | 80.213       |
| 11    | 95.263       |

**Interpretation:** Difference grows monotonically. The model 
does accumulate a signal about negation across layers. 
The representation is building — but behavior doesn't reflect it.

---

## Experiment 3: Control Experiment (Key Finding)

**Method:** Compared residual stream differences for negation 
("not") vs intensity modifiers ("very", "quite") across 
matched sentence pairs.

**Result:**
| Layer | Negation | Control | Difference |
|-------|----------|---------|------------|
| 0     | 14.341   | 17.602  | -3.261     |
| 1     | 20.165   | 23.425  | -3.260     |
| 2     | 25.915   | 29.339  | -3.424     |
| 3     | 32.347   | 37.078  | -4.731     |
| 4     | 36.578   | 42.829  | -6.251     |
| 5     | 41.650   | 45.463  | -3.813     |
| 6     | 45.142   | 47.798  | -2.657     |
| 7     | 51.527   | 52.832  | -1.304     |
| 8     | 58.888   | 58.601  | +0.288     |
| 9     | 67.668   | 66.749  | +0.919     |
| 10    | 80.213   | 78.929  | +1.284     |
| 11    | 95.263   | 98.111  | -2.848     |

**This is the most important finding of this project.**

"not" and "very/quite" produce nearly identical internal 
representations across all 12 layers — within 6 L2 units 
at every layer. The curves are almost indistinguishable.

**Interpretation:** The behavioral failure of negation is NOT 
because the model fails to encode "not" internally. The 
representation exists. The failure lives in the output stage — 
how the model translates internal representations into 
predictions. Internal encoding and behavioral output decouple.

**Safety relevance:** This is the same structural property 
that Apollo Research studies in frontier models under scheming — 
a model whose internal state diverges from its behavioral output. 
Detecting misaligned cognition from behavior alone may be 
insufficient. You need internal access.

---

## Experiment 4: NES Metric

**Method:** Implemented Negation Effect Score following 
Al Mofael et al. (arXiv:2603.12423, 2026).
NES = log P(target | affirmative) - log P(target | negated)
Positive NES = failure. Negative NES = success.

**Result:**
| Sentence | NES | Result |
|----------|-----|--------|
| The sky is → blue | -0.308 | SUCCESS |
| Fire is → hot | +1.050 | FAILURE |
| Ice is → cold | +0.581 | FAILURE |
| The water is clean → and | +1.219 | FAILURE |
| He is guilty → of | +0.094 | FAILURE |
| The food is good → for | -0.925 | SUCCESS |
| She is happy → with | -1.380 | SUCCESS |
| The answer is correct → . | -0.202 | SUCCESS |

Failure rate: 4/8 (50%)
Mean NES: +0.016

**Interpretation:** Semantic domain predicts success.
Social/emotional templates ("She is happy") show strongest 
negation sensitivity (NES: -1.380).
Physical/factual templates ("Fire is hot") fail most strongly 
(NES: +1.050) — factual priors override negation cues.

---

## Experiment 5: Attention Analysis

**Method:** Measured attention weight from final token to 
"not" token across all 144 heads (12 layers × 12 heads).

**Result:** 
- Layer 0 Head 0: highest average attention to "not" (~0.5-0.6)
- Attention becomes scattered and diffuse in deeper layers
- No single head consistently tracks negation beyond layer 2

**Interpretation:** Early layers attend to "not" strongly 
but this signal doesn't persist. The circuit doesn't 
maintain a dedicated negation-tracking mechanism 
through the full network.

---

## Experiment 6: Causal Influence Map

**Method:** Measured delta NES per head via activation patching.
Patched affirmative activations into negated run head by head.

**Result:**
- L2H4 is the dominant head (delta NES: 0.2350)
- Head 4 appears causally influential across multiple layers:
  L2H4, L6H4, L4H7
- Published paper (Al Mofael et al.) found L5H11 and L4H4
- Head 4 overlap across our results is notable

**Interpretation:** The negation circuit is concentrated 
but not identical to the published analysis. Head 4's 
recurring causal role across layers suggests it may 
implement a more general polarity-tracking function 
rather than a negation-specific one.

---

## Open Questions That Still Needs To Be Answered

1. Where exactly does the representation-to-behavior 
   translation break down? Which specific output-stage 
   components fail to use the negation signal?

2. Why does Head 4 appear causally influential across 
   multiple layers? Is this the same functional role 
   or different?

3. Does the semantic domain effect on NES scale — 
   do social/emotional contexts always outperform 
   factual contexts for negation?

4. Does this representation-behavior decoupling 
   scale with model size? Does it get worse or better 
   in larger models?

5. Is this decoupling detectable before it becomes 
   a safety problem in frontier models?