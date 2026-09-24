# Association Rule Mining

Association rule mining identifies item combinations that occur frequently and evaluates whether the occurrence of one itemset is meaningfully associated with the occurrence of another.

## 1. Market-basket model

Let

$$
I=\{i_1,i_2,\ldots,i_n\}
$$

be the set of all possible items. A transaction $T$ is a subset of $I$, and the transaction database is

$$
D=\{T_1,T_2,\ldots,T_N\}.
$$

An **itemset** $X$ is any subset of $I$.

In a supermarket, a transaction is one basket. Association mining asks which itemsets repeatedly occur across many baskets. The same model can represent pages visited in one session, symptoms observed in one patient, or services used by one customer.

## 2. Support, confidence, and strong rules

### 2.1 Itemset support

The support count of $X$ is

$$
\sigma(X)=|\{T\in D:X\subseteq T\}|.
$$

Its relative support is

$$
s(X)=\frac{\sigma(X)}{|D|}.
$$

An itemset is **frequent** when its support reaches the chosen minimum support threshold.

### 2.2 Association rule

An association rule has the form

$$
X\rightarrow Y,
$$

where $X\cap Y=\varnothing$.

Its support is

$$
s(X\rightarrow Y)=s(X\cup Y)=\frac{\sigma(X\cup Y)}{|D|}.
$$

Its confidence is

$$
c(X\rightarrow Y)=\frac{\sigma(X\cup Y)}{\sigma(X)}
=\frac{s(X\cup Y)}{s(X)}.
$$

Support answers: **How common is the complete combination?**

Confidence answers: **Among transactions containing $X$, what fraction also contains $Y$?**

A **strong rule** satisfies both minimum support and minimum confidence.

### Example

Suppose 20 of 100 baskets contain both bread and milk, while 25 contain bread:

$$
s(\text{bread}\rightarrow\text{milk})=\frac{20}{100}=20\%,
$$

$$
c(\text{bread}\rightarrow\text{milk})=\frac{20}{25}=80\%.
$$

The direction does not affect support, but it does affect confidence because the denominator changes.

## 3. Computational complexity of exhaustive search

With $n$ distinct items, there are $2^n$ possible itemsets. Testing every itemset against every transaction becomes infeasible as $n$ grows.

Association mining therefore separates the task into two phases:

1. Find all frequent itemsets using minimum support.
2. Generate high-confidence rules only from those frequent itemsets.

## 4. Anti-monotone property of support

If $X\subseteq Y$, every transaction containing $Y$ must also contain $X$. Therefore,

$$
s(X)\ge s(Y).
$$

This gives two equivalent pruning rules:

- If an itemset is frequent, all its subsets are frequent.
- If an itemset is infrequent, every superset is infrequent.

### Significance of the property

If $\{A,B\}$ is infrequent, there is no reason to count $\{A,B,C\}$, $\{A,B,D\}$, or any larger itemset containing it. Apriori uses this fact to avoid much of the $2^n$ search space.

## 5. Apriori algorithm

Let $C_k$ be the candidate $k$-itemsets and $L_k$ be the frequent $k$-itemsets.

### Procedure

1. Scan the database to construct $L_1$.
2. Self-join compatible itemsets in $L_{k-1}$ to form $C_k$.
3. Prune a candidate if any of its $(k-1)$-subsets is absent from $L_{k-1}$.
4. Scan the database and count the remaining candidates.
5. Retain candidates meeting minimum support as $L_k$.
6. Repeat until $L_k=\varnothing$.
7. Generate rules from the union of all frequent itemsets.

### A complete small trace

Consider four transactions:

| Transaction | Items |
|---|---|
| $T_1$ | $\{A,B,C\}$ |
| $T_2$ | $\{A,C\}$ |
| $T_3$ | $\{A,B\}$ |
| $T_4$ | $\{B,C\}$ |

Let the minimum support count be 2.

First level:

$$
C_1=\{A:3,\ B:3,\ C:3\},
$$

so

$$
L_1=\{A,B,C\}.
$$

Join $L_1$ to form the pairs:

$$
C_2=\{AB:2,\ AC:2,\ BC:2\}.
$$

All meet the threshold, so

$$
L_2=\{AB,AC,BC\}.
$$

Since all 2-subsets are frequent, $ABC$ survives the prune step, but its support count is only 1:

$$
C_3=\{ABC:1\},\qquad L_3=\varnothing.
$$

The frequent itemsets are therefore $A,B,C,AB,AC,$ and $BC$.

If minimum confidence is $60\%$, the rules from $AB$ are

