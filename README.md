# GPT-2 Linguistic Negation Investigation

A causal mechanistic investigation into how GPT-2 Small internally 
processes linguistic negation — and what the findings imply for 
representation-behavior decoupling in language models.

## The Question

GPT-2 handles negation poorly behaviorally. But why?
Is this a representation failure — or does the model encode "not" 
internally but fail to use it?

## Key Findings

**Behavioral:** Average top-5 prediction overlap of 3.3/5 across 
10 positive/negated sentence pairs. Failure rate 50% by NES metric 
(mean NES: +0.016).

**Residual Stream:** Internal difference grows monotonically across 
all 12 layers — L2-norm 14.3 at layer 0 to 95.3 at layer 11.

**Control Experiment (main finding):** "not" and "very/quite" produce 
nearly identical residual stream differences across all 12 layers, 
within 6 L2 units at every layer. The behavioral failure is NOT a 
representation failure — it is an output-stage failure. Internal 
encoding and behavioral output decouple.

**NES Metric:** Implemented Negation Effect Score following 
Al Mofael et al. (arXiv:2603.12423, 2026). Semantic domain matters —
"She is not happy" shows strongest negation sensitivity (NES: -1.380) 
while "Fire is not hot" fails strongly (NES: +1.050), suggesting 
factual priors override negation cues in some templates.

**Causal Patching:** L2H4 is the dominant head (delta NES: 0.2350). 
Head 4 appears causally influential across multiple layers (L2H4, 
L6H4, L4H7). The published paper (Al Mofael et al.) found L5H11 
and L4H4 as key heads — the Head 4 overlap is notable and 
warrants further investigation.

**Attention:** Layer 0 Head 0 shows highest average attention to 
"not" (~0.5–0.6). Attention becomes diffuse in deeper layers — 
no single head consistently tracks negation beyond layer 2.

## Safety Relevance

The representation-behavior decoupling found here is structurally 
analogous to what Apollo Research studies in frontier models — 
where a model's internal state diverges from its behavioral output. 
If this property exists in GPT-2 for something as simple as negation, 
detecting misaligned cognition from behavior alone may be 
fundamentally insufficient. Internal access via mechanistic 
interpretability tools is necessary.

This connects to the broader question AlexMeinke raises in 
"Physics of RL" (LessWrong, 2026): can behavioral selection 
produce internal representations that don't faithfully surface 
in outputs? My negation data suggests yes, even in small models.

## Experiments

- Behavioral prediction overlap (10 sentence pairs)
- NES metric across 8 sentence pairs (failure rate: 50%)
- Residual stream L2 norm difference per layer
- Control experiment: negation vs intensifier ("very"/"quite")
- Attention heatmaps across all 144 heads (12 × 12)
- Causal influence map: delta NES per head via activation patching

## Visualizations



![Residual stream difference by layer](residual_diff_by_layer.png)





![Negation vs control comparison](negation_vs_control.png)





![Attention to not - single sentence](attention_to_not_heatmap.png)





![Attention to not - averaged](attention_to_not_averaged.png)



## Relation to Published Work

After completing this project, I discovered an independent published 
analysis of negation circuits in GPT-2 (Al Mofael et al., 
arXiv:2603.12423, March 2026). My findings on attention diffusion 
and the representation-behavior dissociation partially converge 
with and extend their results. The Head 4 overlap in causal 
patching is a specific point of convergence worth investigating further.

## Tools

- Python 3.11 · TransformerLens · PyTorch · Matplotlib · NumPy
- GPT-2 Small (117M parameters)

## Key References

- Al Mofael et al., "Interpreting Negation in GPT-2" 
  arXiv:2603.12423 (2026)
- Elhage et al., "A Mathematical Framework for Transformer 
  Circuits" (2021)
- Apollo Research, "Scheming Reasoning Evaluations" (2024)
- AlexMeinke, "Physics of RL: Toy Scaling Laws for the Emergence 
  of Reward-Seeking" LessWrong (2026)

## How to Run

git clone https://github.com/biplab-inbits/gpt2-negation-mechanistic-interpretability
cd gpt2-negation-mechanistic-interpretability
pip install transformer_lens torch matplotlib numpy
Open negation.ipynb in Jupyter and run all cells in order

## Full Write-up

Read the complete investigation on LessWrong: [LINK]

## Author

Biplab Aditya — BSc Computer Science, West Bengal, India.
Applying to MATS Autumn 2026 (Apollo Research stream — 
Science of Scheming).