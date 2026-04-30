# Where Does GPT-2 Represent Negation?
*A mechanistic interpretability study*

## Overview
This project investigates how GPT-2 processes negation, focusing on the word **"not"**.  
We analyze both the model’s **output behavior** and its **internal representations** across layers.

## Research Question
Does GPT-2 correctly incorporate negation into its predictions, and how is negation represented internally?

## Methodology

## Methodology

We analyze pairs of positive and negated sentences constructed as follows:

- "The sky is" → "The sky is not"
- "Fire is" → "Fire is not"
- "Ice is" → "Ice is not"
- "Honey is" → "Honey is not"
- "She is happy" → "She is not happy"
- "The room is dark" → "The room is not dark"
- "The food is good" → "The food is not good"
- "He is guilty" → "He is not guilty"
- "The water is clean" → "The water is not clean"
- "The answer is correct" → "The answer is not correct"

### Behavioral Analysis
- Compare top-5 next-token predictions  
- Measure overlap between positive and negated outputs  

### Internal Analysis
- Measure residual stream differences (L2 norm) across layers  
- Analyze attention patterns directed at the token "not"

### Behavioral Analysis
- Compare top-5 next-token predictions  
- Measure overlap between positive and negated outputs  

### Internal Analysis
- Measure residual stream differences (L2 norm) across layers  
- Analyze attention patterns directed at the token *"not"*  

## Results

### Behavioral
- Average top-5 overlap: **3.3 / 5**  
- Indicates partial failure to incorporate negation in predictions  

### Residual Stream
- L2 difference increases from **~14 → ~95** across 12 layers  
- Indicates strong internal representation of negation  

### Attention Patterns
- Early layers (Layer 0 Head 0) attend strongly to *"not"* (~0.5–0.6)  
- Attention becomes more diffuse in deeper layers  

### Average Attention 
![Averaged Attention]
(attension_to_not_averaged.png)

### Attention Heatmap
![Attention Heatmap]
(attension_to_not_heatmap.png)

### Residual Difference by Layer
![Residual Difference]
(residual_diff_by_layer.png)

## Interpretation
The model encodes negation internally, but this signal does not consistently influence final predictions.  
This suggests a gap between internal computation and output generation,though further analysis is needed
 to identify where this signal is lost.

## Requirements
pip install transformer_lens torch matplotlib numpy

## How to Run
1. Open `negation.ipynb`  
2. Run all cells sequentially  
3. Observe outputs and visualizations  

## Project Structure

## Project Structure
```
.
├── negation.ipynb
├── attension_to_not_heatmap.png
├──attension_to_not_averaged.png
├──residual_diff_by_layer.png
├──README.md
```
## Tools
- TransformerLens  
- PyTorch  
- Matplotlib  

## Write-up
Coming soon (LessWrong post)

## Notes
This project was developed as an initial exploration into mechanistic interpretability.

