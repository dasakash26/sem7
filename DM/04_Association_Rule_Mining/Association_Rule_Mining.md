# Chapter 4. Association Rule Mining

Chapters 2 and 3 explain how to prepare transactions and organize their history. Association rule mining now identifies item combinations that occur frequently and evaluates whether the occurrence of one itemset is meaningfully associated with the occurrence of another.

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

The basic basket representation is binary: it records presence, not the quantity bought or price paid. Repeating an item within a basket does not increase its support. Quantity, revenue, and profit need additional attributes or a quantitative/utility-oriented representation.

## 2. Support, confidence, and strong rules

### Itemset support

The support count of $X$ is

$$
\sigma(X)=|\{T\in D:X\subseteq T\}|.
$$

Its relative support is

$$
s(X)=\frac{\sigma(X)}{|D|}.
$$

An itemset is **frequent** when its support reaches the chosen minimum support threshold.

Minimum support may be stated as a proportion, percentage, or support count. For a threshold of $30\%$ in a database of five transactions, the minimum support count is two transactions.

### Association rule

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

An association rule describes co-occurrence rather than causality. The rule $X\rightarrow Y$ indicates that $Y$ occurs frequently when $X$ occurs; it does not establish that $X$ causes $Y$.

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

With $n$ distinct items, there are $2^n$ possible itemsets including the empty set, or $2^n-1$ nonempty itemsets. Testing every itemset against every transaction becomes infeasible as $n$ grows.

Association mining therefore separates the task into two phases:

1. Find all frequent itemsets using minimum support.
2. Generate high-confidence rules only from those frequent itemsets.

This separation is valid because every rule $X\rightarrow Y$ has the same support as the itemset $X\cup Y$. An infrequent itemset cannot produce a strong rule.

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

The itemsets are maintained in lexicographic order during joining so that duplicate candidates are avoided. The pruning test is applied before support counting because an infrequent subset makes the candidate infrequent by the anti-monotone property.

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

Internal nodes of the tree direct the search according to a hash function applied to an item at a particular depth. Leaf nodes contain the actual candidate itemsets and their counters. Only candidates that can be subsets of the current transaction are examined.

### Worked Hash-Tree Lookup

Consider these 3-item candidates:

```text
125 127 139 245 289 357 459 478 567 579 678 679
```

Here `125` denotes the sorted itemset {1,2,5}. Hash items into three groups: {1,4,7}, {2,5,8}, and {3,6,9}. At successive internal depths, use successive items to select branches; each leaf retains candidate itemsets for exact checking.

For transaction {1,2,5,6,8}, its ten 3-subsets are

```text
125 126 128 156 158 168 256 258 268 568
```

Follow the corresponding hash branches and compare subsets with the candidates in reached leaves. Only **125** belongs to the candidate set, so only its counter increases, by one. Other candidates in a reached leaf are not automatically counted: hashing can collide, and exact containment is still required. Repeat for each transaction, then retain candidates whose counters meet minimum support.

This reduces candidate lookup, but it does not solve Apriori's two main problems:

- a very large number of candidates may still be generated;
- the database is scanned again for each itemset size.

## 7. FP-growth algorithm

FP-growth avoids explicit candidate generation. It compresses transactions with common prefixes into a **frequent-pattern tree (FP-tree)**.

Each FP-tree node stores an item name and a count. The count represents the number of filtered transactions sharing the prefix from the root to that node. The header table provides a direct link to every node carrying the same item label.

### Constructing the FP-tree

1. Scan the database and count every item.
2. Remove items below minimum support.
3. Sort the remaining items by descending global frequency. Use one fixed tie rule.
4. Create a null root.
5. Scan the database again. Filter and reorder each transaction using the global order.
6. Insert the ordered transaction as a path. Shared prefixes reuse nodes and increment counts.
7. Connect equal items through node links in a header table.

### Role of frequency ordering

Frequent items are placed near the root, so many transactions share prefixes. Greater prefix sharing produces a smaller tree.

The final tree is:

```text
Root
  f:4
    c:3
      a:3
        m:2
          p:2
        b:1
          m:1
    b:1
  c:1
    b:1
      p:1
```

Header links connect equal item labels across branches. Node counts represent prefix sharing; they are not necessarily the global count of that item.

### Mining the FP-tree

Process suffix items from least frequent to most frequent:

