# Chapter 7. Classification and Clustering

[Contents](../readme.md)

## 7.1 Linear classification and logistic regression

A linear classifier uses the sign of $f(x)=w^Tx+b$. For $w\ne0$, the normal is $w$ and the signed distance to the boundary is $f(x)/\lVert w\rVert$. Logistic regression uses $p=\sigma(f(x))$, so the log-odds equal $f(x)$. Here the binary target is coded as $y\in\{0,1\}$. At probability threshold $0.5$, the boundary is $f(x)=0$. Linear regression predicts an unrestricted real value and commonly fits squared error.

For a Bernoulli target $y$, the likelihood of one example is $p^y(1-p)^{1-y}$. Its negative log is

$$
\ell=-y\ln p-(1-y)\ln(1-p).
$$

The gradients for a single example are $(p-y)x$ and $p-y$ with respect to $w$ and $b$. The optimizer fits both parameters and thereby learns the boundary. At $x=2,y=1,w=b=0$, $p=0.5$ and the gradients are $(-1,-0.5)$. One step at $\eta=0.1$ gives $(w,b)=(0.1,0.05)$ and shifts the one-dimensional boundary to $x=-0.5$.

**Worked log-loss calculation.** For $(y,p)=(1,0.9),(0,0.2),(1,0.3)$, the losses are $0.1054,0.2231,1.2040$. Their mean is $0.5108$. The last example contributes the most because it gives the lowest probability to the correct class.

### Thresholds, features, and model selection

A threshold $\tau$ predicts positive when $p\ge\tau$, equivalent to a score threshold $\ln[\tau/(1-\tau)]$. Raising the threshold usually trades recall for precision. With false-positive cost $C_{\mathrm{FP}}$ and false-negative cost $C_{\mathrm{FN}}$, predict positive if a calibrated $p$ exceeds $C_{\mathrm{FP}}/(C_{\mathrm{FP}}+C_{\mathrm{FN}})$.

- **Interactions and nonlinear transforms:** Adding $x_1x_2$ or $x_1^2$ lets the boundary curve in the original coordinates while staying linear in the expanded features.
- **Regularization:** Tune penalty strength using validation or cross-validation. Scale inputs where the penalty or optimizer is sensitive to their units.
- **Multiple classes:** Softmax regression estimates one probability distribution over mutually exclusive classes. One-versus-rest fits separate binary classifiers.

## 7.2 Fisher LDA and Gaussian LDA

Fisher's linear discriminant projects classes to directions with large separation between class means and small scatter within classes. Compute each class mean $\mu_k$ and scatter $S_k=\sum_{x\in k}(x-\mu_k)(x-\mu_k)^T$. With $S_W=S_A+S_B$ and $d=\mu_B-\mu_A$,

$$
J(w)=\frac{(w^Td)^2}{w^TS_Ww},\qquad w\propto S_W^{-1}d
$$

when $S_W$ is invertible. The solution is a direction. Scaling it by a positive constant scales the projected scores and the decision threshold together. The multiclass version uses generalized eigenvectors and at most $C-1$ discriminant directions for $C$ classes.

**Worked Fisher projection.** Take $A=\{(1,1),(2,1),(1,2)\}$ and $B=\{(4,4),(5,4),(4,5)\}$. The means are $(4/3,4/3)$ and $(13/3,13/3)$. Each class scatter has diagonal $2/3$ and off-diagonal $-1/3$. For class A the deviations are $(-1,-1)/3$, $(2,-1)/3$, and $(-1,2)/3$. Sum their outer products to obtain $S_A$.

$$
S_A=\frac19\left[
\begin{pmatrix}1&1\\1&1\end{pmatrix}+
\begin{pmatrix}4&-2\\-2&1\end{pmatrix}+
\begin{pmatrix}1&-2\\-2&4\end{pmatrix}
\right]=
\begin{pmatrix}2/3&-1/3\\-1/3&2/3\end{pmatrix}.
$$

B is a translation of A, so $S_B=S_A$.

$$
S_W=\begin{bmatrix}4/3&-2/3\\-2/3&4/3\end{bmatrix},
\qquad
S_W^{-1}d=\begin{bmatrix}4.5\\4.5\end{bmatrix}.
$$