$$
c(A\rightarrow B)=\frac{\sigma(AB)}{\sigma(A)}=\frac{2}{3}=66.7\%,
$$

$$
c(B\rightarrow A)=\frac{\sigma(AB)}{\sigma(B)}=\frac{2}{3}=66.7\%.
$$

Both rules are strong.

### Join and prune are different

- **Join** creates a larger candidate from frequent smaller itemsets.
- **Prune** rejects the candidate when any required subset is not frequent.

For example, $\{1,3,4,5\}$ must be pruned if $\{1,4,5\}\notin L_3$, even if the other 3-subsets are frequent.

### Converting support percentage to count

For $N$ transactions and minimum support $p\%$, use

$$
\text{minimum count}=\left\lceil\frac{pN}{100}\right\rceil.
$$

A transaction is a set. Repeating an item inside the same transaction does not increase its support count.

## 6. Hash-tree support counting

Apriori still needs to determine which candidates occur in each transaction. A hash tree places candidate itemsets into buckets. For a transaction, the algorithm generates relevant $k$-subsets and follows only the matching hash branches.

This reduces candidate lookup, but it does not solve Apriori's two main problems:

- a very large number of candidates may still be generated;
- the database is scanned again for each itemset size.

## 7. FP-growth algorithm

FP-growth avoids explicit candidate generation. It compresses transactions with common prefixes into a **frequent-pattern tree (FP-tree)**.

### 7.1 Constructing the FP-tree

1. Scan the database and count every item.
2. Remove items below minimum support.
3. Sort the remaining items by descending global frequency. Use one fixed tie rule.
4. Create a null root.
5. Scan the database again. Filter and reorder each transaction using the global order.
6. Insert the ordered transaction as a path. Shared prefixes reuse nodes and increment counts.
7. Connect equal items through node links in a header table.

### Role of frequency ordering

Frequent items are placed near the root, so many transactions share prefixes. Greater prefix sharing produces a smaller tree.

### 7.2 Mining the FP-tree

Process suffix items from least frequent to most frequent:

1. Follow the item's header links.
2. Collect every prefix path leading to it. These paths form the **conditional pattern base**.
3. Add path counts and remove items below minimum support.
4. Build the **conditional FP-tree**.
5. Combine the suffix with patterns found in that conditional tree.
6. Repeat recursively.

### Illustrative example

For minimum support count 3, the frequent-item order is

$$
L=\langle f:4,\ c:4,\ a:3,\ b:3,\ m:3,\ p:3\rangle.
$$

After filtering and sorting, the transactions are:

| TID | Ordered frequent items |
|---|---|
| 01 | $f,c,a,m,p$ |
| 02 | $f,c,a,b,m$ |
| 03 | $f,b$ |
| 04 | $c,b,p$ |
| 05 | $f,c,a,m,p$ |

For suffix $p$, the prefix paths are

$$
\langle f,c,a,m:2\rangle,qquad \langle c,b:1\rangle.
$$

Only $c$ reaches total count 3 across these paths, so the $p$-conditional tree contains $c:3$. This gives the pattern

$$
\{c,p\}:3.
$$

For suffix $m$, the conditional pattern base is

$$
\langle f,c,a:2\rangle,qquad \langle f,c,a,b:1\rangle.
$$

After removing infrequent $b$, the conditional tree is the single path

$$
f:3\rightarrow c:3\rightarrow a:3.
$$

Combining every non-empty subset of this path with $m$ gives

$$
\{f,m\},\{c,m\},\{a,m\},\{f,c,m\},\{f,a,m\},\{c,a,m\},\{f,c,a,m\}.
$$

The maximal frequent itemsets for the example are

$$
\{c,p\}\quad\text{and}\quad\{f,c,a,m\}.
$$

## 8. Apriori versus FP-growth

| Aspect | Apriori | FP-growth |
|---|---|---|
| Search strategy | Level-wise candidate generation | Recursive pattern growth |
| Database scans | One for each itemset length | Normally two before tree mining |
| Candidate sets | Explicitly generated | Avoided |
| Storage | Candidate tables/hash tree | Compressed FP-tree and header links |
| Best use | Small or sparse data; easy manual trace | Dense data or long frequent patterns |
| Main weakness | Candidate explosion and repeated scans | Tree construction is more complex |

For identical input and support threshold, both algorithms must discover the same frequent itemsets.

## 9. Generating association rules

For a frequent itemset $F$, every non-empty proper subset $A$ creates the candidate rule

$$
A\rightarrow F-A.
$$

A $k$-itemset produces

$$
2^k-2
$$

