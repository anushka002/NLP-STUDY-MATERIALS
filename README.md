
## Important Links-

1. Self-Attention VS Multi-Head Attention: https://www.geeksforgeeks.org/nlp/self-attention-in-nlp-2/ and https://www.geeksforgeeks.org/nlp/multi-head-attention-mechanism/
2. LSTM: https://colah.github.io/posts/2015-08-Understanding-LSTMs/
3. Transformer-Based Models: https://medium.com/@minh.hoque/a-comprehensive-overview-of-transformer-based-models-encoders-decoders-and-more-e9bc0644a4e5


---

## Difference between PPO, DPO, TPO, GRPO:



| **Aspect**                              | **PPO** *(Proximal Policy Optimization)*                                                          | **DPO** *(Direct Preference Optimization)*                                                       | **TPO** *(Triple Preference Optimization)*                                                  | **GRPO** *(Group Relative Policy Optimization)*                                               |
|-----------------------------------------|----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| **Does it use Reinforcement Learning?** | **Yes** — PPO *is* an RL algorithm; used in RLHF for policy optimization.                          | **No RL** — purely preference-based supervised optimization.                                     | **No RL** — also preference-based (extension of DPO).                                         | **Yes** — RL variant but **removes value network**.                                            |
| **Main Input Data**                     | Reward scores from a **reward model** + rollouts from the policy.                                  | **Pairwise** preference tuples: (winner, loser).                                                 | **Triple** preference tuples: (gold ≻ win ≻ lose).                                            | Reward scores + **multiple rollouts** for each prompt.                                         |
| **Baseline / Advantage Estimation**     | Uses a **learned value function** (critic) to estimate advantage.                                  | No value function — optimization uses **likelihood ratio vs reference model**.                   | No value function — reward derived from **log-prob of current model only** + gold anchor.    | Uses **group-average reward** from old rollouts as the baseline (no critic).                   |
| **Reference Model Needed?**             | No explicit reference model (but KL penalty keeps policy near SFT).                                | **Yes** — requires a frozen **reference policy** (e.g., the SFT model).                          | **No** — completely removes reference model dependence.                                       | No reference model; only old-policy samples.                                                   |
| **How Reward is Interpreted**           | Reward comes from RM; PPO maximizes it while avoiding policy drift.                                | Reward defined *implicitly* through difference from reference model behavior.                    | Reward = **log πθ(y | x)** (direct model confidence); adds **gold NLL** as anchor.            | Reward compared **relative to the group average** of old responses.                            |
| **Optimization Goal**                   | Improve policy to generate outputs preferred by RM while controlling deviation from SFT.           | Push probability of preferred responses higher than dispreferred ones **relative to ref model**. | Enforce gold ≻ win ≻ lose ordering; stabilize with gold term; no KL to ref.                   | Improve policy performance while avoiding critic errors and ensuring stability.                |
| **Complexity / Compute Cost**           | **Highest cost** — requires: policy + critic + RM + rollouts; unstable and computationally heavy.  | **Low** — simple supervised learning; no critic, no rollouts; uses pairs.                        | **Moderate** — uses triples; no reference model; more stable than DPO.                       | **Lower than PPO** — removes critic, uses only policy + RM + group sampling.                   |
| **Stability**                           | Can be unstable due to critic errors, value estimation noise, reward hacking.                      | More stable than PPO but still sensitive to quality of reference model.                          | **Most stable** among preference methods: gold term prevents drift; no ref-model artifacts.   | More stable than PPO since no critic; group baseline reduces high-variance updates.            |
| **Main Motivation**                     | Original method for aligning LLMs with human feedback (InstructGPT).                               | Avoid the complexity of PPO by eliminating RL and reward model during training.                  | Improve over DPO by removing reference model & using richer preference structure.             | Fix PPO’s critic instability + reduce compute by replacing critic with multiple old rollouts.  |
| **When to Use**                         | When you have a good reward model and enough compute for RLHF.                                     | When you only have pairwise preferences and want simple, scalable training.                      | When you have gold, win, and lose responses and need robust, ref-free alignment.              | When PPO is too expensive or unstable — e.g., very large models (DeepSeek, RLHF at scale).     |
| **Key Limitation**                      | Critic overfitting, reward hacking, KL drift, expensive training.                                  | Dependence on reference model; sequence-level only; may inherit SFT biases.                      | Needs triple data; more annotation cost; slightly more complex pipeline.                      | Requires generating **multiple rollouts** per prompt; higher sampling cost.                    |




<img width="759" height="663" alt="image" src="https://github.com/user-attachments/assets/3930ff59-1573-4f5e-b375-08ccea02a095" />