Use the equivalent direction $(1,1)$. The projected means are $8/3$ and $26/3$. With equal priors and equal projected variances, the midpoint threshold is $17/3$. Predict B when $x_1+x_2>17/3$.

Gaussian LDA models each class with a Gaussian mean, a shared covariance $\Sigma$, and prior $\pi_k$. It selects the largest score $x^T\Sigma^{-1}\mu_k-\frac12\mu_k^T\Sigma^{-1}\mu_k+\log\pi_k$. The common quadratic term cancels, producing linear boundaries. Unequal priors can shift the threshold.

### How LDA compares with nearby methods

| Method | What it uses | What to watch |
|---|---|---|
| PCA | Unlabeled feature variance | A high-variance direction may not separate classes. |
| Fisher LDA | Labels and within-class versus between-class scatter | Center points at their own class means for $S_W$. If $S_W$ is singular, consider shrinkage or reducing dimension. |
| Gaussian LDA | Class means, priors, and shared covariance | Unequal priors move the boundary. Class-specific covariances generally give a quadratic boundary. |
| Logistic regression | Conditional class probability fitted directly | Does not require Gaussian features. It assumes linear log-odds in the chosen features. |
| Least-squares discriminant | Fits numeric class codes and classifies using the fitted scores | Squared error treats deviations in class codes as numeric residuals. Scores need not be probabilities. |

For $C$ classes, define $S_B=\sum_k n_k(\mu_k-\mu)(\mu_k-\mu)^T$. Fisher directions solve $S_Bw=\lambda S_Ww$. Select directions with large generalized eigenvalues. The rank of $S_B$ is at most $C-1$.

## 7.3 Support vector machines

The SVM convention here uses labels $y_i\in\{-1,+1\}$. The score $f(x)$ determines the side of the boundary. The product $y_if(x_i)$ is the signed score relative to the true label.

| Model | Objective and constraints |
|---|---|
| Hard margin | Minimize $\frac12\lVert w\rVert^2$ with $y_i(w^Tx_i+b)\ge1$ |
| Soft margin | Minimize $\frac12\lVert w\rVert^2+C\sum_i\xi_i$ with $y_i(w^Tx_i+b)\ge1-\xi_i,\;\xi_i\ge0$ |

To derive the width, move a distance $d$ along the unit normal $w/\lVert w\rVert$. The score increases by $d\lVert w\rVert$. Going from $f(x)=-1$ to $f(x)=1$ requires a score change of 2, hence $d=2/\lVert w\rVert$. Maximizing that distance gives the hard-margin objective.

For soft margin, the smallest feasible slack is $\xi_i=\max(0,1-y_if(x_i))$, the hinge loss. If $y_if(x_i)=0.4$, the point is correctly classified but incurs slack $0.6$. If $y_if(x_i)=-0.4$, the point is misclassified and incurs slack $1.4$. Larger $C$ penalizes violations more strongly.

**Worked maximum-margin boundary.** Two apples lie at $(1,2)$ and $(2,1)$, and the nearest orange is at $(4,4)$. The apple hull touches the margin on the segment joining those apples. The nearest point on that segment to the orange is $(1.5,1.5)$. The line between these two hull points has direction $(1,1)$, so write $w=(a,a)$. The margin equations are $3a+b=-1$ and $8a+b=1$. Subtracting gives $a=0.4$ and then $b=-2.2$.

The center line is $x_1+x_2=5.5$. The margin lines are $x_1+x_2=3$ and $x_1+x_2=8$, and their separation is $2/\sqrt{0.32}\approx3.54$. In general, solve the constrained optimization. In the dual solution, points with positive multipliers $\alpha_i$ are support vectors and contribute to $w=\sum_i\alpha_i y_ix_i$.

A kernel evaluates a feature-space inner product without explicitly constructing the feature vector. Prediction uses $f(x)=\sum_i\alpha_i y_i K(x_i,x)+b$. An RBF kernel is $K(x,z)=\exp(-\gamma\lVert x-z\rVert^2)$. Use $\gamma>0$ for the RBF kernel. Its decision boundary can be curved in the original space. Soft margin can also be used with a kernel.