non-trivial candidate rules.

All rules generated from $F$ already meet minimum support because their support is $s(F)$. Only confidence still needs to be tested.

If a rule fails minimum confidence, rules derived by moving more items from its antecedent to its consequent can be pruned. The smaller antecedent has equal or greater support, so the confidence cannot improve.

## 10. Frequent, closed, and maximal itemsets

These terms describe different levels of compression.

- **Frequent:** its support meets minimum support.
- **Closed frequent:** no proper superset has exactly the same support.
- **Maximal frequent:** no proper superset is frequent at all.

Every maximal frequent itemset is closed, and every closed frequent itemset is frequent. The reverse statements are not always true.

### Example

Suppose

$$
\sigma(AB)=4,\qquad \sigma(ABC)=4.
$$

$AB$ is not closed because adding $C$ does not reduce its support. If no frequent superset of $ABC$ exists, then $ABC$ is maximal.

| Representation | Compactness | Recover frequent itemsets? | Recover exact support? |
|---|---:|---:|---:|
| All frequent itemsets | Low | Yes | Yes |
| Closed frequent itemsets | Medium | Yes | Yes |
| Maximal frequent itemsets | High | Yes, as subsets | No |

Maximal patterns tell us that their subsets are frequent, but not the exact support of each subset.

## 11. Limitation of confidence

Consider this contingency table:

|  | Coffee | No coffee | Total |
|---|---:|---:|---:|
| Tea | 150 | 50 | 200 |
| No tea | 650 | 150 | 800 |
| Total | 800 | 200 | 1000 |

For the rule $\text{Tea}\rightarrow\text{Coffee}$,

$$
s(\text{Tea}\rightarrow\text{Coffee})=\frac{150}{1000}=15\%,
$$

$$
c(\text{Tea}\rightarrow\text{Coffee})=\frac{150}{200}=75\%.
$$

Seventy-five percent sounds strong, but coffee is purchased by $80\%$ of all customers. Tea drinkers are actually **less** likely than average to buy coffee.

The problem is that confidence ignores the baseline support of the consequent.

## 12. Lift and interest factor

Lift corrects the confidence baseline:

$$
\operatorname{lift}(X\rightarrow Y)
=\frac{c(X\rightarrow Y)}{s(Y)}
=\frac{s(X\cup Y)}{s(X)s(Y)}.
$$

Using contingency-table counts,

$$
\operatorname{lift}(X,Y)=\frac{Nf_{11}}{f_{1+}f_{+1}}.
$$

Interpretation:

- lift $>1$: positive association;
- lift $=1$: statistical independence;
- lift $<1$: negative association.

For tea and coffee,

$$
\operatorname{lift}=\frac{0.75}{0.80}=0.9375<1.
$$

Thus the rule indicates a slight negative association despite its high confidence.

### Objective and subjective interestingness

- **Objective measures** come from the data: support, confidence, lift, and correlation.
- **Subjective measures** depend on the user: novelty, usefulness, actionability, or compatibility with domain knowledge.

A statistically strong rule may still be obvious or impossible to act upon.

## 13. Objective-measure properties

For a binary contingency table:

|  | $B$ | $\neg B$ |
|---|---:|---:|
| $A$ | $f_{11}$ | $f_{10}$ |
| $\neg A$ | $f_{01}$ | $f_{00}$ |

- A measure has the **inversion property** if swapping presence with absence for both variables leaves its value unchanged.
- A measure has the **null-addition property** if increasing $f_{00}$ leaves its value unchanged.

These properties help decide which interestingness measure is suitable for a particular application.

## 14. Examination-answer format

### Apriori numerical

The answer should convert support to a count, show every $C_k$ and $L_k$, explain at least one pruning decision, continue until $L_k$ is empty, and calculate each requested rule confidence from support counts.

### FP-growth

The answer should show first-pass counts, the fixed frequency order, filtered transactions, the final FP-tree, header links, conditional pattern bases, conditional trees, and discovered patterns.

### Rule evaluation

The answer should define support and confidence, establish the baseline-frequency limitation of confidence, calculate lift, and interpret positive, negative, or independent association.

## 15. Summary

- Support measures prevalence; confidence measures conditional frequency.
- Anti-monotonicity prunes supersets of infrequent itemsets.
- Apriori generates candidates; FP-growth compresses transactions and grows patterns.
- Closed itemsets preserve support information; maximal itemsets are smaller but lose it.
- High confidence is not enough—compare it with the consequent's baseline using lift.

## Source

Primary supplied material: [Association Rule Mining slides](../sources/4_Association_Rules.pdf).