1. Follow the item's header links.
2. Collect every prefix path leading to it. These paths form the **conditional pattern base**.
3. Add path counts and remove items below minimum support.
4. Build the **conditional FP-tree**.
5. Combine the suffix with patterns found in that conditional tree.
6. Repeat recursively.

The conditional pattern base contains the prefix information relevant to one suffix item only. Its conditional tree therefore represents the frequent patterns that co-occur with that suffix.

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
\langle f,c,a,m:2\rangle,\qquad \langle c,b:1\rangle.
$$

Only $c$ reaches total count 3 across these paths, so the $p$-conditional tree contains $c:3$. This gives the pattern

$$
\{c,p\}:3.
$$

For suffix $m$, the conditional pattern base is

$$
\langle f,c,a:2\rangle,\qquad \langle f,c,a,b:1\rangle.
$$

After removing infrequent $b$, the conditional tree is the single path

$$
f:3\rightarrow c:3\rightarrow a:3.
$$

Combining every non-empty subset of this path with $m$ gives

$$
\{f,m\},\{c,m\},\{a,m\},\{f,c,m\},\{f,a,m\},\{c,a,m\},\{f,c,a,m\}.
$$

The maximal frequent itemsets for the example are $\{b\}$, $\{c,p\}$, and $\{f,c,a,m\}$. The singleton $b$ has count 3 but no frequent pair containing it; it must not be discarded.

For the remaining suffixes, $b$ has conditional base fca:1, f:1, c:1, in which no item reaches 3, but singleton b:3 is still frequent. Suffix a has conditional path f:3 → c:3, producing af:3,ac:3,acf:3. Suffix c produces cf:3; suffix f has no prefix. Together with the p and m results and all six singletons, this exhausts the frequent patterns. A recursion with an empty conditional tree stops after reporting its suffix; a single-path tree emits each nonempty path subset combined with the suffix.

## 8. Vertical Mining and Scalable Search

The horizontal representation stores TID-to-items rows. A **vertical representation** stores each itemset's set of transaction identifiers, called its tidset. Support becomes the tidset size, and extension becomes intersection:

$$
T(X\cup Y)=T(X)\cap T(Y),\qquad\sigma(X)=\lvert T(X)\rvert.
$$

Using the four-transaction A-B-C example, $T(A)=\{1,2,3\}$, $T(B)=\{1,3,4\}$, $T(C)=\{1,2,4\}$. Intersections give $T(AB)=\{1,3\}$, $T(AC)=\{1,2\}$, $T(BC)=\{1,4\}$, and $T(ABC)=\{1\}$. At minimum count 2, the three pairs are frequent and the triple is not.

**ECLAT** performs depth-first itemset extension using this representation:

1. Build singleton tidsets and remove infrequent singletons.
2. Choose a fixed item order and recursively extend a prefix only with later items.
3. Intersect tidsets to obtain an extension's tidset and support.
4. Emit a frequent extension and recursively explore it.
5. Prune an infrequent extension and all its supersets.
6. Stop when no extension remains.

A prefix and its extension lists form the maintained search state. A singleton-to-pair-to-triple branch gives the complete dry run above. Sorted-list intersection is linear in the combined list sizes; bitsets can make intersections efficient but require storage. Dense tidsets and many frequent combinations can still create substantial memory or output cost.

Other scalability techniques exploit different opportunities. **Transaction reduction** discards transactions that cannot contain further candidates. **Partitioning** mines locally with an equivalent relative threshold, unions locally frequent candidates, and verifies them globally: any globally frequent pattern must be frequent in at least one partition. **Sampling** mines a sample but needs verification and a strategy for potentially missed patterns. **Hashing** can discard pair buckets whose counts cannot reach support. These optimize search; they do not change support's definition.

## 9. Apriori versus FP-growth

| Aspect | Apriori | FP-growth |
|---|---|---|
| Search strategy | Level-wise candidate generation | Recursive pattern growth |
| Database scans | One for each itemset length | Normally two before tree mining |
| Candidate sets | Explicitly generated | Avoided |
| Storage | Candidate tables/hash tree | Compressed FP-tree and header links |
| Best use | Small or sparse data; easy manual trace | Dense data or long frequent patterns |
| Main weakness | Candidate explosion and repeated scans | Tree construction is more complex |

For identical input and support threshold, both algorithms must discover the same frequent itemsets.

## 10. Generating association rules

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