**Worked nonlinear boundary.** For XOR, map $(x_1,x_2)$ to $(x_1,x_2,x_1x_2)$. The linear score in this space is $2x_1+2x_2-4x_1x_2-1$. It gives $(-1,+1,+1,-1)$ on $(0,0),(1,0),(0,1),(1,1)$. The interaction term changes the score only when both inputs are 1, which a linear score on the original inputs cannot do.

### Training and interpreting SVMs

The soft-margin objective equals a squared weight penalty plus $C$ times the sum of hinge losses. For a point with $y_if(x_i)<1$, the hinge subgradient with respect to $w$ is $-y_ix_i$. For $y_if(x_i)>1$ it is zero. At equality, either endpoint or a convex combination is valid. Include the regularizer’s gradient when updating the weights.

The dual chooses multipliers with $\alpha_i\ge0$ and $\sum_i\alpha_i y_i=0$. The soft-margin case adds $\alpha_i\le C$. It uses only inner products, which the kernel replaces. For a soft-margin support vector with $0<\alpha_i<C$, solve $y_if(x_i)=1$ to recover the bias. A support vector at the upper bound can lie inside the margin.

Written explicitly, the dual maximizes $\sum_i\alpha_i-\frac12\sum_{i,j}\alpha_i\alpha_jy_iy_jK(x_i,x_j)$ under the constraints above. For a linear kernel, the fitted normal is $\sum_i\alpha_i y_ix_i$. For a nonlinear kernel, evaluate the score using the support vectors. Examples with $\alpha_i=0$ do not contribute to this score.

| Decision | How to make it |
|---|---|
| Tune $C$ and RBF $\gamma$ | Use validation after scaling. Large $\gamma$ makes similarity fall off quickly. Large $C$ makes violations more expensive. |
| Extend to several classes | Fit one-versus-rest models or one-versus-one pairwise models and combine their scores or votes. |
| Estimate probabilities | The raw margin is a score. Platt scaling fits a sigmoid to out-of-sample scores so the resulting probability can be evaluated for calibration. |
| Compare feature subsets | Tune the feature selection and SVM together. For a linear model on comparable scales, coefficients can help rank features. Evaluate the selected subset on held-out data. |

The syllabus groups these topics under *Classification and Clustering*. Its listed methods in this block are classifiers. Clustering is the unsupervised task of grouping examples by structure or similarity.

### Other classifiers and clustering in the sources

| Method | Decision rule |
|---|---|
| $k$-nearest neighbors | Use a vote among nearby labeled examples. The distance measure, feature scaling, and $k$ affect the boundary. |
| Decision tree | Split by feature tests that improve class purity. Control depth or prune using validation. |
| Generalized linear model | Relates the mean to a linear predictor through a link function. Logistic regression uses the logit link for a Bernoulli response. |
| $k$-means | Alternate assigning unlabeled points to their nearest center and recomputing each center. The choice of $k$, initialization, and scale matter. |

## Practice

- **7A.** For $z=\ln3$ and target 0, calculate the logistic probability and loss.
- **7B.** Reproduce the two scatter matrices in the Fisher example. Explain why changing the sign of $w$ reverses the threshold inequality.
- **7C.** For $f(x)=3x_1+4x_2-2$ in canonical SVM scaling, give the unit normal, both margin equations, and total width.

## Answer checks

| Exercise | Answer |
|---|---|
| 7A | $p=0.75$, so $L=-\ln(1-0.75)\approx1.3863$. |
| 7B | Both class scatter matrices have diagonal $2/3$ and off-diagonal $-1/3$. Negating $w$ and the threshold negates both sides of the comparison, reversing the inequality. |
| 7C | Unit normal $(3/5,4/5)$. The margins are $3x_1+4x_2-2=\pm1$. Full width $2/5$. |
[Chapter sources](../readme.md#7-classification-and-clustering)

---

[← Chapter 6](../06%20-%20Deep%20Learning/Deep%20Learning.md) · [Contents](../readme.md) · [Chapter 8 →](../08%20-%20Reinforcement%20Learning/Reinforcement%20Learning.md)
