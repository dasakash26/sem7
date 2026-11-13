# CT2 Study Plan

**Three days · 6 study hours per day · 18 hours total.** Take breaks between blocks.

The plan assumes CT2 covers Chapters 5–8. Refresh neurons, predictions, and training first. For each new model, explain what it does in plain language, trace one worked example, then solve the named exercise from memory.

Use the chapter’s worked examples to prepare for written calculations. Each topic has a YouTube link. Use about 5–10 minutes of the block for the relevant example, then solve the exercise. For full lectures, jump to the named concept.

## Day 1 · Probabilistic networks and deep learning · 6 h

Start with [Neural Networks](03%20-%20Neural%20Networks/Neural%20Networks.md) §3.1 and the [CT1 refresher](04%20-%20Feedforward%20and%20Graphical%20Models/Feedforward%20Networks/Feedforward%20Networks.md) §§4.1–4.2, [Probabilistic Networks](05%20-%20Probabilistic%20Networks/Probabilistic%20Networks.md) §§5.1–5.3, and [Deep Learning](06%20-%20Deep%20Learning/Deep%20Learning.md) §§6.1–6.3.

| Time | Topic | What to finish | YouTube |
|---:|---|---|---|
| 60 min | Neuron and training basics | Draw a neuron with inputs, weights, and a bias. Compute its weighted sum and sigmoid output, then calculate the loss and trace one weight update. | [Neuron and layers](https://www.youtube.com/watch?v=aircAruvnKk) · [Loss and gradient descent](https://www.youtube.com/watch?v=IHZwWFHWa-w) (3Blue1Brown) |
| 60 min | Hopfield networks | Do **5A**. Start with the idea of recalling a stored pattern from a noisy input. Build the weights, flip one bit, and explain why the energy drops. | [Pattern recall and energy](https://www.youtube.com/watch?v=1WPJdAW-sFo) (Artem Kirsanov) |
| 75 min | Boltzmann machines and RBMs | Use a neuron’s sigmoid output as its probability of firing. Sample a binary state, then trace a Gibbs sampling step and a CD-1 update. Do **5C** and explain how the sampled reconstruction affects the update. | [Boltzmann sampling and RBM intuition](https://www.youtube.com/watch?v=_bqa_I5hNAo) (Kirsanov) · [CD weight update](https://www.youtube.com/watch?v=wMb7cads0go) (Hugo Larochelle) |
| 45 min | Sigmoid networks and autoencoders | Compute a decoder output and its reconstruction error. Do **5B** to compare what changes in Hopfield recall, RBM sampling, and autoencoder training. | [Sigmoid activation](https://www.youtube.com/watch?v=aircAruvnKk) (3Blue1Brown) · [Encoder and decoder](https://www.youtube.com/watch?v=FzS3tMl4Nsc) (Larochelle) |
| 60 min | Training and regularization | Do **6A**. Calculate batch updates, choose a checkpoint from validation loss, and compare L1, L2, and dropout. | [Stochastic and mini-batch updates](https://www.youtube.com/watch?v=vMh0zPT0tLI) · [L1 vs. L2 intuition](https://www.youtube.com/watch?v=Xm2C_gTAl8c) (StatQuest) · [Dropout](https://www.youtube.com/watch?v=D8PJAL-MZv8) · [Early stopping](https://www.youtube.com/watch?v=BOCLq2gpcGU) (DeepLearning.AI). Use §6.2 to practice choosing a checkpoint. |
| 60 min | CNNs | Compute one convolution and pooling output by hand. Do **6B**, then repeat with different padding or stride. | [Filters and pooling](https://www.youtube.com/watch?v=HGwBXDKFk9I) (StatQuest) |

## Day 2 · Finish deep learning, then classifiers and RL foundations · 6 h

Use [Deep Learning](06%20-%20Deep%20Learning/Deep%20Learning.md) §6.4, [Classification](07%20-%20Classification/Classification.md) §§7.1–7.3, and [Reinforcement Learning](08%20-%20Reinforcement%20Learning/Reinforcement%20Learning.md) §§8.1–8.2.

| Time | Topic | What to finish | YouTube |
|---:|---|---|---|
| 30 min | Day 1 recall | Redo one RBM weight update and one CNN shape calculation without notes. | Retry **5C** or **6B**, then replay the relevant example in Day 1 if you get stuck. |
| 60 min | Recurrent and recursive networks | Start with the hidden state as a running summary of earlier inputs. Do **6C**, then trace two RNN steps and show how the previous hidden state affects the current output. | [RNN hidden state](https://www.youtube.com/watch?v=AsNTP8Kwu80) (StatQuest) · [TreeRNN segment](https://www.youtube.com/watch?v=S8d-7v3f5MQ) (Stanford CS224N) |
| 60 min | Logistic regression | Draw a line separating two classes. Turn the linear score into a probability with sigmoid, then do **7A**. | [Binary classification and sigmoid](https://www.youtube.com/watch?v=yIYKR4sgzI8) (StatQuest) |
| 60 min | LDA | Do **7B**. Calculate the scatter matrices and explain what makes a projection useful for separating classes. | [Class separation and projections](https://www.youtube.com/watch?v=azXCzI57Yfc) (StatQuest) |
| 75 min | SVM | Do **7C**. Derive the margin width, explain a soft margin, and work through the mapped XOR example in §7.3 if the kernel step is unclear. | [Hard and soft margins, 0:40–6:46](https://www.youtube.com/watch?v=efR1C6CvhmE&t=40s) · [Kernel intuition, 12:23–15:25](https://www.youtube.com/watch?v=efR1C6CvhmE&t=743s) (StatQuest) |
| 75 min | RL foundations | Use a two-state example to identify the agent, state, action, and reward. Distinguish a bandit from an MDP, then compute a discounted return and one Bellman backup. | [Agent, actions, and rewards, opening segment](https://www.youtube.com/watch?v=2pWv7GOvuf0) · [MDPs and Bellman equations](https://www.youtube.com/watch?v=lfHX2hHRMVQ) · [Bandits and exploration](https://www.youtube.com/watch?v=sGuiWX07sKw) (David Silver) |

## Day 3 · Finish reinforcement learning and rehearse · 6 h

Use [Reinforcement Learning](08%20-%20Reinforcement%20Learning/Reinforcement%20Learning.md) §§8.3–8.4, then [Semester Practice](Semester%20Practice.md) sections **E–H**.

| Time | Topic | What to finish | YouTube |
|---:|---|---|---|
| 60 min | Dynamic programming | Recall the Bellman backup from Day 2. Do **8A** and explain how policy iteration differs from value iteration. | [Policy iteration and value iteration](https://www.youtube.com/watch?v=Nd1-UUMVfz4) (David Silver) |
| 75 min | Monte Carlo, TD, and Q-learning | Write the MC and TD targets from memory. Do **8B** for both terminal and nonterminal next states. | [Monte Carlo and TD targets](https://www.youtube.com/watch?v=PnHCvfgC_ZA) (David Silver) · [Worked Q-learning update](https://www.youtube.com/watch?v=J3qX50yyiU0) (Mahesh Huddar) |
| 45 min | REINFORCE, off-policy MC, and UCT | Write each method's purpose and main rule. Do **8C**, including how UCT selects a child and backs up a return. | [REINFORCE](https://www.youtube.com/watch?v=KHZVXao4qXs) · [Off-policy learning and importance sampling](https://www.youtube.com/watch?v=0g4j2k_Ggc4) (David Silver) · [Tree search and UCT intuition](https://www.youtube.com/watch?v=BEFY7IHs0HM) (Computerphile) |
| 90 min | Timed practice | Pick at least one question from each of **E–H**. Close the notes and show your steps. | Closed book. |
| 60 min | Mark and retry | Find where each mistake started and redo the two weakest questions from scratch. | Use the revision clips below to check the step you missed. |
| 30 min | Final recall | Write one page from memory with formulas, update rules, and the errors you need to avoid. Check it against the notes. | Use the last 30 minutes as described below. |

### Revision after the timed set

Use up to **10 minutes** of the final recall block to replay **one or two clips** for mistakes you made. Use the rest of the block to write the formulas from memory and redo the missed steps on paper.

| If you missed… | Replay | Then redo |
|---|---|---|
| Hopfield recall or the RBM update | [Hopfield recall](https://www.youtube.com/watch?v=1WPJdAW-sFo) or [CD update](https://www.youtube.com/watch?v=wMb7cads0go) | **5A** or **5C** |
| CNN shapes or the RNN hidden state | [CNN filter example](https://www.youtube.com/watch?v=HGwBXDKFk9I) or [RNN sequence example](https://www.youtube.com/watch?v=AsNTP8Kwu80) | **6B** or **6C** |
| A classifier’s probability, projection, or margin | [Logistic sigmoid](https://www.youtube.com/watch?v=yIYKR4sgzI8), [LDA projection](https://www.youtube.com/watch?v=azXCzI57Yfc), or [SVM margin from 0:40](https://www.youtube.com/watch?v=efR1C6CvhmE&t=40s) | **7A**, **7B**, or **7C** |
| An RL target or update | [Monte Carlo vs. TD](https://www.youtube.com/watch?v=PnHCvfgC_ZA) or [Q-learning update](https://www.youtube.com/watch?v=J3qX50yyiU0) | **8B** or the MC vs. TD part of **8C** |

[Chapter contents](readme.md)
