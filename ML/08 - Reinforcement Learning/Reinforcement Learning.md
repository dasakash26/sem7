# Chapter 8. Reinforcement Learning

[Contents](../readme.md)

## 8.1 From immediate rewards to sequential decisions

An agent observes a state, takes an action, receives a reward, and observes a new state. A **policy** specifies how it selects actions. A **value function** estimates future return. A **model** describes how states and rewards change. Value-based methods estimate action values and derive a policy from them. Policy-gradient methods adjust the policy directly.

| Setting | Decision objective | Example |
|---|---|---|
| Multi-armed bandit | Choose an arm with high immediate expected reward | Select one of several ad variants |
| Contextual bandit | Choose an action for the current context | Select an ad using page or audience features |
| Markov decision process | Maximize cumulative reward when actions affect future states | Navigate a robot toward a goal |

A bandit can update its estimate for action $a$ using $Q(a)\leftarrow Q(a)+(r-Q(a))/N(a)$, where $N(a)$ includes the current observation. If the estimate was 4 after three pulls and the fourth reward is 8, the new estimate is 5. An $\epsilon$-greedy policy chooses a random action with probability $\epsilon$ and a greedy action otherwise. UCB adds an exploration bonus, such as $c\sqrt{\ln t/N(a)}$, after each arm has been tried. An optimal action under a known stationary reward model maximizes its expected reward. Exploration is necessary when those expectations must be learned.

### Bandit optimality and feedback

A greedy action is best under the current estimates. The true optimal arm maximizes the actual mean reward, which the agent may not know. **Regret** compares accumulated reward with what always choosing the best arm would yield in expectation. For an ad selector, a contextual bandit observes page features, chooses one ad, and observes only that ad’s reward. It can learn an expected reward $Q(x,a)$ for each context–action pair.

## 8.2 Returns, values, and Bellman equations

An MDP specifies states, actions, a next-state and reward distribution, and a discount factor $\gamma$. Under the Markov property, the distribution of the next state and reward depends on the present state and action. The discounted return is $G_t=r_{t+1}+\gamma G_{t+1}$. Set $G_T=0$ at a terminal time $T$.

- $V^\pi(s)=\mathbb E_\pi[G_t\mid s_t=s]$ values a state under policy $\pi$.
- $Q^\pi(s,a)=\mathbb E_\pi[G_t\mid s_t=s,a_t=a]$ values a specified action followed by that policy.
- For rewards 2 and 4 followed by termination at $\gamma=0.5$, the return before the first reward is $2+0.5(4)=4$.

Let $L^\pi$ denote the Bellman expectation operator. For a candidate value function $V$,

