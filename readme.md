#Trying to use Reinforcement Learning for multi-hop QA#

**1. Motivation**

To be used after retrieval and before final answer generation in this concept.

Given the input sentence and passages, estimate the optimal trajectory to reach an answer.

If this task is complete: eventually Natual Language QA using RL will have a following pipeline:

1) Query Generator will generate queries(keywords) from input sentence and get documents ⇒ extract passages(snippets).

2) Given the input sentence and passages ⇒ generate series of actions that selects the most relevant passages(w/ optimized order) to answering the questions.

3) Feed input sent + selected passages in sequential order to LLM to generate final response


**2. MDP**

State: input sentence, unselected passages, selected passages

Action: select one from the unselected passages, or stop selecting and answer.

Reward: Give reward to the selection w.r.t. how relevant it is to reaching final answer.

Next State: “action” passage removed from unselected passages, added to selected passages.

Termination: When all correct passages are selected, or all passages are selected, or horizon has ben reached.


**3. Model Design Ideas**

Should utilize existing pre-trained LM structures. ⇒ not limited to decoder-based architectures. BERT-like Encoder architectures might also work.

Use Actor-Critic setting, 

Actor: should take input sent. + selected passages + single unselected passage as an input ⇒ outputs probability(logits) of each unselected option. 

Actor's Forward Pass: 

1. generate embedding for input sent. + selected passages: called state embedding
2. generate embedding for each unselected passage: called action embeddings
3. compute scores(logits) for each unselected action conditioned on state embedding. ⇒ sample based on probability

Need 2 separate embedders ⇒ state embedder & action embedder.

Critic: Predicts state-value or action-value given the input sent., selected passages and unselected passages.

**4. Reward System**

Remove the explicit termination step ⇒ traj .terminated only when all correct passage are found or horizon has been reached
1. with fixed reward +1, -0.2 + accuracy-proportional termination reward
2. Allowing the model to terminate whenever they want often leads to very poor policy. The agent will almost always immediately terminate to reduce future negative rewards.