For example, the frequent itemset $\{A,B,C\}$ generates the six rules $A\rightarrow BC$, $B\rightarrow AC$, $C\rightarrow AB$, $AB\rightarrow C$, $AC\rightarrow B$, and $BC\rightarrow A$. Each has support $s(ABC)$, but each has a different confidence because the antecedent support differs.

If a rule fails minimum confidence, rules derived by moving more items from its antecedent to its consequent can be pruned. The smaller antecedent has equal or greater support, so the confidence cannot improve.

## 11. Frequent, closed, and maximal itemsets

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

Closed itemsets are therefore preferred when exact support information is required, whereas maximal itemsets are preferred when a more compact summary is sufficient.

## 12. Multilevel and Multidimensional Rules

A product hierarchy connects item-level purchases to broader concepts:

```text
Dairy -> Milk -> Skim milk
             -> Whole milk
Bakery -> Bread -> Brown bread
```

A **multilevel rule** can describe broad or specific products, such as Dairy → Bakery or Skim milk → Brown bread. Encode basket ancestors consistently, count each ancestor at most once per basket, and apply the declared thresholds at each level. A basket with both skim and whole milk contains Milk once, not twice.

With a common minimum count 3, a fine-level item can be pruned if its ancestor count is below 3. With decreasing thresholds at deeper levels, that inference is invalid unless the ancestor is tested against a sufficiently low bound. For example, parent support 2 fails a broad count threshold 3 while a child with support 2 can meet a fine threshold 2. State whether thresholds are uniform or level-dependent.

A rule that merely says Skim milk → Milk is true by the hierarchy and generally uninformative. Exclude such ancestor-descendant redundancies when the goal is cross-selling.

A **multidimensional rule** uses several attributes or predicates, for example

$$
\operatorname{AgeBand}=\text{Young}\ \land\ \operatorname{IncomeBand}=\text{High}
\ \rightarrow\ \operatorname{Buys}=\text{Laptop}.
$$

A single-dimensional market-basket rule repeats the same “buys” predicate for different items. An interdimension rule uses each involved dimension/predicate once; a hybrid rule can combine customer attributes with multiple purchases. Support counts complete record matches, and confidence divides by antecedent matches, just as before. Do not include mutually exclusive values of the same single-valued attribute in one candidate.

For four customer records, suppose two are Young/High/Laptop, one Young/Low/Phone, and one Senior/High/Laptop. Young ∧ High → Laptop has support $2/4=50\%$ and confidence $2/2=100\%$. High → Laptop has support $3/4$ and confidence 1. Different conditions describe different subpopulations.

Numerical association mining first needs a representation for ranges or quantitative relations. Discretizing age or spending produces categorical conditions; domain-aware quantitative-rule methods can optimize interval choices. 

## 13. Constraint-Based Mining and High-Dimensional Patterns

A **constraint** specifies which patterns are useful or admissible. It can restrict items, dimensions, rule structure, aggregate values, length, or support. A **metarule** specifies a template such as AgeBand ∧ IncomeBand → Buys, so the search need not generate every possible form.

Pushing constraints into candidate generation saves work only when the pruning logic is valid.

| Constraint type | Inference under extension | Example |
|---|---|---|
| Anti-monotone | If it fails for X, every superset fails | Support ≥ threshold; length ≤ 3 |
| Monotone | If it holds for X, every superset holds | Contains Milk; sum of nonnegative prices ≥ 100 |
| Succinct | Enforced by selecting eligible items or constructing candidates directly | Every item's category is Dairy |
| Convertible | Becomes monotone or anti-monotone under a suitable item order | Certain average-price constraints under sorted extension |

For nonnegative item prices, sum ≤ 100 is anti-monotone: once exceeded, adding items cannot repair it. Sum ≥ 100 is monotone: once satisfied, extension preserves it. If negative values are allowed, these conclusions can fail. An average can increase or decrease under arbitrary extension, so neither inference is valid without extra conditions.

For a query requiring Milk and support ≥ 2, prune low-support prefixes immediately, but do not prune a prefix solely because it does not yet contain Milk if future extension can add Milk. A length limit allows pruning after the maximum allowed size. Constraint conjunctions require combining the safe implications, not treating every condition as anti-monotone.

High-dimensional sparse data create many possible combinations even when each record has few present items. Restrict the pattern language, use sparse/vertical representations, choose meaningful support, and use closed/maximal patterns when their information tradeoff is acceptable. With $d$ items, $2^d-1$ nonempty itemsets exist in principle. No representation can make writing an exponentially large requested output take constant time.