$$
(L^\pi V)(s)=\sum_a\pi(a\mid s)\sum_{s',r}p(s',r\mid s,a)[r+\gamma V(s')].
$$

Policy evaluation solves $V^\pi=L^\pi V^\pi$. With bounded rewards and $0\le\gamma<1$, $L^\pi$ contracts the sup-norm difference by at most $\gamma$. Repeated updates therefore converge to its unique fixed point. The optimal Bellman backup takes the maximum over actions outside the expectation.

$$
(L^*V)(s)=\max_a\sum_{s',r}p(s',r\mid s,a)[r+\gamma V(s')].
$$

Use $V=0$ at terminal states. The backup gives a new estimate using the current $V$. Repeating the backup in value iteration approaches the optimal value function under the same discounted, bounded-reward assumptions.

## 8.3 Dynamic programming, Monte Carlo, and TD

| Method | Where its target comes from |
|---|---|
| Policy iteration | Use a known model to evaluate the policy, then improve it greedily |
| Value iteration | Use a known model to repeatedly take the best Bellman backup, then extract a greedy policy |
| Monte Carlo | Use a sampled complete return $G_t$ after the episode |
| TD(0) | Use a one-step sample and bootstrap from $r+\gamma V(s')$ |
| Q-learning | Use the greedy action-value target $r+\gamma\max_{a'}Q(s',a')$ |

For sampled prediction, both Monte Carlo and TD(0) use $V(s)\leftarrow V(s)+\alpha[\text{target}-V(s)]$. The target in the table determines when the update can run and whether it depends on a current value estimate.

**One value-iteration backup.** From state S, action Stop pays 2 and terminates. Action Continue pays 1 and deterministically reaches a state currently valued at 4. At $\gamma=0.9$, the backup chooses $\max(2,1+0.9\cdot4)=4.6$.

**One Q-learning update.** If $Q(s,a)=2$, reward is 1, the best next-state action value is 4, $\gamma=0.9$, and $\alpha=0.5$, then

$$
Q(s,a)\leftarrow 2+0.5[1+0.9(4)-2]=3.3.
$$

The behavior policy can explore while the target uses a maximum. Set the continuation term to zero at termination.

**Compare MC and TD on the same episode.** Suppose the rewards are 1 then 4, with termination after the second reward. Let $\gamma=0.9$, $V(s)=2$, $V(s')=3$, and $\alpha=0.5$.

| Method | Target for $V(s)$ | Updated estimate |
|---|---|---|
| Monte Carlo | $1+0.9(4)=4.6$ after the episode | $2+0.5(4.6-2)=3.3$ |
| TD(0) | $1+0.9(3)=3.7$ after the first transition | $2+0.5(3.7-2)=2.85$ |

Both move the estimate toward a target. MC uses the reward actually observed later. TD uses the current estimate of later rewards.

**Off-policy Monte Carlo.** A behavior policy $b$ generates episodes while a target policy $\pi$ is evaluated. Importance sampling reweights a return by the product of $\pi(a_t\mid s_t)/b(a_t\mid s_t)$ over the relevant actions. For ratios $0.8/0.4$ and $0.5/0.5$, the trajectory weight is 2. The behavior policy must give positive probability to every action the target can choose in the evaluated trajectories.

### Policy improvement and off-policy estimates

Given $V^\pi$, compute one expected backup for each action, then choose a greedy action in each state. This is the improvement step of policy iteration. With a known model, policy iteration repeats evaluation and improvement. Value iteration performs a maximization on each sweep, then reads out the greedy policy.

With sampled returns $G_i$ and trajectory weights $\rho_i$, ordinary importance sampling averages $\rho_iG_i$. Weighted importance sampling uses $\sum_i\rho_iG_i/\sum_i\rho_i$ when the denominator is positive. For returns 4 and 2 with weights 2 and 1, the ordinary estimate is 5 and the weighted estimate is $10/3$. Large weight products can raise variance.

## 8.4 Policy search, REINFORCE, and UCT

**Policy search** directly optimizes a parameterized policy. For the episodic objective of maximizing expected discounted return from the start state, the update contribution at step $t$ is $\alpha\gamma^tG_t\nabla_\theta\log\pi_\theta(a_t\mid s_t)$. In REINFORCE, compute $G_t$ after the episode. A positive return weights the update in the direction that locally increases the sampled action’s log-probability. A state-dependent baseline can reduce variance.

**UCT** uses a UCB-style score at each tree node to balance exploiting a high mean rollout return with exploring a rarely visited child. Select children using that score, expand an unvisited child, simulate a continuation, and back up the sampled return along the path. For parent visits $N$, child visits $n_i$, and child mean $\bar G_i$, a common score is $\bar G_i+c\sqrt{\ln N/n_i}$. Unvisited children are tried before applying the formula.

### Choosing an RL method

| Situation | Useful starting point |
|---|---|
| One decision with immediate feedback | A bandit or contextual bandit |
| Known transition and reward model | Dynamic programming or tree search |
| Episodes that can finish before each update | Monte Carlo prediction or REINFORCE |
| Learn online from transitions | TD for prediction or Q-learning for action values |

In UCT, the first term of the selection score is the current mean and the second rewards limited exploration. For the same mean and parent visit count, a child visited 4 times gets twice the exploration bonus of a child visited 16 times. A simulation then updates the visited nodes’ counts and mean returns.

## Practice

- **8A.** Explain the difference between value iteration and policy iteration. For the two-action example, repeat the backup when the continuation state's value drops from 4 to 1.
- **8B.** Update $Q(s,a)=1$ after reward 2 with $\gamma=0.9$, $\max_{a'}Q(s',a')=3$, and $\alpha=0.25$. Repeat with a terminal next state.
- **8C.** Compare a Monte Carlo target with a TD(0) target. Explain where UCT uses exploration and how it updates the tree.

## Answer checks

| Exercise | Answer |
|---|---|
| 8A | Policy iteration alternates policy evaluation and improvement. Value iteration updates the value directly with a maximization. The new Continue score is $1+0.9(1)=1.9$, so choose Stop for value 2. |
| 8B | Nonterminal target $4.7$ and updated value $1.925$. Terminal target 2 and updated value $1.25$. |
| 8C | Monte Carlo uses a completed return. TD(0) uses one reward and a bootstrap estimate. UCT uses an exploration bonus during child selection and backs up simulated return to visited ancestors. |
[Chapter sources](../readme.md#8-reinforcement-learning)

---

[← Chapter 7](../07%20-%20Classification/Classification.md) · [Contents](../readme.md)
