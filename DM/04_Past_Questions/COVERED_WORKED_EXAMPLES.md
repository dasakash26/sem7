# Covered PYQ Worked Examples

These solutions present the required examination working. Only questions supported by the supplied lecture notes are included.

## 1. 2026 Q2 b Bin-mean smoothing

Given

$$
7,12,5,8,5,9,13,12,19,7,12,12,13,3,4,5,13,8,7,6,
$$

the first compulsory step is sorting:

$$
3,4,5,5,5,6,7,7,7,8,8,9,12,12,12,12,13,13,13,19.
$$

A bin depth of 3 means three observations per bin. The final bin contains the remaining two observations.

| Bin | Original values | Mean | Smoothed bin |
|---|---|---:|---|
| $B_1$ | $3,4,5$ | $4$ | $4,4,4$ |
| $B_2$ | $5,5,6$ | $5.33$ | $5.33,5.33,5.33$ |
| $B_3$ | $7,7,7$ | $7$ | $7,7,7$ |
| $B_4$ | $8,8,9$ | $8.33$ | $8.33,8.33,8.33$ |
| $B_5$ | $12,12,12$ | $12$ | $12,12,12$ |
| $B_6$ | $12,13,13$ | $12.67$ | $12.67,12.67,12.67$ |
| $B_7$ | $13,19$ | $16$ | $16,16$ |

Hence the smoothed sequence is

$$
4,4,4,5.33,5.33,5.33,7,7,7,8.33,8.33,8.33,
12,12,12,12.67,12.67,12.67,16,16.
$$

Where integer output is required, the rounding rule should be stated explicitly.

## 2. 2026 Q2 c Boxplot outliers

After sorting, the data is

$$
\begin{aligned}
&0.11,0.18,0.23,0.51,1.19,1.30,1.32,1.73,2.06,2.16,2.37,\\
&2.91,4.50,4.51,4.66,14.68,14.82,27.44,39.41,41.04,49.09,60.05.
\end{aligned}
$$

Using the median-of-halves convention,

$$
\text{median}=\frac{2.37+2.91}{2}=2.64,
$$

$$
Q_1=1.30,\qquad Q_3=14.82.
$$

Therefore,

$$
\operatorname{IQR}=Q_3-Q_1=14.82-1.30=13.52.
$$

The fences are

$$
Q_1-1.5\operatorname{IQR}
=1.30-1.5(13.52)
=-18.98,
$$

$$
Q_3+1.5\operatorname{IQR}
=14.82+1.5(13.52)
=35.10.
$$

Thus the potential outliers are

$$
\boxed{39.41,\ 41.04,\ 49.09,\ 60.05}.
$$

A boxplot identifies unusual observations but does not establish that they are erroneous.

## 3. 2026 Q3 Apriori

Each transaction is treated as a set. The repeated $O$ in $T500$ therefore contributes once to the support count.

| TID | Itemset |
|---|---|
| T100 | $\{M,O,N,K,E,Y\}$ |
| T200 | $\{D,O,N,K,E,Y\}$ |
| T300 | $\{M,A,K,E\}$ |
| T400 | $\{M,U,C,K,Y\}$ |
| T500 | $\{C,O,K,I,E\}$ |

There are five transactions and minimum support is $30\%$. Hence

$$
\text{minimum support count}
=\left\lceil0.30\times5\right\rceil
=2.
$$

### Frequent itemsets

$$
L_1=\{C:2,E:4,K:5,M:3,N:2,O:3,Y:3\}.
$$

$$
\begin{aligned}
L_2=\{&CK:2,EK:4,EM:2,EN:2,EO:3,EY:2,KM:3,\\
      &KN:2,KO:3,KY:3,MY:2,NO:2,NY:2,OY:2\}.
\end{aligned}
$$

$$
\begin{aligned}
L_3=\{&EKM:2,EKN:2,EKO:3,EKY:2,ENO:2,ENY:2,\\
      &EOY:2,KMY:2,KNO:2,KNY:2,KOY:2,NOY:2\}.
\end{aligned}
$$

$$
L_4=\{EKNO:2,EKNY:2,EKOY:2,ENOY:2,KNOY:2\}.
$$

$$
L_5=\{EKNOY:2\}.
$$

No larger frequent itemset exists.

### How to generate strong rules

For a frequent itemset $F$ and non-empty $A\subset F$,

$$
c(A\rightarrow F-A)=\frac{\sigma(F)}{\sigma(A)}.
$$

Representative calculations are:

$$
c(N\rightarrow EKOY)=\frac{2}{2}=100\%,
\qquad s=\frac{2}{5}=40\%,
$$

$$
c(EO\rightarrow K)=\frac{3}{3}=100\%,
\qquad s=\frac{3}{5}=60\%,
$$

$$
c(M\rightarrow EK)=\frac{2}{3}=66.67\%,
\qquad s=\frac{2}{5}=40\%,
$$

$$
c(E\rightarrow M)=\frac{2}{4}=50\%,
\qquad s=\frac{2}{5}=40\%,
$$

$$
c(K\rightarrow C)=\frac{2}{5}=40\%,
\qquad s=\frac{2}{5}=40\%.
$$

All five rules meet the $40\%$ confidence threshold. Since the threshold is low, many rules qualify; they should be organized under their generating frequent itemsets.

## 4. 2025 Q3 b FP-growth result

For the six transactions in the paper and minimum support count 3, the item counts are

$$
F:5,\qquad C:5,\qquad N:4,\qquad B:4,\qquad S:2.
$$

$S$ is removed because it is infrequent. Choose and state one consistent tie order, for example

$$
F>C>N>B.
$$

The important frequent patterns are

$$
FC:4,\quad FN:4,\quad CN:4,\quad FB:3,\quad CB:3,
$$

and

$$
FCN:4.
$$

Therefore, the maximal frequent itemsets are

$$
\boxed{\{F,C,N\},\ \{F,B\},\ \{C,B\}}.
$$

The final patterns do not replace the required FP-growth derivation. A complete answer should show:

1. the first-pass counts and frequency order;
2. filtered, reordered transaction paths;
3. the FP-tree and header links;
4. conditional pattern bases and conditional trees;
5. the patterns generated from each suffix.