## 14. Limitation of confidence

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

A high minimum support can miss useful rare patterns; a low one can generate too many patterns to inspect. Confidence has a different limitation: it ignores the baseline support of the consequent.

The observed joint support of tea and coffee is $0.15$. Under independence, the expected joint support would be $s(\text{Tea})s(\text{Coffee})=0.20\times0.80=0.16$. Since the observed value is smaller than the expected value, the association is negative.

## 15. Lift and interest factor

Lift corrects the confidence baseline:

$$
\operatorname{lift}(X\rightarrow Y)
=\frac{c(X\rightarrow Y)}{s(Y)}
=\frac{s(X\cup Y)}{s(X)s(Y)}.
$$

For binary counts, use the following contingency table; a + in a subscript denotes a marginal total.

|  | $B$ | $\neg B$ |
|---|---:|---:|
| $A$ | $f_{11}$ | $f_{10}$ |
| $\neg A$ | $f_{01}$ | $f_{00}$ |

Using these counts,

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

For example, a rule relating bread to bread packets of another size may have high support and confidence but little marketing value. An unexpected cross-category rule may be less frequent but more actionable.

## 16. Correlation, Cosine, and Interestingness Properties

Let $f_{1+}=f_{11}+f_{10}$, $f_{+1}=f_{11}+f_{01}$, and let the other margins be defined similarly. The binary Pearson correlation, or phi coefficient, is

$$
\phi=\frac{f_{11}f_{00}-f_{10}f_{01}}
{\sqrt{f_{1+}f_{0+}f_{+1}f_{+0}}}.
$$

Positive phi indicates association in the same direction and negative phi the opposite, when the denominator is nonzero. For Tea/Coffee, phi is $(150\cdot150-50\cdot650)/\sqrt{200\cdot800\cdot800\cdot200}=-0.0625$. This describes weak negative linear association between the binary indicators.

The **IS measure**, equivalent to cosine for binary indicator vectors, is

$$
IS(A,B)=\sqrt{\operatorname{lift}(A,B)s(AB)}
=\frac{s(AB)}{\sqrt{s(A)s(B)}}
=\frac{f_{11}}{\sqrt{f_{1+}f_{+1}}}.
$$

Tea/Coffee gives $150/\sqrt{200\cdot800}=0.375$. Cosine compares co-presence with the geometric mean of the individual frequencies; it does not use joint absence.

The two tables with counts $(880,50,50,20)$ and $(20,50,50,880)$ illustrate different questions. The first has confidence $880/930=0.946237$, lift $1000(880)/930^2=1.017459$, and cosine 0.946237. The second has confidence $20/70=0.285714$, lift $1000(20)/70^2=4.081633$, and cosine 0.285714. The second shows a larger increase over its much lower baseline, while the first shows much greater co-presence. Neither number alone establishes an unconditional ranking of “relatedness.” Both tables have phi 0.231951 because swapping both presence/absence labels preserves phi.

**Inversion invariance** means that swapping presence and absence for both variables leaves the measure unchanged. **Null-addition invariance** means that adding joint-zero records leaves it unchanged.

| Measure | Invariant when both binary labels are inverted? | Invariant to adding joint-zero records? |
|---|---|---|
| Relative support | No | No |
| Confidence | No | Yes |
| Lift | No | No |
| Phi | Yes | No |
| Cosine/IS | No | Yes |

Adding records with neither item increases $N$ while leaving the three co-presence/mismatch counts unchanged. Relative support falls and lift changes. Confidence and cosine remain unchanged. Inversion exchanges $f_{11}$ with $f_{00}$ and $f_{10}$ with $f_{01}$. Choose a measure whose treatment of absence and baseline frequency suits the task.

## 17. Worked 2025 FP-Growth Question

Map F1,C1,N1,B1,S1 to F,C,N,B,S. The six baskets are FC SNB, CSB, FCN, FB, FCNB, FCN, interpreted as sets. Minimum count is 3. Counts are F=5,C=5,N=4,B=4,S=2. Remove S and fix order F,C,N,B, including the declared tie order.

| TID | Filtered ordered basket |
|---|---|
| 1 | F,C,N,B |
| 2 | C,B |
| 3 | F,C,N |
| 4 | F,B |
| 5 | F,C,N,B |
| 6 | F,C,N |

Insert paths and increment shared prefixes:

```text
Root
  F:5
    C:4
      N:4
        B:2
    B:1
  C:1
    B:1
```

The header connects the two C nodes and the three B nodes; F and N each have one node. Mine suffixes from the end of the order:

| Suffix | Conditional pattern base | Frequent conditional structure | New non-singleton patterns |
|---|---|---|---|
| B | FCN:2; F:1; C:1 | F:3 with C:2 beneath it, plus root C:1 | FB:3; CB:3 |
| N | FC:4 | F:4 → C:4 | FN:4; CN:4; FCN:4 |
| C | F:4; empty prefix:1 | F:4 | FC:4 |
| F | Empty | Empty | None |

In B's conditional base, N occurs only twice and is removed. F and C each have count 3, but their joint conditional count is 2, so FCB is not frequent. The empty prefix for the root C contributes to C's singleton support without supporting FC.

The complete frequent collection is F:5,C:5,N:4,B:4; FC:4,FN:4,CN:4,FB:3,CB:3; FCN:4. No larger pattern meets support. The maximal patterns are **FCN, FB, and CB**. The conditional recursion has no further frequent extension, so it terminates.

## 18. Worked 2026 Apriori Question

Treat baskets as sets, so the repeated O in the printed last basket counts once:

| TID | Basket |
|---|---|
| 100 | M,O,N,K,E,Y |
| 200 | D,O,N,K,E,Y |
| 300 | M,A,K,E |
| 400 | M,U,C,K,Y |
| 500 | C,O,K,I,E |

There are five baskets. A 30% support threshold requires $\lceil0.3(5)\rceil=2$ occurrences. The singleton counts are C:2,E:4,K:5,M:3,N:2,O:3,Y:3; A,D,I,U each have count 1 and are removed.

All 21 pairs from the seven frequent singletons are candidates. The following 14 pairs survive:

```text
CK:2 EK:4 EM:2 EN:2 EO:3 EY:2 KM:3
KN:2 KO:3 KY:3 MY:2 NO:2 NY:2 OY:2
```

Rejected pairs are CE:1,CM:1,CN:0,CO:1,CY:1,MN:1,MO:1. For example, C has no frequent partner other than K, so no triple containing C survives subset pruning.

The triple candidates whose every pair is frequent are:

```text
EKM EKN EKO EKY EMY ENO ENY EOY KMY KNO KNY KOY NOY
```

EMY has count 1 and is removed; the other counts are:

```text
EKM:2 EKN:2 EKO:3 EKY:2 ENO:2 ENY:2
EOY:2 KMY:2 KNO:2 KNY:2 KOY:2 NOY:2
```

The five surviving four-item candidates are EKNO,EKNY,EKOY,ENOY,KNOY, each with count 2. They generate EKNOY, whose count is also 2. No six-item candidate exists, so stop.

| Level | Candidate count after subset pruning | Frequent count |
|---|---:|---:|
| 1 | 11 | 7 |
| 2 | 21 | 14 |
| 3 | 13 | 12 |
| 4 | 5 | 5 |
| 5 | 1 | 1 |
| 6 | 0 | 0 |

For every frequent $F$ of size at least 2, generate every nonempty proper antecedent $A$ and use $A\rightarrow F\setminus A$. All these rules meet support. Here they also all meet the 40% confidence threshold: $\sigma(F)\ge2$ while $\sigma(A)\le5$, so confidence is at least $2/5=0.4$.

The complete answer can therefore be specified exactly as

$$
\{A\rightarrow F\setminus A:\ F\in L_2\cup L_3\cup L_4\cup L_5,
\ \varnothing\ne A\subsetneq F\}.
$$

This denotes **200 strong rules**: $14(2)+12(6)+5(14)+1(30)=200$. It is an exhaustive description, not a sample of the rule set. For illustration, K→C has support 40% and confidence 40%; EO→K has support 60% and confidence 100%; M→EK has support 40% and confidence $2/3$. The generating itemsets and the formula determine every remaining rule without a repetitive 200-row listing.

## 19. Sources

Primary source: `sources/4_Association_Rules.pdf`. Vertical, multilevel, multidimensional, and constrained mining also use the shared textbook, Chapter 5. Worked questions use the 2025 and 2026 semester papers. The lecture's maximal-pattern list omits singleton b; the corrected FP-growth example includes it.
