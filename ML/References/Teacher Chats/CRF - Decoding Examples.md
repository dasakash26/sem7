# Explain CRF Functionality

[Original teacher-shared chat](https://chatgpt.com/share/6aac0b76-691c-83ee-9663-87356ff52c76)

Text transcript from the shared page. Interactive elements, images, and original formatting may differ.

---

## 1. Teacher prompt

explain the functionality of conditional random field (CRF) with proper examples.

---

## 2. ChatGPT response

Absolutely. Let us understand Conditional Random Field (CRF) step by step, especially in the context of NLP sequence labeling, where CRFs are commonly used for tasks such as POS tagging, Named Entity Recognition (NER), chunking, etc.

### Conditional Random Field (CRF)

A Conditional Random Field (CRF) is a probabilistic model used to predict a sequence of labels for a sequence of observations.

For example, suppose the input sentence is:

Rohini lives in Kolkata

We may want to identify the type of each word:

Word | Label | 
Rohini | PERSON | 
lives | O | 
in | O | 
Kolkata | LOCATION | 

Here, the words are observations and the labels are hidden/output variables.

A CRF learns how to choose the best sequence of labels for the entire sentence, rather than deciding the label of each word independently.

### 1. Why do we need CRF?

Consider the sentence:

Washington is beautiful.

The word Washington could be:

a PERSON

a LOCATION

an ORGANIZATION

If we look only at the word itself, it may be difficult to decide.

But consider:

President Washington visited France.

Here, Washington is probably a PERSON.

Or:

Washington is the capital of the USA.

Here, Washington is a LOCATION.

So the label of a word depends not only on the word itself but also on its context.

CRF handles this contextual dependency.

### 2. Basic idea

Suppose our input sentence is:

X=(x1​,x2​,x3​,…,xn​)
where each xi​ is a word.

We want to predict:

Y=(y1​,y2​,y3​,…,yn​)
where each yi​ is a label.

For example:

X=(Rohini,lives,in,Kolkata)
and

Y=(PERSON,O,O,LOCATION)
A CRF calculates:

P(Y∣X)
That means:

What is the probability of a particular label sequence Y, given the observed sentence X?

The CRF then chooses the label sequence having the highest probability.

Y∗=argYmax​P(Y∣X)

### 3. CRF is a discriminative model

This is an important point.

A CRF directly models:

P(Y∣X)
It does not try to model:

P(X,Y)
This is why CRF is called a discriminative probabilistic model.

Compare this with an HMM.

### HMM

An HMM models:

P(X,Y)
and uses:

P(Y∣X)=P(X)P(X,Y)​

### CRF

CRF directly models:

P(Y∣X)
So:

HMM | CRF | 
Generative model | Discriminative model | 
Models P(X,Y) | Models P(Y∣X) | 
Makes stronger assumptions about observations | Can use many arbitrary features | 
Emission probabilities are important | Feature functions are important | 
Commonly solved using Viterbi | Also commonly decoded using Viterbi | 

### 4. The most important idea: feature functions

The heart of a CRF is the concept of a feature function.

A feature function examines the input and labels and gives a score.

For example:

f(xi​,yi​)
could ask:

"Is the current word 'Kolkata' and is the current label LOCATION?"

If yes:

f=1
otherwise:

f=0
Another feature could examine two consecutive labels:

f(yi−1​,yi​)
For example:

"Does a PERSON label normally follow another PERSON label?"

Or:

"Does LOCATION follow O?"

Thus CRF can consider both:

Observation features

Label-transition features

### 5. Example of feature functions

Consider:

Rohini lives in Kolkata

Possible labels:

PERSON, LOCATION, O
Suppose we are evaluating:

Rohini → PERSON

We could have features such as:

### Feature 1: Word identity

f1​(xi​,yi​)=1
if:

word = Rohini AND label = PERSON

This is a strong positive feature.

### Feature 2: Capitalization

f2​(xi​,yi​)=1
if:

word begins with a capital letter AND label = PERSON

This can be useful because names often begin with capital letters.

### Feature 3: Previous word

For Kolkata:

f3​(xi−1​,xi​,yi​)
might ask:

Is the previous word "in" and is the current label LOCATION?

For:

in Kolkata

the feature would fire.

### Feature 4: Label transition

f4​(yi−1​,yi​)
might ask:

Does O → LOCATION occur?

For:

O→LOCATION
the feature might have a positive weight.

### 6. CRF assigns weights to features

Suppose we have several feature functions:

f1​,f2​,f3​,…,fk​
CRF assigns a weight to each feature:

w1​,w2​,w3​,…,wk​
The total score of a label sequence can then be written as:

Score(X,Y)=i=1∑n​k∑​wk​fk​(yi−1​,yi​,X,i)
In simple words:

Score = weighted sum of all the features that fire throughout the sentence.

A positive weight increases the score.

A negative weight decreases the score.

### 7. A simple numerical example

Suppose we want to label:

Kolkata

with either:

PERSON

LOCATION

Suppose our CRF has these features:

Feature | Weight | 
Word is "Kolkata" + LOCATION | +5 | 
Word starts with capital + LOCATION | +1 | 
Word is "Kolkata" + PERSON | -2 | 
Previous word is "in" + LOCATION | +3 | 

Now consider:

I live in Kolkata

For:

Kolkata→LOCATION
the score might be:

5+1+3=9
For:

Kolkata→PERSON
the score might be:

−2
Therefore:

Score(LOCATION)=9
Score(PERSON)=−2
So CRF strongly prefers:

Kolkata→LOCATION

### 8. CRF considers the entire sequence

This is the major strength of CRF.

Suppose:

Rohini lives in Kolkata

Consider this possible output:

PERSON,O,O,LOCATION
CRF calculates a score for the whole sequence.

It does not simply do:

Rohini → PERSON

lives → O

in → O

Kolkata → LOCATION

independently.

Instead, it evaluates relationships such as:

PERSON→O
O→O
O→LOCATION
along with the relationship between words and labels.

Thus:

Score(X,Y)
represents the quality of the entire labeling sequence.

### 9. The CRF probability equation

The conditional probability is generally written as:

P(Y∣X)=Z(X)exp(Score(X,Y))​
where:

Z(X)=Y′∑​exp(Score(X,Y′))
is called the partition function or normalization factor.

Therefore:

P(Y∣X)=∑Y′​exp(∑i​∑k​wk​fk​(yi−1′​,yi′​,X,i))exp(∑i​∑k​wk​fk​(yi−1​,yi​,X,i))​
The denominator considers all possible label sequences.

This makes the probabilities sum to 1.

### 10. A small complete example

Let's use a very small sentence:

John works

Suppose the possible labels are:

PERSON, O
There are:

22=4
possible label sequences.

They are:

PERSON, PERSON

PERSON, O

O, PERSON

O, O

Suppose after training, the CRF gives the following scores:

Label sequence | Score | 
PERSON, PERSON | 1 | 
PERSON, O | 5 | 
O, PERSON | 0 | 
O, O | 2 | 

The highest score is:

PERSON,O
Therefore CRF predicts:

Word | Label | 
John | PERSON | 
works | O | 

### 11. Why probability is needed

The scores themselves aren't probabilities.

We convert scores to probabilities using the exponential function.

For example:

P(Y∣X)=∑Y′​eScore(Y′)eScore(Y)​
For our example:

Z=e1+e5+e0+e2
The sequence:

PERSON,O
has score 5, so:

P(PERSON,O∣X)=e1+e5+e0+e2e5​
It will have the largest probability because it has the largest score.

### 12. How CRF is trained

Suppose we have training sentences where the correct labels are already known.

For example:

Sentence | Correct labels | 
Rohini lives in Kolkata | PERSON O O LOCATION | 
John works at Google | PERSON O O ORGANIZATION | 
Mary lives in Delhi | PERSON O O LOCATION | 

The CRF learns appropriate weights for its feature functions.

For example, during training it may learn:

w("Kolkata"→LOCATION)=4.8
w("Delhi"→LOCATION)=4.5
w("Google"→ORGANIZATION)=4.2
and perhaps:

w(PERSON→O)=2.3
The exact weights are learned automatically from the training data.

### 13. What does training try to accomplish?

Suppose the training sentence is:

Rohini lives in Kolkata

with correct labels:

PERSON,O,O,LOCATION
The CRF should assign a high probability to this correct sequence.

So training attempts to maximize:

P(Ycorrect​∣X)
or, more conveniently, the log-likelihood:

logP(Ycorrect​∣X)
The model adjusts the feature weights until the correct label sequences receive high probability.

### 14. Testing / prediction

After training, suppose we give:

Soham lives in Delhi

The CRF extracts features such as:

Soham begins with a capital letter

Delhi begins with a capital letter

"in" occurs before Delhi

possible label transitions

It evaluates possible sequences.

For example:

PERSON,O,O,LOCATION
might get a high score.

Another sequence:

O,O,O,PERSON
might get a much lower score.

Therefore:

Y∗=argYmax​Score(X,Y)
and the prediction becomes:

Word | Predicted label | 
Soham | PERSON | 
lives | O | 
in | O | 
Delhi | LOCATION | 

### 15. CRF and Viterbi algorithm

There is an important connection with your earlier study of HMM and Viterbi.

In an HMM, Viterbi finds:

argYmax​P(X,Y)
In a CRF, Viterbi can find:

argYmax​P(Y∣X)
Since:

P(Y∣X)=Z(X)exp(Score(X,Y))​
and Z(X) is the same for every candidate sequence for a given X, maximizing probability is equivalent to maximizing the score:

Y∗=argYmax​Score(X,Y)
Therefore, Viterbi-style dynamic programming can efficiently find the best label sequence without explicitly enumerating every possible sequence.

### 16. CRF vs HMM

This comparison is particularly useful.

Suppose we want to perform POS tagging.

Sentence:

The dog runs

Possible tags:

DET, NOUN, VERB

### HMM

HMM uses:

P(Tagi​∣Tagi−1​)
and:

P(Wordi​∣Tagi​)
Therefore:

P(X,Y)=i∏​P(yi​∣yi−1​)P(xi​∣yi​)
It makes assumptions about how observations are generated from states.

### CRF

CRF can use arbitrary features such as:

Current word

Previous word

Next word

Prefix

Suffix

Capitalization

Digits

Previous label

Previous two labels

Word shape

Whether the word occurs in a dictionary

For example:

"Does the word end in -ing?"

This could be a feature.

Thus CRF has much greater flexibility in incorporating contextual information.

### 17. CRF vs HMM: an intuitive example

Consider:

The beautiful Kolkata

Suppose we want to determine the POS tag of "Kolkata".

An HMM might primarily depend on:

P(Kolkata∣NOUN)
and the preceding tag.

A CRF can use much richer information:

Kolkata starts with capital letter

It is in a city dictionary

Previous word is "beautiful"

Previous word's POS tag is ADJECTIVE

The word itself is Kolkata

Surrounding words

All these can contribute to the final decision.

### 18. CRF structure

A simple linear-chain CRF can be visualized as:

Observations:

 x1          x2          x3          x4
Rohini      lives        in       Kolkata
 |           |            |           |
 v           v            v           v
 y1          y2           y3          y4
PERSON       O            O        LOCATION
   ↕          ↕            ↕
transition  transition  transition

There are two types of relationships:

### Observation → Label

xi​→yi​
For example:

Kolkata→LOCATION

### Label → Label

yi−1​→yi​
For example:

O→LOCATION
CRF combines both.

### 19. Why is it called a "Conditional Random Field"?

Let's break the name down.

### Conditional

It models:

P(Y∣X)
The labels are modeled conditional on the observed input.

### Random

The labels are treated as random variables.

### Field

The model represents relationships between variables across a sequence/graph.

So:

Conditional Random Field = a probabilistic model that represents the conditional distribution of output variables given observed variables.

### 20. One very important difference from independent classification

Suppose we use a normal classifier independently on every word.

For:

New York

it might predict:

New    → LOCATION
York   → PERSON

which is inconsistent.

A CRF can learn that certain label sequences are more likely, such as:

New    → B-LOCATION
York   → I-LOCATION

Therefore, CRFs are especially useful for sequence labeling.

### 21. CRF for Named Entity Recognition

This is one of the classic applications.

Sentence:

Rohini works at Google in Kolkata.

We might use BIO labels:

Word | Label | 
Rohini | B-PER | 
works | O | 
at | O | 
Google | B-ORG | 
in | O | 
Kolkata | B-LOC | 

The CRF learns:

### Word features

Google→B−ORG

### Context features

at Google→B−ORG

### Transition features

B−ORG→O
and so on.

The final output is chosen by considering all these features together.

### 22. The overall CRF process

The entire functionality can be summarized as:

             TRAINING DATA
                   |
                   v
        -----------------------
        | Feature Extraction |
        -----------------------
                   |
                   v
        -----------------------
        | Learn Feature       |
        | Weights             |
        -----------------------
                   |
                   v
              TRAINED CRF
                   |
                   v
             New Sentence
                   |
                   v
        -----------------------
        | Extract Features    |
        -----------------------
                   |
                   v
        Calculate sequence
              scores
                   |
                   v
        -----------------------
        | Viterbi / Dynamic   |
        | Programming         |
        -----------------------
                   |
                   v
        Best Label Sequence

### 23. The key mathematical picture

The entire CRF can be understood through these three equations:

### Step 1: Calculate score

Score(X,Y)=i∑​k∑​wk​fk​(yi−1​,yi​,X,i)

### Step 2: Convert score to probability

P(Y∣X)=∑Y′​eScore(X,Y′)eScore(X,Y)​

### Step 3: Select the best sequence

Y∗=argYmax​P(Y∣X)
or equivalently:

Y∗=argYmax​Score(X,Y)
The third step is where Viterbi-style dynamic programming is commonly used for a linear-chain CRF.

### 24. In one sentence

The simplest way to remember CRF is:

A CRF predicts the best sequence of labels by assigning scores to the relationships between words, their features, and neighboring labels, and then selecting the highest-scoring complete label sequence.

And compared with an HMM:

HMM asks "How could these observations have been generated by these states?", whereas CRF asks "Given these observations, which label sequence is most appropriate?"

If you are studying HMM → Viterbi → CRF, the next useful step is to work through a complete numerical CRF example with 3 words and 2–3 labels, calculating every feature score, transition score, probability, and the Viterbi table by hand.

---

## 3. Teacher prompt

Show the simulation with another example.

---

## 4. ChatGPT response

Sure. Let's do a complete numerical simulation of a CRF with a small example so that you can see exactly how the model chooses the final label sequence.

We'll use Named Entity Recognition (NER) because it makes the working of CRF easy to visualize.

### Example

Suppose the input sentence is:

Riya visits Delhi

We want to assign one of three labels to each word:

PER = Person

LOC = Location

O = Other

So the input is:

X=(Riya,visits,Delhi)
We want to find:

Y=(y1​,y2​,y3​)

### 1. Possible labels

For each word:

yi​∈{PER,LOC,O}
Therefore, there are:

33=27
possible label sequences.

For example:

PER PER PER
PER PER LOC
PER PER O
PER LOC PER
PER LOC LOC
PER LOC O
...
O O O

A CRF doesn't simply look at each word independently. It calculates a score for each possible sequence.

### 2. Suppose the trained CRF has learned these feature weights

Let's keep the model very simple.

### Word-label features

Suppose it has learned:

Feature | Weight | 
Riya → PER | +4 | 
Riya → LOC | -2 | 
Riya → O | -1 | 
Delhi → LOC | +5 | 
Delhi → PER | -2 | 
Delhi → O | -1 | 
visits → O | +2 | 
visits → PER | -1 | 
visits → LOC | -1 | 

These weights represent what the CRF learned from its training data.

For example:

w(Riya,PER)=4
means that assigning PER to "Riya" strongly increases the score.

### 3. Transition weights

Now suppose the CRF has also learned relationships between consecutive labels.

For example:

Previous → Current | Weight | 
PER → O | +2 | 
PER → PER | -1 | 
PER → LOC | -1 | 
O → O | +1 | 
O → PER | +1 | 
O → LOC | +2 | 
LOC → O | +2 | 
LOC → PER | -1 | 
LOC → LOC | -1 | 

These are called transition features.

For example:

PER→O
has weight:

+2
So if the previous word is labeled PER and the next word is labeled O, the score increases by 2.

### 4. Now consider one possible sequence

Let's first examine:

PER,O,LOC
That means:

Riya       → PER
visits     → O
Delhi      → LOC

This is intuitively the correct answer.

Now let's calculate its score.

### 5. Score for Riya → PER

From our feature table:

Riya→PER=+4
So:

Score1​=4

### 6. Transition PER → O

We have:

PER→O=+2
Therefore:

Score2​=2

### 7. Score for visits → O

From our table:

visits→O=+2
Therefore:

Score3​=2

### 8. Transition O → LOC

We have:

O→LOC=+2
Therefore:

Score4​=2

### 9. Score for Delhi → LOC

From the word-label feature:

Delhi→LOC=+5
Therefore:

Score5​=5

### 10. Total score

The total score is:

Score(X,Y)=4+2+2+2+5
Therefore:

Score(X,Y)=15​
So CRF gives the sequence:

Riya       visits       Delhi
 ↓           ↓            ↓
PER          O           LOC

a score of 15.

### 11. But CRF must compare it with other sequences

This is extremely important.

CRF doesn't say:

"PER is the best label for Riya, so I'm finished."

Instead, it asks:

Which complete label sequence has the highest score?

Let's compare some other possible sequences.

### Sequence 2

Consider:

PER,PER,LOC
That means:

Riya       → PER
visits     → PER
Delhi      → LOC

Calculate:

### Riya → PER

+4

### PER → PER

−1

### visits → PER

−1

### PER → LOC

−1

### Delhi → LOC

+5
Total:

4−1−1−1+5
Score=6​
Much lower than 15.

### 12. Sequence 3

Consider:

O,O,LOC
That means:

Riya       → O
visits     → O
Delhi      → LOC

Score:

### Riya → O

−1

### O → O

+1

### visits → O

+2

### O → LOC

+2

### Delhi → LOC

+5
Therefore:

−1+1+2+2+5=9
So:

Score=9​

### 13. Sequence 4

Consider:

PER,O,O
That means:

Riya       → PER
visits     → O
Delhi      → O

Score:

Riya→PER=4
PER→O=2
visits→O=2
O→O=1
Delhi→O=−1
Therefore:

4+2+2+1−1=8
So:

Score=8​

### 14. Compare the sequences

We have:

Label sequence | Score | 
PER O LOC | 15 | 
O O LOC | 9 | 
PER O O | 8 | 
PER PER LOC | 6 | 

The highest score is:

PER,O,LOC​
Therefore CRF predicts:

Word | Label | 
Riya | PER | 
visits | O | 
Delhi | LOC | 

### 15. What exactly happened?

This is the central idea of CRF.

For Riya, the model considered:

Riya → PER
Riya → LOC
Riya → O

For visits, it considered:

visits → PER
visits → LOC
visits → O

For Delhi, it considered:

Delhi → PER
Delhi → LOC
Delhi → O

But it also considered transitions:

PER → PER
PER → LOC
PER → O

LOC → PER
LOC → LOC
LOC → O

O → PER
O → LOC
O → O

Thus the decision is based on both word information and label-to-label relationships.

### 16. Why this is better than independent classification

Suppose we use a simple classifier separately for each word.

It might say:

Riya    → PER
visits  → O
Delhi   → LOC

That happens to be correct here.

But consider a more difficult sentence:

New York is beautiful

A classifier looking at each word separately might produce:

New    → LOC
York   → O
is     → O
beautiful → O

But a sequence model can learn that:

LOC → LOC

is a possible pattern.

With BIO tagging, it can produce:

New    → B-LOC
York   → I-LOC
is     → O
beautiful → O

The relationship between neighboring labels helps produce a coherent sequence.

### 17. Where does the probability come in?

So far, we've used scores.

CRF converts these scores into probabilities.

The equation is:

P(Y∣X)=∑Y′​eScore(X,Y′)eScore(X,Y)​
Suppose, for simplicity, there were only four candidate sequences:

Sequence | Score | 
PER O LOC | 15 | 
O O LOC | 9 | 
PER O O | 8 | 
PER PER LOC | 6 | 

Then:

P(PER,O,LOC∣X)=e15+e9+e8+e6e15​
Because e15 is much larger than the others, the probability of the first sequence will be very high.

Using these four candidates only, it is approximately:

P(PER,O,LOC∣X)≈0.997
In the actual CRF, the denominator considers all valid label sequences, not just the four we selected for illustration.

### 18. How does CRF avoid checking all 27 sequences?

This is where dynamic programming / Viterbi algorithm becomes important.

We have:

33=27
sequences for only three words.

For a sentence of 10 words:

310=59,049
For 20 words:

320=3,486,784,401
Clearly, enumerating every possible sequence becomes inefficient.

Instead, CRF uses a Viterbi-style dynamic programming algorithm to find the highest-scoring sequence efficiently.

### 19. Viterbi-style simulation

Let's construct the dynamic-programming table.

Our labels are:

PER, LOC, O
For the first word Riya, the scores are:

Label | Score | 
PER | 4 | 
LOC | -2 | 
O | -1 | 

So:

Riya

PER = 4
LOC = -2
O   = -1

### Second word: visits

For every possible current label, CRF considers every possible previous label.

For example, for:

visits→O
we calculate:

### Previous PER

4+PER→O(2)+visits→O(2)
=8

### Previous LOC

−2+LOC→O(2)+visits→O(2)
=2

### Previous O

−1+O→O(1)+visits→O(2)
=2
The best is:

8
and it comes from:

PER→O
Therefore:

V2​(O)=8

For:

visits→PER
we calculate:

### Previous PER

4+(−1)+(−1)=2

### Previous LOC

−2+(−1)+(−1)=−4

### Previous O

−1+(+1)+(−1)=−1
Therefore:

V2​(PER)=2

For:

visits→LOC
we calculate:

### Previous PER

4+(−1)+(−1)=2

### Previous LOC

−2+(−1)+(−1)=−4

### Previous O

−1+(2)+(−1)=0
Therefore:

V2​(LOC)=2
So after processing visits:

Current label | Best score | 
PER | 2 | 
LOC | 2 | 
O | 8 | 

### 20. Third word: Delhi

Now we process:

Delhi

For:

Delhi→LOC
we consider the best previous score for each possible previous label.

### Previous PER

V2​(PER)+PER→LOC+Delhi→LOC
=2−1+5
=6

### Previous LOC

V2​(LOC)+LOC→LOC+Delhi→LOC
=2−1+5
=6

### Previous O

V2​(O)+O→LOC+Delhi→LOC
=8+2+5
=15
Therefore:

V3​(LOC)=15
And the best path is:

PER→O→LOC
Hence:

Riya       visits       Delhi
 ↓           ↓            ↓
PER          O           LOC

### 21. The complete Viterbi table

We can summarize the calculation:

Word | PER | LOC | O | 
Riya | 4 | -2 | -1 | 
visits | 2 | 2 | 8 | 
Delhi | — | 15 | — | 

The maximum final score is:

15​
and backtracking gives:

PER→O→LOC​

### 22. The complete CRF simulation

So the entire process is:

Input:

        Riya       visits       Delhi
          |           |            |
          v           v            v

     Extract features
          |
          v
  +-------------------+
  | Word-label scores |
  +-------------------+
          |
          +
  +-------------------+
  | Transition scores |
  +-------------------+
          |
          v
     Total sequence
        scores
          |
          v
  Viterbi / Dynamic
    Programming
          |
          v
    Highest scoring
       sequence
          |
          v

        PER         O          LOC
         |          |           |
       Riya       visits      Delhi

### Final prediction

Riya/PERvisits/ODelhi/LOC​

### The key point to remember

The most important difference between a normal classifier and a CRF is this:

Normal classifier:

xi​→yi​
It decides the label of each word separately.

CRF:

(X,Y)→Score
It evaluates the whole sequence, considering:

word features + contextual features + label transitions​
and finally selects:

Y∗=argYmax​Score(X,Y)​
So, conceptually, a CRF says:

"Don't just tell me what label fits this word. Tell me which complete sequence of labels fits this entire sentence best."

---

## 5. Teacher prompt

How the weights (+4 to -1 in the long list) are calculated for the word-level features?

---

## 6. ChatGPT response

Yes. This is actually one of the most important points in understanding CRF.

The values such as +4, +5, −1, −2 that I used in the previous simulation were not calculated manually from a formula for that example. They were illustrative weights that a trained CRF might learn.

In a real CRF, these weights are learned automatically from labeled training data.

Let's see exactly how.

### 1. Start with labeled training data

Suppose we want to build a CRF for Named Entity Recognition.

Our training data might be:

Sentence | Correct labels | 
Riya visits Delhi | PER O LOC | 
Rahul visits Kolkata | PER O LOC | 
Amit lives in Delhi | PER O LOC | 
Sita lives in Kolkata | PER O LOC | 
Delhi is beautiful | LOC O O | 
Kolkata is crowded | LOC O O | 

The CRF sees both:

the words

their correct labels

during training.

### 2. CRF creates feature functions

Suppose we create the following word-level features.

For simplicity, let's consider:

f1​(X,Y)={10​if word = Riya and label = PERotherwise​
Another:

f2​(X,Y)={10​if word = Riya and label = LOCotherwise​
Another:

f3​(X,Y)={10​if word = Delhi and label = LOCotherwise​
And so on.

The important point is:

The feature function is designed first; the weight is learned later.

### 3. Initially, weights are unknown

Suppose we have:

w1​,w2​,w3​,…
Initially, they might be:

w1​=0,w2​=0,w3​=0,…
The training algorithm then changes these weights.

For example, eventually it might learn:

w1​=3.8
w2​=−1.7
w3​=4.6
These are the kinds of numbers I simplified earlier to:

+4,−2,+5

### 4. But how does the CRF know what value to give a weight?

This is the interesting part.

Suppose the training data contains:

Riya → PER
Rahul → PER
Amit → PER
Sita → PER

So whenever these words appear, they are consistently labeled PERSON.

The model learns:

Words like these are strong evidence for PER.

Consequently, the corresponding word-label features get positive weights.

### 5. What about a negative weight?

Suppose:

Delhi → LOC

occurs frequently.

Then:

Delhi→LOC
gets a positive weight.

But if:

Delhi→PER
almost never occurs in the training data, the corresponding feature can receive a negative weight.

For example:

w(Delhi,LOC)=+4.5
while:

w(Delhi,PER)=−2.1
The negative weight means:

Assigning PER to Delhi is evidence against that particular label sequence.

### 6. A simple training example

Let's make the idea numerical.

Suppose our entire training set is:

Riya      PER
Rahul     PER
Amit      PER
Delhi     LOC
Kolkata   LOC
Mumbai    LOC

We want to learn whether the word should be PER or LOC.

Consider the feature:

f(X,Y)={10​if word=Delhi and label=LOCotherwise​
The training data tells us:

Delhi → LOC

So the feature fires whenever the correct labeling contains Delhi → LOC.

The training algorithm therefore tends to increase its weight.

For example:

wDelhi,LOC​:0→1.2→2.5→3.7→4.3
until the optimization procedure reaches a suitable value.

### 7. What is the optimization trying to achieve?

This is the fundamental idea.

Suppose we have:

Riya visits Delhi

Correct labels:

PER,O,LOC
The CRF wants:

P(PER,O,LOC∣X)
to be high.

So it adjusts weights to increase the probability of the correct sequence.

Mathematically, the CRF maximizes the conditional log-likelihood:

L(w)=n∑​logP(Y(n)∣X(n))
where:

X(n) = training sentence

Y(n) = correct label sequence

w = all feature weights

### 8. Substitute the CRF probability

We know:

P(Y∣X)=Z(X)eScore(X,Y)​
Therefore:

logP(Y∣X)=Score(X,Y)−logZ(X)
So the training algorithm tries to maximize:

L(w)=n∑​[Score(X(n),Y(n))−logZ(X(n))]
This is how the weights are mathematically learned.

### 9. The beautiful intuition behind the learning

There are two competing forces.

### Force 1 — Reward the correct sequence

If the training sentence is:

Riya visits Delhi

with:

PER,O,LOC
then features that occur in the correct sequence should receive higher weights.

For example:

Riya→PER
Delhi→LOC
PER→O
O→LOC
Their weights tend to increase.

### Force 2 — Penalize incorrect alternatives

Suppose the model considers:

O,O,PER
or:

LOC,O,PER
If those sequences get too much probability, the training algorithm adjusts weights to reduce their probability.

Thus the weights are learned through a competition between:

correct sequences vs. incorrect sequences.

### 10. A very simplified weight update

To understand the mechanics, we can think of the update approximately as:

wk​←wk​+η[observed feature count−expected feature count]
where:

wk​ = weight of feature k

η = learning rate

observed feature count = how often the feature occurs in the correct labels

expected feature count = how often the model expects that feature across possible label sequences

This is a very important equation for understanding CRF training.

### 11. Let's use a tiny example

Suppose our training sentence is:

Riya visits Delhi

Correct:

PER,O,LOC
Consider the feature:

f1​="Riya"+PER
Suppose in the current model:

### Observed count

The correct sequence contains:

Riya+PER
so:

Observed=1
Suppose the model's current probabilities imply that across all possible label sequences, the expected occurrence of this feature is:

Expected=0.30
Then:

Observed−Expected=1−0.30=0.70
Suppose:

η=0.1
Then approximately:

Δw1​=0.1(0.70)=0.07
So:

w1new​=w1old​+0.07
If the old weight was:

w1​=1.5
then:

w1new​=1.57
The feature becomes slightly stronger.

### 12. What if the feature is associated with an incorrect label?

Suppose we consider:

f2​="Riya"+LOC
The correct training label is PER, not LOC.

The observed count is:

Observed=0
Suppose the model expects:

Expected=0.20
Then:

Observed−Expected=0−0.20=−0.20
With:

η=0.1
we get:

Δw2​=−0.02
Therefore:

w2​
decreases.

Repeated training can eventually make it substantially negative.

### 13. This explains the +4 and −2 values

Now we can understand my earlier example.

When I wrote:

Feature | Weight | 
Riya → PER | +4 | 
Riya → LOC | −2 | 
Delhi → LOC | +5 | 
Delhi → PER | −2 | 

you should interpret these as:

After training, suppose the optimization process has learned these weights.

They weren't obtained by simply counting the words.

For example:

Riya→PER=+4
means the learned model considers this feature strong evidence for PER.

And:

Riya→LOC=−2
means it considers that feature evidence against LOC.

### 14. Important: frequency alone does NOT determine the weight

This is a subtle but important point.

You might think:

"If Riya appears 100 times as PER, its weight should be 100."

No.

CRF weights are not simply word frequencies.

The weight results from an optimization process involving the whole conditional probability model.

The weight depends on things such as:

frequency in the training data

competing labels

other features

neighboring labels

correlations between features

regularization

the optimization algorithm

Therefore, you cannot generally say:

Weight=Frequency

### 15. What happens in a real CRF?

A practical CRF might have thousands or millions of features.

For example:

word=Riya, label=PER
word=Riya, label=LOC
word=Delhi, label=LOC
word=Delhi, label=PER

word starts with capital, label=PER
word starts with capital, label=LOC

previous word=in, current label=LOC

previous label=PER, current label=O
previous label=O, current label=LOC

word suffix=ing, label=VERB
word suffix=tion, label=NOUN
...

The training algorithm learns a weight for each.

So we eventually have something like:

w1​=3.72
w2​=−1.83
w3​=2.94
w4​=0.61
etc.

### 16. Regularization is also important

In real CRF training, we normally don't allow the weights to grow without constraint.

A regularization term is added to the objective.

For example, with L2​ regularization:

L(w)=n∑​logP(Y(n)∣X(n))−2λ​k∑​wk2​
The second term discourages excessively large weights.

This helps prevent overfitting.

### 17. So who actually calculates the weights?

The answer is:

The CRF training algorithm calculates the weights through numerical optimization.

Common optimization methods include:

L-BFGS

Gradient-based optimization

Stochastic optimization variants

Other numerical optimization methods

The model repeatedly evaluates how changing each weight affects the training objective and adjusts the weights accordingly.

### 18. Complete picture

You can think of CRF training like this:

Training sentences
       +
Correct labels
       |
       v
Feature extraction
       |
       v
Initial weights
       |
       v
Calculate probabilities
       |
       v
Compare:
Observed features
       vs.
Expected features
       |
       v
Calculate gradient
       |
       v
Update weights
       |
       v
Repeat many times
       |
       v
Final learned weights

Then, during testing:

New sentence
     |
     v
Extract features
     |
     v
Use learned weights
     |
     v
Calculate sequence scores
     |
     v
Viterbi algorithm
     |
     v
Best label sequence

### Therefore, remember this distinction:

Feature function=Feature weight​
The feature function says what pattern to look for.

The weight says how strongly that pattern influences the prediction.

And the weight is learned from the labeled training data through optimization, not manually assigned as +4 or −2.

---

## 7. Teacher prompt

Can you provide a suitable example of simulation with a  proper training dataset?

---

## 8. ChatGPT response

Yes. Let's make the simulation more realistic, but still small enough to calculate by hand.

We'll use Named Entity Recognition (NER) and train a tiny CRF on several labeled sentences. Then we'll use the learned information to predict labels for a new sentence.

Important: To make the calculation transparent, I will use a simplified CRF training procedure rather than pretending that the small hand-calculated weights are what a production CRF library would exactly produce. A real CRF learns all feature weights jointly by optimizing the conditional log-likelihood.

### Example: Training a CRF for NER

Suppose we want to identify:

PER = Person

LOC = Location

O = Other

We will use a small training dataset.

### 1. Training dataset

Consider these six sentences:

No. | Sentence | Correct labels | 
1 | Riya lives in Kolkata | PER O O LOC | 
2 | Rahul lives in Delhi | PER O O LOC | 
3 | Amit visits Mumbai | PER O LOC | 
4 | Sita visits Delhi | PER O LOC | 
5 | Kolkata is beautiful | LOC O O | 
6 | Delhi is crowded | LOC O O | 

So the training data is:

Riya      PER
lives     O
in        O
Kolkata   LOC

Rahul     PER
lives     O
in        O
Delhi     LOC

Amit      PER
visits    O
Mumbai    LOC

Sita      PER
visits    O
Delhi     LOC

Kolkata   LOC
is        O
beautiful O

Delhi     LOC
is        O
crowded   O

### 2. What does the CRF learn?

There are two major types of information it can learn.

### A. Word → label relationship

For example:

Riya→PER
Kolkata→LOC
Delhi→LOC

### B. Label → label relationship

For example:

PER→O
O→LOC
The CRF learns weights for both.

### 3. First calculate some simple statistics

Before talking about weights, let's see what the training data tells us.

### Person words

The training data contains:

Riya    → PER
Rahul   → PER
Amit    → PER
Sita    → PER

So these words strongly indicate PER.

### Location words

Kolkata → LOC
Delhi   → LOC
Mumbai  → LOC

So these strongly indicate LOC.

This suggests that the corresponding word-label features should receive positive weights.

### 4. Define feature functions

Let's use a simplified set of features.

### Feature f1​

f1​(xi​,yi​)=1
when:

current word = Riya AND current label = PER

Otherwise:

f1​=0

### Feature f2​

f2​(xi​,yi​)=1
when:

current word = Delhi AND current label = LOC

### Feature f3​

f3​(xi​,yi​)=1
when:

current word = Kolkata AND current label = LOC

### Feature f4​

f4​(yi−1​,yi​)=1
when:

previous label = PER AND current label = O

### Feature f5​

f5​(yi−1​,yi​)=1
when:

previous label = O AND current label = LOC

### 5. Initial weights

Suppose initially:

w1​=w2​=w3​=w4​=w5​=0
So initially the CRF has no knowledge.

We now train it.

### 6. First training example

Take:

Riya lives in Kolkata

Correct sequence:

Y=PER,O,O,LOC
The features occurring in the correct sequence include:

Riya→PER
PER→O
O→LOC
Kolkata→LOC
So these features should become stronger.

### 7. But why don't we simply increase every feature by 1?

Because CRF is probabilistic.

It asks:

"How much of this feature occurs in the correct sequence compared with how much I would expect it to occur across all possible sequences?"

This is why the gradient contains the idea:

Observed−Expected

### 8. Simplified first update

Suppose, for illustration, that the current model calculates the following expected counts.

Feature | Observed | Expected | 
Riya → PER | 1 | 0.33 | 
Delhi → LOC | 0 | 0.33 | 
Kolkata → LOC | 1 | 0.33 | 
PER → O | 1 | 0.50 | 
O → LOC | 1 | 0.33 | 

Then:

Gradient=Observed−Expected
For Riya→PER:

1−0.33=0.67
For Delhi→LOC:

0−0.33=−0.33
For Kolkata→LOC:

1−0.33=0.67
For PER→O:

1−0.50=0.50
For O→LOC:

1−0.33=0.67

### 9. Update the weights

Suppose learning rate:

η=0.1
The simplified update is:

wknew​=wkold​+η(Observed−Expected)
Therefore:

### Riya → PER

w1​=0+0.1(0.67)
w1​=0.067

### Delhi → LOC

w2​=0+0.1(−0.33)
w2​=−0.033

### Kolkata → LOC

w3​=0+0.1(0.67)
w3​=0.067
And similarly:

w4​=0.05
w5​=0.067
This is just one training update.

### 10. Now train on the second sentence

The second sentence is:

Rahul lives in Delhi

with:

PER,O,O,LOC
This sentence provides evidence for:

Rahul→PER
and:

Delhi→LOC
and:

PER→O
and:

O→LOC
The corresponding weights become stronger.

### 11. After many training examples

After repeatedly processing the training dataset, the model might learn weights resembling:

Feature | Example learned weight | 
Riya → PER | +2.8 | 
Rahul → PER | +2.7 | 
Amit → PER | +2.6 | 
Sita → PER | +2.7 | 
Delhi → LOC | +3.4 | 
Kolkata → LOC | +3.2 | 
Mumbai → LOC | +3.1 | 
PER → O | +1.8 | 
O → LOC | +2.1 | 
O → O | +0.9 | 

These numbers are illustrative, not the exact result of training this dataset with a particular CRF implementation.

Now the model has learned something useful:

Riya/Rahul/Amit/Sita  → likely PER
Delhi/Kolkata/Mumbai  → likely LOC
PER → O               → likely transition
O → LOC               → likely transition

### 12. Now give the CRF a new sentence

Suppose the test sentence is:

Sohan lives in Kolkata

We have never seen Sohan during training.

This is interesting because the model cannot simply rely on:

Sohan→PER
since it hasn't learned that exact word.

But we can add another type of feature.

### 13. Add a capitalization feature

Define:

f6​(xi​,yi​)
as:

Current word starts with a capital letter AND label = PER.

Suppose training shows that names are generally capitalized.

The model may learn:

w6​=1.5
Similarly, we can have:

Capitalized word + LOC

with a smaller weight.

This allows CRF to generalize to previously unseen words.

### 14. Evaluate Sohan

For:

Sohan→PER
suppose:

Capitalized + PER = +1.5

PER is compatible with following O = +1.8

For:

Sohan→LOC
suppose:

Capitalized + LOC = +0.5

Therefore, PER is preferred.

### 15. Evaluate the entire sequence

Candidate:

PER,O,O,LOC
Let's assign the following illustrative scores:

### Sohan → PER

+1.5

### PER → O

+1.8

### lives → O

+0.9

### O → O

+0.9

### O → LOC

+2.1

### Kolkata → LOC

+3.2
Total:

1.5+1.8+0.9+0.9+2.1+3.2
=10.4​

### 16. Compare with another sequence

Consider:

O,O,O,LOC
Suppose:

Sohan → O       = -0.5
O → O           = +0.9
lives → O       = +0.9
O → O           = +0.9
O → LOC         = +2.1
Kolkata → LOC   = +3.2

Total:

−0.5+0.9+0.9+0.9+2.1+3.2
=7.5​
So:

10.4>7.5
Therefore:

PER,O,O,LOC
wins.

### 17. Final prediction

The CRF predicts:

Word | Label | 
Sohan | PER | 
lives | O | 
in | O | 
Kolkata | LOC | 

So:

Sohan/PERlives/Oin/OKolkata/LOC​

### 18. Now let's understand where the weights came from

This brings us back to your original question.

The weights such as:

+3.4,+2.1,−0.5
are not manually assigned.

They emerge from the optimization process.

Conceptually:

             Training dataset
                    ↓
             Feature extraction
                    ↓
          Initialize weights
                    ↓
        Calculate P(Y | X)
                    ↓
       Calculate log-likelihood
                    ↓
              Calculate
               gradient
                    ↓
             Update weights
                    ↓
              Repeat
                    ↓
          Learned CRF weights

### 19. What happens if a feature is frequently correct?

Suppose:

Delhi → LOC

appears many times as a correct relationship.

Then the model repeatedly gets:

Observed>Expected
for that feature.

Therefore:

Δw>0
and its weight increases.

Eventually:

Delhi→LOC
might have a strong positive weight.

### 20. What happens if a feature is associated with an incorrect label?

Suppose:

Delhi → PER

never occurs in the correct training labels.

But the model initially assigns some probability to it.

Then:

Observed=0
while:

Expected>0
Therefore:

Observed−Expected<0
and:

Δw<0
So the weight becomes negative.

This is where negative weights come from.

### 21. Why the transition weights matter

Now consider:

Riya lives in Kolkata

Suppose the word-level features alone suggest:

Riya → PER
lives → O
in → O
Kolkata → LOC

But CRF also knows that:

PER→O
and:

O→LOC
are common transitions.

Therefore the entire sequence receives a high score.

The CRF is essentially combining:

word evidence+contextual evidence+transition evidence​

### 22. One important correction to the earlier simplified example

When I previously showed:

Riya → PER = +4

Delhi → LOC = +5

etc.

those numbers were chosen for demonstration so that we could manually simulate the scoring.

In a proper CRF:

You don’t decide +4 or −2 yourself.​
You provide:

Training sentences

Correct label sequences

Feature definitions

Then the CRF training algorithm learns the weights.

### 23. A more realistic CRF feature set

A real NER CRF would usually have far more features than our tiny example.

For the word Kolkata, features could include:

Feature | Possible value | 
Current word | Kolkata | 
Previous word | in | 
Next word | is | 
Capitalized | Yes | 
All uppercase | No | 
Contains digit | No | 
Prefix-1 | K | 
Prefix-2 | Ko | 
Prefix-3 | Kol | 
Suffix-1 | a | 
Suffix-2 | ta | 
Suffix-3 | ata | 
Previous label | O | 
Current label | LOC | 

The CRF learns a weight for each relevant feature.

So a real model might have thousands of weights.

### 24. The complete conceptual simulation

TRAINING DATA

Riya lives in Kolkata
PER  O     O  LOC

Rahul lives in Delhi
PER   O     O  LOC

Amit visits Mumbai
PER  O      LOC

Sita visits Delhi
PER  O      LOC

Kolkata is beautiful
LOC     O  O

Delhi is crowded
LOC   O  O
             │
             ▼
       Feature Extraction
             │
             ▼
      Feature functions
             │
             ▼
     Initial weights = 0
             │
             ▼
       CRF optimization
             │
       ┌─────┴─────┐
       │           │
   Observed     Expected
   features     features
       │           │
       └─────┬─────┘
             ▼
        Gradient
             │
             ▼
       Weight update
             │
             ▼
          Repeat
             │
             ▼
      LEARNED WEIGHTS
             │
             ▼
     New sentence:
     
     Sohan lives in Kolkata
             │
             ▼
       Feature extraction
             │
             ▼
      Sequence scoring
             │
             ▼
      Viterbi algorithm
             │
             ▼
     Sohan/PER
     lives/O
     in/O
     Kolkata/LOC

### The central idea

A CRF does not learn:

"Riya has weight +4."

Rather, it learns:

"The feature current word = Riya AND current label = PER is useful for explaining the correct label sequences, so optimization gives that feature a positive weight."

And the same principle applies to every word feature, contextual feature, and transition feature.

---

## 9. Teacher prompt

What does the BIO level indicate ?

---

## 10. ChatGPT response

In Named Entity Recognition (NER), BIO is a labeling scheme used to indicate the position of a word within an entity.

BIO stands for:

B = Beginning (Beginning of an entity)

I = Inside (Inside an entity)

O = Outside (Not part of any entity)

### Example

Consider:

Riya lives in New York

Suppose we want to identify Riya as a person and New York as a location.

The BIO labels would be:

Word | BIO Label | Meaning | 
Riya | B-PER | Beginning of a Person entity | 
lives | O | Outside any entity | 
in | O | Outside any entity | 
New | B-LOC | Beginning of a Location entity | 
York | I-LOC | Inside the same Location entity | 

So:

Riya/B-PER lives/O in/O New/B-LOC York/I-LOC​

### Why do we need B and I?

Consider the entity:

New York

If we simply labeled both words LOC, we would have:

New    LOC
York   LOC

But BIO tells us that New starts the entity and York continues it:

New    B-LOC
York   I-LOC

This becomes especially important when there are multiple consecutive entities:

Riya visited New York University

Possible labels:

Word | Label | 
Riya | B-PER | 
visited | O | 
New | B-ORG | 
York | I-ORG | 
University | I-ORG | 

Here, BIO tells us that New York University is one entity, rather than three separate entities.

### Connection with CRF

This is particularly relevant to our CRF discussion.

Instead of having only:

PER, LOC, O
the CRF can have labels such as:

B−PER, I−PER, B−LOC, I−LOC, B−ORG, I−ORG, O
The CRF then learns not only word-label relationships but also transition relationships, for example:

B−LOC→I−LOC
is a very reasonable transition, while:

B−LOC→I−PER
would generally be undesirable.

So BIO gives the CRF a way to represent the structure and boundaries of multi-word named entities.

---

## 11. Teacher prompt

Explain the functionality of CRF with a suitable example by simulation.

---

## 12. ChatGPT response

Yes. Let us do a proper end-to-end CRF simulation, starting from a small labeled training dataset, defining features, showing how the model learns their weights, and finally using those weights to predict a new sentence.

I will keep the mathematics manageable so that the mechanism is visible rather than hiding it behind software.

### 1. Problem: Named Entity Recognition

Suppose our task is to identify Person (PER) and Location (LOC) in sentences.

We will use BIO labels:

B-PER = beginning of a person name

I-PER = continuation of a person name

B-LOC = beginning of a location

I-LOC = continuation of a location

O = outside an entity

For simplicity, our training examples will mostly contain single-word names.

### 2. Training dataset

Suppose we have the following labeled training data:

Sentence | Correct BIO labels | 
Riya lives in Kolkata | B-PER O O B-LOC | 
Rahul lives in Delhi | B-PER O O B-LOC | 
Amit visits Mumbai | B-PER O B-LOC | 
Sita visits Delhi | B-PER O B-LOC | 
Kolkata is beautiful | B-LOC O O | 
Delhi is crowded | B-LOC O O | 

So the CRF sees:

Riya      → B-PER
lives     → O
in        → O
Kolkata   → B-LOC

and so on.

The important thing is that the correct labels are supplied during training.

### 3. What does the CRF learn?

The CRF tries to learn two things.

### Word/observation features

For example:

Riya→B-PER
Delhi→B-LOC

### Transition features

For example:

B-PER→O
O→B-LOC
Thus the CRF learns both:

What does this word look like?
        +
What labels normally occur next?

### 4. Define some feature functions

Let's deliberately use only a few features.

### Feature f1​

Current word is "Riya" and current label is B-PER.

f1​(xi​,yi​)={10​xi​=Riya, yi​=B-PERotherwise​

### Feature f2​

Current word is "Delhi" and current label is B-LOC.

### Feature f3​

Current word is "Kolkata" and current label is B-LOC.

### Feature f4​

Previous label is B-PER and current label is O.

### Feature f5​

Previous label is O and current label is B-LOC.

These are only a tiny subset of what a real CRF might use.

### 5. Initially, the weights are unknown

Suppose:

w1​=w2​=w3​=w4​=w5​=0
So initially:

Riya → B-PER       weight = 0
Delhi → B-LOC      weight = 0
Kolkata → B-LOC    weight = 0
B-PER → O          weight = 0
O → B-LOC          weight = 0

The model knows nothing.

### 6. First training sentence

Consider:

Riya lives in Kolkata

Correct labels:

B-PER,O,O,B-LOC
So the correct path is:

Riya       lives       in       Kolkata
  ↓          ↓          ↓          ↓
B-PER        O          O       B-LOC

The corresponding features fire:

f1​=1
because:

Riya→B-PER
Also:

f4​=1
because:

B-PER→O
and:

f5​=1
because:

O→B-LOC
and:

f3​=1
because:

Kolkata→B-LOC

### 7. How does the model know whether to increase a weight?

This is where CRF training becomes interesting.

The model compares:

Observed feature count
with:

Expected feature count
A simplified gradient is:

∂wk​∂L​=Observedk​−Expectedk​
If:

Observed>Expected
the weight increases.

If:

Observed<Expected
the weight decreases.

### 8. Numerical example of the first update

Suppose, for illustration, that the current model calculates:

Feature | Observed | Expected | 
Riya → B-PER | 1 | 0.25 | 
Kolkata → B-LOC | 1 | 0.30 | 
B-PER → O | 1 | 0.40 | 
O → B-LOC | 1 | 0.30 | 

Therefore:

### Riya → B-PER

1−0.25=0.75

### Kolkata → B-LOC

1−0.30=0.70

### B-PER → O

1−0.40=0.60

### O → B-LOC

1−0.30=0.70
All are positive.

So these weights should increase.

### 9. Weight update

Suppose the learning rate is:

η=0.1
Then:

wknew​=wkold​+η(Observed−Expected)
Therefore:

### Riya → B-PER

w1​=0+0.1(0.75)
w1​=0.075​

### Kolkata → B-LOC

w3​=0+0.1(0.70)
w3​=0.070​

### B-PER → O

w4​=0+0.1(0.60)
w4​=0.060​

### O → B-LOC

w5​=0+0.1(0.70)
w5​=0.070​
This is one small training update.

### 10. Second training sentence

Now:

Rahul lives in Delhi

Correct:

B-PER,O,O,B-LOC
The model sees more evidence that:

Person → B-PER
B-PER → O
O → B-LOC
Delhi → B-LOC

should be favored.

After many iterations over all six training sentences, the weights will move substantially away from zero.

For example, after optimization we might obtain an illustrative set like:

Feature | Learned weight | 
Riya → B-PER | +2.4 | 
Rahul → B-PER | +2.5 | 
Amit → B-PER | +2.3 | 
Sita → B-PER | +2.4 | 
Delhi → B-LOC | +3.0 | 
Kolkata → B-LOC | +2.9 | 
Mumbai → B-LOC | +2.8 | 
B-PER → O | +1.6 | 
O → B-LOC | +1.9 | 
O → O | +0.7 | 

Again, these final numbers are illustrative. A real CRF's exact weights depend on the feature representation, regularization, optimizer, and training procedure.

### 11. Now let's test the trained CRF

Give it a sentence it has not seen during training:

Sohan lives in Kolkata

We want:

Sohan      lives      in      Kolkata
   ↓         ↓         ↓         ↓
 ?           ?         ?       ?

Possible labels are:

B-PER,B-LOC,O

### 12. What does the CRF know about "Sohan"?

The word Sohan wasn't in our training data.

Therefore we don't have a feature such as:

Sohan→B-PER
But we could have a general feature:

Word starts with a capital letter + B-PER

Suppose training has taught this feature a positive weight:

wcapital+PER​=1.5
Then:

Sohan→B-PER
gets:

+1.5
because Sohan begins with a capital letter.

This illustrates why CRFs can use general contextual and orthographic features, rather than relying only on exact words.

### 13. Calculate one candidate sequence

Consider:

Y1​=B-PER,O,O,B-LOC
That gives:

Sohan      lives       in       Kolkata
  ↓          ↓          ↓          ↓
B-PER        O           O       B-LOC

Suppose the relevant learned weights are:

Feature | Weight | 
Capitalized → B-PER | +1.5 | 
B-PER → O | +1.6 | 
lives → O | +0.8 | 
O → O | +0.7 | 
O → B-LOC | +1.9 | 
Kolkata → B-LOC | +2.9 | 

Therefore:

Score(Y1​)=1.5+1.6+0.8+0.7+1.9+2.9
Score(Y1​)=9.4​

### 14. Consider another candidate

Suppose the model considers:

Y2​=O,O,O,B-LOC
That means:

Sohan      lives       in       Kolkata
  ↓          ↓          ↓          ↓
 O           O          O       B-LOC

Suppose:

Sohan→O=−0.5
Then:

O→O=0.7
lives→O=0.8
O→O=0.7
O→B-LOC=1.9
Kolkata→B-LOC=2.9
Therefore:

Score(Y2​)=−0.5+0.7+0.8+0.7+1.9+2.9
Score(Y2​)=6.5​
Since:

9.4>6.5
the first sequence is preferred.

### 15. Consider a clearly bad sequence

Suppose:

Y3​=B-LOC,O,O,B-PER
That means:

Sohan      lives       in       Kolkata
  ↓          ↓          ↓          ↓
B-LOC        O          O       B-PER

Suppose the corresponding features give:

Sohan→B-LOC=0.4
B-LOC→O=0.3
lives→O=0.8
O→O=0.7
O→B-PER=0.2
Kolkata→B-PER=−1.5
Then:

Score(Y3​)=0.4+0.3+0.8+0.7+0.2−1.5
Score(Y3​)=0.9​
So this sequence is strongly disfavored.

### 16. Compare the candidates

Candidate sequence | Score | 
B-PER O O B-LOC | 9.4 | 
O O O B-LOC | 6.5 | 
B-LOC O O B-PER | 0.9 | 

Therefore:

B-PER,O,O,B-LOC​
is selected.

Final output:

Word | Prediction | 
Sohan | B-PER | 
lives | O | 
in | O | 
Kolkata | B-LOC | 

### 17. Where does Viterbi enter?

So far I showed the comparison of complete sequences.

But imagine we have:

10
possible labels and a sentence of 50 words.

We cannot enumerate:

1050
possible sequences.

Instead, a linear-chain CRF uses dynamic programming, commonly the Viterbi algorithm, to find the highest-scoring sequence efficiently.

Conceptually:

Sohan       lives       in       Kolkata
  |           |          |           |
  v           v          v           v

B-PER       B-PER      B-PER       B-PER
B-LOC       B-LOC      B-LOC       B-LOC
O           O          O           O
  |           |          |           |
  +-----------+----------+-----------+
              |
              v
       Highest-scoring
          path
              |
              v
 B-PER → O → O → B-LOC

At each position, Viterbi keeps the best score reaching each possible label, rather than keeping every possible partial sequence.

### 18. The mathematical scoring function

Everything we have just simulated can be represented by:

Score(X,Y)=i=1∑n​k∑​wk​fk​(yi−1​,yi​,X,i)
where:

X = observed sentence

Y = proposed label sequence

fk​ = feature function

wk​ = learned weight

The CRF then defines:

P(Y∣X)=∑Y′​eScore(X,Y′)eScore(X,Y)​
And prediction is:

Y∗=argYmax​Score(X,Y)​

### 19. What exactly did the training accomplish?

Before training:

Riya → B-PER          weight ≈ 0
Delhi → B-LOC         weight ≈ 0
B-PER → O             weight ≈ 0
O → B-LOC             weight ≈ 0

After training:

Riya → B-PER          strongly positive
Delhi → B-LOC         strongly positive
B-PER → O             positive
O → B-LOC             positive

So the CRF has effectively learned:

"These word-label combinations and label transitions are useful for producing the correct sequences."

### 20. The most important point

Notice that CRF did not simply learn:

P(B-PER∣Riya)
and:

P(B-LOC∣Kolkata)
independently.

It learned a joint scoring function for the sequence.

For example:

Sohan → B-PER
          +
B-PER → O
          +
lives → O
          +
O → O
          +
O → B-LOC
          +
Kolkata → B-LOC

All these contributions are added:

9.4​
and compared against alternative complete paths.

That is the essence of CRF.

### CRF simulation in one diagram

             TRAINING DATA
                    │
                    ▼
       ┌────────────────────────┐
       │ Sentences + BIO labels │
       └────────────┬───────────┘
                    │
                    ▼
            Feature functions
                    │
                    ▼
             Initial weights
               (mostly 0)
                    │
                    ▼
       ┌────────────────────────┐
       │ CRF training           │
       │                        │
       │ Observed − Expected    │
       │ feature counts        │
       └────────────┬───────────┘
                    │
                    ▼
             Weight updates
                    │
                    ▼
              Repeat many
               iterations
                    │
                    ▼
          ┌──────────────────┐
          │ TRAINED CRF      │
          │                  │
          │ learned weights  │
          └────────┬─────────┘
                   │
                   ▼
          New sentence
       "Sohan lives in Kolkata"
                   │
                   ▼
            Extract features
                   │
                   ▼
          Calculate transition
             + word scores
                   │
                   ▼
             Viterbi algorithm
                   │
                   ▼
       B-PER → O → O → B-LOC
                   │
                   ▼
          Final NER output

### In short

Training:

Training data→learn feature weights​
Prediction:

New sentence→score possible label paths→best path​
And this is why CRF is particularly powerful for sequence labeling: it considers what each word suggests AND how neighboring labels fit together.

---

## 13. Teacher prompt

Show how dynamic programming works for the functionality of CRF.

---

## 14. ChatGPT response

Absolutely. The dynamic programming part is the heart of efficient inference in a linear-chain CRF. Let's simulate it numerically using the same NER idea, but with a very small sentence so that every calculation can be seen.

We will use:

Riya visits Delhi

and labels:

PER = Person

LOC = Location

O = Outside an entity

### 1. What are we trying to find?

Input:

X=(Riya, visits, Delhi)
We want the best label sequence:

Y∗=(y1​,y2​,y3​)
Each yi​ can be:

{PER,LOC,O}
Therefore, theoretically there are:

33=27
possible sequences.

For example:

PER PER PER
PER PER LOC
PER PER O
PER LOC PER
PER LOC LOC
...
O O O

We could calculate the score of all 27 sequences.

But for a long sentence this becomes enormous.

Dynamic programming avoids calculating every complete sequence separately.

### 2. The CRF score

For a simplified linear-chain CRF, let's write:

Score(X,Y)=i∑​Ei​(yi​)+i∑​T(yi−1​,yi​)
where:

Ei​(yi​) = emission/word score

T(yi−1​,yi​) = transition score

So:

Total score=word scores+transition scores​

### 3. Give the model some learned scores

Suppose training has already happened and the CRF has learned the following word-level scores.

### For Riya

Label | Score | 
PER | 4 | 
LOC | -2 | 
O | -1 | 

So:

E1​(PER)=4
E1​(LOC)=−2
E1​(O)=−1

### For visits

Label | Score | 
PER | -1 | 
LOC | -1 | 
O | 2 | 

### For Delhi

Label | Score | 
PER | -2 | 
LOC | 5 | 
O | -1 | 

These are illustrative learned scores.

### 4. Transition scores

Suppose the CRF has learned:

Previous → Current | Score | 
PER → PER | -1 | 
PER → LOC | -1 | 
PER → O | +2 | 
LOC → PER | -1 | 
LOC → LOC | -1 | 
LOC → O | +2 | 
O → PER | +1 | 
O → LOC | +2 | 
O → O | +1 | 

For example:

T(PER,O)=2
means:

A PER followed by O is favorable.

And:

T(O,LOC)=2
means:

O followed by LOC is favorable.

### 5. Start with the first word

The first word is:

Riya

There is no previous label yet.

So we simply assign its word scores:

Label | Score | 
PER | 4 | 
LOC | -2 | 
O | -1 | 

We can represent this as:

             Riya
               |
       +-------+-------+
       |       |       |
      PER     LOC      O
       |       |       |
       4      -2      -1

This is our first DP column.

### 6. Move to the second word

The second word is:

visits

Now something interesting happens.

To calculate the best score for visits → PER, we don't just look at visits.

We consider all possible previous labels:

Riya → PER → visits/PER
Riya → LOC → visits/PER
Riya → O   → visits/PER

Then select the best one.

The recurrence is:

Vi​(y)=Ei​(y)+y′max​[Vi−1​(y′)+T(y′,y)]
This is the key dynamic-programming equation.

### 7. Calculate visits → PER

We want:

V2​(PER)
There are three possible previous labels.

### Case 1: Riya = PER

4+T(PER,PER)
=4+(−1)
=3

### Case 2: Riya = LOC

−2+T(LOC,PER)
=−2−1
=−3

### Case 3: Riya = O

−1+T(O,PER)
=−1+1
=0
The maximum is:

max(3,−3,0)=3
Now add the word score for visits → PER:

E2​(PER)=−1
Therefore:

V2​(PER)=3−1
V2​(PER)=2​
And we remember that the best previous label was:

PER​
So:

Riya/PER → visits/PER

is the best path ending in PER.

### 8. Calculate visits → LOC

Now:

V2​(LOC)
Again, consider all three possibilities.

### Previous PER

4+T(PER,LOC)
=4−1=3

### Previous LOC

−2+T(LOC,LOC)
=−2−1=−3

### Previous O

−1+T(O,LOC)
=−1+2=1
Maximum:

max(3,−3,1)=3
Add:

E2​(LOC)=−1
Therefore:

V2​(LOC)=3−1
V2​(LOC)=2​
Best previous label:

PER
So:

Riya/PER → visits/LOC

is the best path ending in LOC.

### 9. Calculate visits → O

Now:

V2​(O)

### Previous PER

4+T(PER,O)
=4+2=6

### Previous LOC

−2+T(LOC,O)
=−2+2=0

### Previous O

−1+T(O,O)
=−1+1=0
Maximum:

max(6,0,0)=6
Add:

E2​(O)=2
Therefore:

V2​(O)=6+2
V2​(O)=8​
Best previous label:

PER
So the best path ending in O is:

Riya/PER → visits/O

### 10. Our DP table so far

After processing two words:

Word | PER | LOC | O | 
Riya | 4 | -2 | -1 | 
visits | 2 | 2 | 8 | 

But notice something important.

The 8 at visits/O doesn't represent just the word visits.

It represents the best complete path from the beginning up to visits ending with O:

Riya/PER→visits/O​
This is the essence of dynamic programming.

### 11. Now process Delhi

Third word:

Delhi

Again, calculate each possible final label.

### Delhi → PER

We want:

V3​(PER)
Possible previous labels:

### Previous PER

V2​(PER)+T(PER,PER)
=2−1=1

### Previous LOC

V2​(LOC)+T(LOC,PER)
=2−1=1

### Previous O

V2​(O)+T(O,PER)
=8+1=9
Maximum:

max(1,1,9)=9
Now add Delhi → PER score:

E3​(PER)=−2
Therefore:

V3​(PER)=9−2
V3​(PER)=7​
Best previous label:

O
So the best path ending in PER is:

Riya/PER → visits/O → Delhi/PER

### 12. Delhi → LOC

Now calculate:

V3​(LOC)

### Previous PER

V2​(PER)+T(PER,LOC)
=2−1=1

### Previous LOC

V2​(LOC)+T(LOC,LOC)
=2−1=1

### Previous O

V2​(O)+T(O,LOC)
=8+2=10
Maximum:

max(1,1,10)=10
Now add:

E3​(LOC)=5
Therefore:

V3​(LOC)=10+5
V3​(LOC)=15​
The best previous label is:

O
Therefore the best path ending in LOC is:

Riya/PER → visits/O → Delhi/LOC

### 13. Delhi → O

Finally:

V3​(O)

### Previous PER

2+T(PER,O)
=2+2=4

### Previous LOC

2+T(LOC,O)
=2+2=4

### Previous O

8+T(O,O)
=8+1=9
Maximum:

max(4,4,9)=9
Add:

E3​(O)=−1
Therefore:

V3​(O)=9−1
V3​(O)=8​
Best previous label:

O

### 14. Final DP table

We now have:

Position | Word | PER | LOC | O | 
1 | Riya | 4 | -2 | -1 | 
2 | visits | 2 | 2 | 8 | 
3 | Delhi | 7 | 15 | 8 | 

The largest value in the final row is:

15​
and it corresponds to:

Delhi→LOC
Therefore, the final label is:

LOC​

### 15. Now we backtrack

This is the second important part of Viterbi.

We know:

Delhi=LOC
When we calculated Delhi → LOC, we found that its best previous label was:

O
Therefore:

visits=O
When we calculated visits → O, we found that its best previous label was:

PER
Therefore:

Riya=PER
So we backtrack:

Delhi/LOC
    ↑
visits/O
    ↑
Riya/PER

Reverse the direction:

Riya/PER→visits/O→Delhi/LOC​

### 16. Final answer

The CRF predicts:

Word | Label | 
Riya | PER | 
visits | O | 
Delhi | LOC | 

So:

Riya/PERvisits/ODelhi/LOC​

### 17. Why is this called dynamic programming?

Notice what we didn't do.

We did not separately calculate all 27 complete sequences.

Instead, after processing visits, we kept only the best score for each possible current label:

visits/PER = 2
visits/LOC = 2
visits/O   = 8

Suppose there were 100 words.

At every position, we would keep only:

best path ending in PER
best path ending in LOC
best path ending in O

rather than keeping every possible partial sequence.

That's the dynamic programming principle:

Store the best solution to smaller subproblems and reuse those solutions to construct the best solution to the larger problem.

### 18. The important recurrence

For a linear-chain CRF, the Viterbi recurrence is essentially:

Vi​(y)=Ei​(y)+y′max​[Vi−1​(y′)+T(y′,y)]​
In words:

Best score for reaching the current label = current word score + the best score from any previous label plus its transition score.

And we also store the corresponding previous label so that we can backtrack later.

### 19. Visualizing the DP lattice

The calculation can be visualized as:

                Riya          visits           Delhi

PER             4  ───────→   2  ─────────→    7
                 \             \                ↑
                  \             \               │
LOC              -2 ───────→   2  ─────────→   15  ← BEST
                  \             \               ↑
                   \             \              │
O                 -1 ───────→   8  ─────────→    8

But each arrow has a transition score, and at every stage we retain only the best incoming path for each state.

More explicitly:

                         Delhi
                    /      |      \
                  PER     LOC      O
                   ↑       ↑       ↑
                   |       |       |
                  7       15       8
                   ↑       ↑       ↑
                   |       |       |
                 visits
                /   |   \
              PER  LOC   O
               2    2    8
                ↑    ↑    ↑
                |    |    |
               Riya
              PER LOC O
               4  -2 -1

The winning route is:

Riya/PER
    ↓
visits/O
    ↓
Delhi/LOC

with total score:

15​

### 20. Why not just choose the highest label at each word?

This is a very important question.

For Riya:

PER=4
so choose PER.

For visits:

O=2
so choose O.

For Delhi:

LOC=5
so choose LOC.

In this example, that happens to give the correct result.

But in general, choosing the highest label independently can fail because the best global sequence depends on transition scores.

For example, suppose:

Word score:
A → PER = 5
A → O   = 4

B → LOC = 5
B → O   = 4

but suppose:

PER→LOC=−10
while:

O→LOC=+3
Then choosing the locally best labels:

PER→LOC
may be worse globally than:

O→LOC
because:

4+3+5=12
could beat:

5−10+5=0
This is exactly why the sequence-level optimization of CRF matters.

### 21. Complexity advantage

Suppose there are:

n words

K possible labels

Naively checking every possible sequence requires:

Kn
calculations.

For example:

K=3,n=20
gives:

320=3,486,784,401
possible sequences.

With dynamic programming, the Viterbi calculation requires approximately:

O(nK2)​
because for every word and every current label, we examine all possible previous labels.

For our example:

n=3,K=3
so the basic transition comparisons are only on the order of:

3×32=27
rather than enumerating all complete sequences separately.

For long sequences, this difference is enormous.

### 22. How this fits into the complete CRF

Now we can see the complete CRF pipeline:

             TRAINING
                │
                ▼
       Labeled sentences
                │
                ▼
        Feature functions
                │
                ▼
       Learn feature weights
                │
                ▼
        ┌───────────────┐
        │ Trained CRF   │
        └───────┬───────┘
                │
                ▼
             TESTING
                │
                ▼
          New sentence
                │
                ▼
       Calculate word scores
                +
       Calculate transition
             scores
                │
                ▼
       ┌──────────────────┐
       │ Dynamic          │
       │ Programming      │
       │ (Viterbi)        │
       └────────┬─────────┘
                │
                ▼
       Best score at final
              state
                │
                ▼
           Backtracking
                │
                ▼
       Best label sequence
                │
                ▼
       PER → O → LOC

### The key idea

The CRF tells us how to score a complete label sequence:

word evidence+transition evidence
The dynamic programming/Viterbi algorithm tells us how to efficiently find the highest-scoring sequence without examining every possible sequence.

So:

CRF+Viterbi⇒efficient sequence prediction​
And the most important DP idea is:

Keep only the best path reaching each label at each position.​

---

## 15. Teacher prompt

Can you provide another simulation example of CRF functionality other than named entity recognition (NER)?

---

## 16. ChatGPT response

Certainly. A good example other than NER is Part-of-Speech (POS) tagging, because it clearly demonstrates why a CRF considers the whole sequence and neighboring labels, rather than classifying each word independently.

Let's simulate a small CRF from training data through prediction and Viterbi decoding.

### CRF Simulation: POS Tagging

Suppose we want to assign a grammatical category to every word.

We'll use only three POS tags for simplicity:

N = Noun

V = Verb

D = Determiner

Our test sentence will be:

The dog runs

We want:

DNV

### 1. Training dataset

Suppose our labeled training data is:

Sentence | POS labels | 
The dog runs | D N V | 
The cat eats | D N V | 
A dog eats | D N V | 
The boy runs | D N V | 
A cat sleeps | D N V | 

Thus the CRF receives:

The dog runs
 D   N   V

The cat eats
 D   N   V

A dog eats
 D   N   V

The boy runs
 D   N   V

A cat sleeps
 D   N   V

From this, it can learn both word-level evidence and transition patterns.

### 2. What features can the CRF learn?

Let's define a few simple features.

### Word features

For example:

f1​(xi​,yi​)=1
if:

current word = "The" and current label = D.

Similarly:

f2​:

dog + N

f3​:

runs + V

### Transition features

For example:

f4​:

previous label = D and current label = N

and:

f5​:

previous label = N and current label = V

So the CRF learns things such as:

The → D
dog → N
runs → V

D → N
N → V

### 3. Suppose training produces these illustrative weights

After training, imagine the CRF has learned:

### Word-level weights

Word → Tag | Weight | 
The → D | +3 | 
The → N | -1 | 
The → V | -2 | 
dog → N | +3 | 
dog → D | -1 | 
dog → V | -2 | 
runs → V | +3 | 
runs → N | -1 | 
runs → D | -2 | 

### Transition weights

Transition | Weight | 
D → N | +2 | 
D → V | -1 | 
D → D | -2 | 
N → V | +2 | 
N → N | -1 | 
N → D | -1 | 
V → N | -1 | 
V → D | -1 | 
V → V | -2 | 

Again, these values are illustrative learned weights, not manually calculated weights.

### 4. Now test a new sentence

Let's give the trained CRF:

The dog runs

We already know the expected answer, but we'll let the CRF discover it.

There are three possible labels for every word:

D,N,V
So theoretically there are:

33=27
possible tag sequences.

For example:

D D D
D D N
D D V
D N D
D N V
...
V V V

We don't want to calculate all 27 separately.

We'll use dynamic programming/Viterbi.

### 5. Position 1: "The"

There is no previous tag.

Therefore:

V1​(D)=3
V1​(N)=−1
V1​(V)=−2
So:

             The
          /    |    \
         D     N     V
         3    -1    -2

The best first tag is:

D

### 6. Position 2: "dog"

Now we need to calculate the best score for every possible tag for dog.

The recurrence is:

Vi​(y)=Ei​(y)+y′max​[Vi−1​(y′)+T(y′,y)]
where:

Ei​(y) = word/tag score

T(y′,y) = transition score

### 6.1 dog → D

Possible previous tags:

### The = D

3+T(D,D)
=3−2=1

### The = N

−1+T(N,D)
=−1−1=−2

### The = V

−2+T(V,D)
=−2−1=−3
Best:

max(1,−2,−3)=1
Now add:

dog→D=−1
Therefore:

V2​(D)=1−1
V2​(D)=0​
Best previous tag:

D

### 7. dog → N

Again consider all previous tags.

### Previous D

3+T(D,N)=3+2=5

### Previous N

−1+T(N,N)=−1−1=−2

### Previous V

−2+T(V,N)=−2−1=−3
Best:

5
Now add:

dog→N=3
Therefore:

V2​(N)=5+3
V2​(N)=8​
Best previous tag:

D
So we have:

The/D→dog/N

### 8. dog → V

Possible previous tags:

### Previous D

3+T(D,V)=3−1=2

### Previous N

−1+T(N,V)=−1+2=1

### Previous V

−2+T(V,V)=−2−2=−4
Best:

2
Add:

dog→V=−2
Therefore:

V2​(V)=2−2
V2​(V)=0​

### 9. DP table after two words

Word | D | N | V | 
The | 3 | -1 | -2 | 
dog | 0 | 8 | 0 | 

The best score for dog is:

8​
and it corresponds to:

The/D→dog/N
This is already interesting.

The CRF didn't just decide:

dog = N

because "dog" is a noun.

It also considered:

D→N
which is a very strong transition.

### 10. Position 3: "runs"

Now calculate the best path ending in each possible tag.

### runs → D

### Previous D

V2​(D)+T(D,D)
=0−2=−2

### Previous N

V2​(N)+T(N,D)
=8−1=7

### Previous V

V2​(V)+T(V,D)
=0−1=−1
Maximum:

7
Add:

runs→D=−2
Therefore:

V3​(D)=7−2
V3​(D)=5​

### 11. runs → N

### Previous D

0+T(D,N)=0+2=2

### Previous N

8+T(N,N)=8−1=7

### Previous V

0+T(V,N)=0−1=−1
Maximum:

7
Add:

runs→N=−1
Therefore:

V3​(N)=7−1
V3​(N)=6​

### 12. runs → V

Now the important one.

### Previous D

0+T(D,V)=0−1=−1

### Previous N

8+T(N,V)=8+2=10

### Previous V

0+T(V,V)=0−2=−2
Maximum:

10
Add:

runs→V=3
Therefore:

V3​(V)=10+3
V3​(V)=13​
The best previous tag is:

N

### 13. Final DP table

We have:

Position | Word | D | N | V | 
1 | The | 3 | -1 | -2 | 
2 | dog | 0 | 8 | 0 | 
3 | runs | 5 | 6 | 13 | 

The highest final score is:

13​
corresponding to:

runs→V

### 14. Backtracking

We now trace backwards.

We know:

runs=V
When calculating runs → V, the best previous tag was:

N
Therefore:

dog=N
When calculating dog → N, the best previous tag was:

D
Therefore:

The=D
So the final path is:

The/D → dog/N → runs/V

or:

DNV​

### 15. What did dynamic programming actually do?

This is the most important part.

At dog, instead of remembering all possible paths, we remembered:

Best path ending in D = 0
Best path ending in N = 8
Best path ending in V = 0

Then at runs, we used those stored values.

So:

Best path to current state=best previous path+transition+current word score​
This is dynamic programming.

### 16. Why is this useful for CRF?

Suppose the sentence contained 100 words.

With three tags, the number of possible complete sequences would be:

3100
which is astronomically large.

But Viterbi only maintains three best scores at each position:

Position 1:
D, N, V

Position 2:
D, N, V

Position 3:
D, N, V

...

Position 100:
D, N, V

For each position, it considers transitions from the three previous tags.

Therefore the complexity is:

O(nK2)
where:

n = number of words

K = number of labels

For our example:

n=3,K=3
so:

O(3×32)=O(27)
rather than explicitly enumerating all possible sequences.

### 17. Why CRF is better than independently tagging each word

Suppose we used an independent classifier.

It might say:

The   → D
dog   → N
runs  → V

because each word individually suggests those tags.

But consider a more ambiguous sentence:

Time flies

The word flies can be:

a noun: "flies" are insects

a verb: "Time flies"

A word-only classifier might have difficulty.

A CRF can consider:

Time→N
and:

N→V
and therefore recognize:

Time/Nflies/V
The relationship between neighboring labels helps resolve ambiguity.

### 18. The fundamental difference

An independent classifier does approximately:

P(yi​∣xi​)​
It asks:

"What is the best tag for this word?"

CRF asks:

P(Y∣X)​
It asks:

"What is the best label sequence for the entire sentence?"

That's a fundamental difference.

### 19. Complete CRF functionality

The whole process can now be visualized as:

                 TRAINING DATA
                      │
                      ▼
             The dog runs → D N V
             The cat eats → D N V
             A dog eats   → D N V
             The boy runs → D N V
             A cat sleeps → D N V
                      │
                      ▼
               Feature extraction
                      │
                      ▼
             Learn feature weights
                      │
                      ▼
                TRAINED CRF
                      │
                      ▼
                New sentence
               "The dog runs"
                      │
                      ▼
             Calculate word scores
                      +
             Calculate transition
                   scores
                      │
                      ▼
              Dynamic programming
                 (Viterbi)
                      │
            ┌─────────┴─────────┐
            │                   │
         Best D              Best N/V
         paths               paths
            │                   │
            └─────────┬─────────┘
                      ▼
               Backtracking
                      │
                      ▼
               D → N → V
                      │
                      ▼
           The/D dog/N runs/V

### The key idea to remember

For CRF:

Feature weights→sequence scores→dynamic programming→best label sequence​
In this POS-tagging example, the CRF didn't merely learn that dog is a noun and runs is a verb. It also learned that the sequence

D→N→V​
is highly plausible. That combination of observation/word evidence + label-transition evidence is what makes CRF a powerful sequence-labeling model.

---

## 17. Teacher prompt

Can you provide any other example not related to NLP?

---

## 18. ChatGPT response

Yes. A very good non-NLP example for understanding CRF is human activity recognition from sensor data.

Imagine a smartwatch or smartphone records a person's movement every second. We want to identify the activity:

S = Standing

W = Walking

R = Running

This is naturally a sequence-labeling problem, just like POS tagging, but the observations are sensor measurements rather than words.

### CRF Example: Human Activity Recognition

Suppose an accelerometer gives us the following simplified measurements:

Time | Acceleration x | 
1 | 1.0 | 
2 | 1.1 | 
3 | 1.2 | 
4 | 3.0 | 
5 | 3.2 | 

We want the CRF to determine:

Y=(y1​,y2​,y3​,y4​,y5​)
where each yi​ is:

{S,W,R}

### 1. Training data

Suppose we previously collected labeled sensor data:

### Training sequence 1

Acceleration:  1.0   1.1   1.2   1.0
Activity:      S     S     S     S

### Training sequence 2

Acceleration:  1.5   1.6   1.7   1.8
Activity:      W     W     W     W

### Training sequence 3

Acceleration:  3.0   3.2   3.4   3.3
Activity:      R     R     R     R

From training, the CRF learns that:

Low acceleration  → Standing
Medium acceleration → Walking
High acceleration → Running

But it can also learn transitions:

S → S    likely
S → W    possible
W → W    likely
W → R    possible
R → R    likely

and perhaps:

S → R    unlikely
R → S    unlikely

### 2. Suppose the CRF has learned these scores

For simplicity, let's assume the learned observation scores are:

Acceleration | S | W | R | 
1.0 | 4 | 1 | -2 | 
1.1 | 4 | 1 | -2 | 
1.2 | 4 | 1 | -2 | 
3.0 | -2 | 1 | 4 | 
3.2 | -2 | 1 | 4 | 

For example:

E(1.0,S)=4
means:

An acceleration of 1.0 strongly supports Standing.

### 3. Transition scores

Suppose the CRF has learned:

Previous → Current | Score | 
S → S | +2 | 
S → W | +1 | 
S → R | -3 | 
W → S | 0 | 
W → W | +2 | 
W → R | +1 | 
R → S | -3 | 
R → W | 0 | 
R → R | +2 | 

These scores express the fact that activities usually continue smoothly.

For example:

T(S,S)=+2
because remaining standing is quite likely.

Whereas:

T(S,R)=−3
because suddenly changing from standing to running is less likely.

### 4. New sensor sequence

Now the device observes:

Time:           1    2    3    4    5
Acceleration:  1.0  1.1  1.2  3.0  3.2

We want to predict:

?     ?     ?     ?     ?

### 5. Position 1

At time 1:

x1​=1.0
Observation scores:

S=4,W=1,R=−2
Therefore:

State | Score | 
S | 4 | 
W | 1 | 
R | -2 | 

So initially:

V1​(S)=4
V1​(W)=1
V1​(R)=−2

### 6. Position 2

Now:

x2​=1.1
We calculate the best path ending in each state.

The Viterbi recurrence is:

Vi​(y)=Ei​(y)+y′max​[Vi−1​(y′)+T(y′,y)]

### Calculate time 2 → S

Previous states:

### S → S

4+2=6

### W → S

1+0=1

### R → S

−2−3=−5
Best:

6
Add observation score:

E(1.1,S)=4
Therefore:

V2​(S)=6+4=10​

### Time 2 → W

### S → W

4+1=5

### W → W

1+2=3

### R → W

−2+0=−2
Best:

5
Add:

E(1.1,W)=1
Therefore:

V2​(W)=5+1=6​

### Time 2 → R

### S → R

4−3=1

### W → R

1+1=2

### R → R

−2+2=0
Best:

2
Add:

E(1.1,R)=−2
Therefore:

V2​(R)=2−2=0​

### 7. DP table so far

Time | Acceleration | S | W | R | 
1 | 1.0 | 4 | 1 | -2 | 
2 | 1.1 | 10 | 6 | 0 | 

The best path ending in S has score 10.

And that path is:

S→S

### 8. Position 3

Acceleration:

1.2
Observation scores:

S=4,W=1,R=−2
Calculate S:

max(10+2, 6+0, 0−3)+4
=max(12,6,−3)+4
=12+4
V3​(S)=16​
Calculate W:

max(10+1, 6+2, 0+0)+1
=max(11,8,0)+1
V3​(W)=12​
Calculate R:

max(10−3, 6+1, 0+2)−2
=max(7,7,2)−2
V3​(R)=5​
So:

Time | Acceleration | S | W | R | 
1 | 1.0 | 4 | 1 | -2 | 
2 | 1.1 | 10 | 6 | 0 | 
3 | 1.2 | 16 | 12 | 5 | 

The best path so far is:

S→S→S

### 9. Now something interesting happens

At time 4:

x4​=3.0
This is a high acceleration, so Running should receive a high observation score.

E(3.0,R)=4
Let's calculate.

### Time 4 → R

Previous S:

16+(−3)=13
Previous W:

12+1=13
Previous R:

5+2=7
Best:

13
Add:

E(3.0,R)=4
Therefore:

V4​(R)=13+4
17​
Notice something fascinating:

The best path to Running does not necessarily come from the previous best state overall.

At time 3:

S=16
was the best state.

But to reach R at time 4, we compare all previous states.

### 10. Calculate time 4 → S

max(16+2, 12+0, 5−3)−2
=max(18,12,2)−2
=18−2
16​

### 11. Calculate time 4 → W

max(16+1, 12+2, 5+0)+1
=max(17,14,5)+1
18​
Therefore:

Time | Acceleration | S | W | R | 
1 | 1.0 | 4 | 1 | -2 | 
2 | 1.1 | 10 | 6 | 0 | 
3 | 1.2 | 16 | 12 | 5 | 
4 | 3.0 | 16 | 18 | 17 | 

Notice that W has the highest score at time 4, even though acceleration is high.

Why?

Because the transition structure still matters.

### 12. Position 5

Now acceleration:

3.2
Observation:

S=−2,W=1,R=4
Calculate R:

max(16−3, 18+1, 17+2)+4
=max(13,19,19)+4
=19+4
V5​(R)=23​
So the best final state is:

R​

### 13. Final DP table

Our complete table is:

Time | Acceleration | Standing S | Walking W | Running R | 
1 | 1.0 | 4 | 1 | -2 | 
2 | 1.1 | 10 | 6 | 0 | 
3 | 1.2 | 16 | 12 | 5 | 
4 | 3.0 | 16 | 18 | 17 | 
5 | 3.2 | 13? | 20? | 23 | 

The exact intermediate values for the last row, if fully calculated, are:

### Time 5 → S

max(16+2,18+0,17−3)−2
=max(18,18,14)−2
=16

### Time 5 → W

max(16+1,18+2,17+0)+1
=max(17,20,17)+1
=21

### Time 5 → R

max(16−3,18+1,17+2)+4
=max(13,19,19)+4
=23
So the correct final row is:

Time | S | W | R | 
5 | 16 | 21 | 23 | 

### 14. Backtracking

We know:

y5​=R
For R at time 5, the maximum came from either:

W→R
or:

R→R
Both gave 19 before adding the observation score.

Let's choose the path determined by the stored backpointer. Suppose the implementation stores W.

Then:

y4​=W
For time 4, W came from:

S→W
Therefore:

y3​=S
For time 3, S came from:

S→S
Therefore:

y2​=S
And:

y1​=S
Thus one optimal path is:

S→S→S→W→R​

### 15. Final interpretation

Our sensor data was:

Time:          1    2    3    4    5
Acceleration: 1.0  1.1  1.2  3.0  3.2

The CRF predicts:

Time:          1    2    3    4    5
Activity:      S    S    S    W    R

So:

Standing→Standing→Standing→Walking→Running​
This makes intuitive sense: the person was initially stationary, began moving, and then started running.

### 16. Why CRF is useful here

Imagine that the sensor briefly produces an abnormal measurement:

1.0   1.1   3.5   1.2   1.1

A simple classifier might say:

S     S     R     S     S

because 3.5 looks like running.

But a CRF can say:

"Wait. The surrounding observations indicate Standing, and the transition S → R is unlikely. Maybe this is sensor noise."

So it might predict:

S → S → S → S → S

depending on the learned weights.

This is one of the major advantages of sequence models.

### 17. The CRF structure here

Notice that nothing here involves language or words.

The CRF receives:

sensor measurements​
and predicts:

activity sequence​
The same mathematical mechanism applies:

Observations+learned feature weights+transition weights
↓
sequence scores
↓
Viterbi / Dynamic Programming​
↓
best sequence​

### One more important observation

This example makes the role of dynamic programming particularly clear.

At each time i, we don't remember every possible history.

For example, at time 4 we only retain:

Best path ending in S
Best path ending in W
Best path ending in R

Then time 5 uses those three stored results.

That is the same principle we saw in POS tagging, but here the "words" have been replaced by sensor measurements.

So CRF is not fundamentally an NLP model. It is a probabilistic sequence-labeling model that can be applied whenever we have:

X1​,X2​,…,Xn​→Y1​,Y2​,…,Yn​​
where neighboring Y's have meaningful relationships.

---

## 19. Teacher prompt

How the illustrative scores are assigned in Sohan lives in Kolkata  ?

---

## 20. ChatGPT response

Yes. This is an important point, because in our earlier “Sohan lives in Kolkata” example I gave scores such as +1.5, +1.6, +2.9, but I did not show where those numbers actually come from.

The key point is:

Those scores are not assigned manually by the CRF. They are learned from the training data.

The numbers I used were illustrative learned weights, chosen to make the simulation easy to follow. In a real CRF, the weights are estimated by maximizing the conditional log-likelihood, typically using an optimization method such as L-BFGS. The gradient is essentially observed feature counts − model-expected feature counts, with regularization often added. Springer+1

Let's now see exactly how the idea works.

### 1. Start with a training dataset

Suppose we have this NER training data:

Sentence | Correct labels | 
Riya lives in Kolkata | B-PER O O B-LOC | 
Rahul lives in Delhi | B-PER O O B-LOC | 
Amit visits Mumbai | B-PER O B-LOC | 
Sita visits Delhi | B-PER O B-LOC | 
Kolkata is beautiful | B-LOC O O | 
Delhi is crowded | B-LOC O O | 

We want the CRF to learn weights from this data.

### 2. Define a feature

Consider this feature:

f1​(xi​,yi​)={10​if word is capitalized and yi​=B-PERotherwise​
This feature asks:

"Is the current word capitalized and labeled as the beginning of a person?"

For example:

Riya       B-PER     → feature = 1
Rahul      B-PER     → feature = 1
Amit       B-PER     → feature = 1
Sita       B-PER     → feature = 1

Kolkata    B-PER     → feature = 1 also
Delhi      B-PER     → feature = 1 also

Notice that the feature doesn't inherently know that Kolkata is a location. It is just one feature.

### 3. Another feature

Consider:

f2​(xi​,yi​)={10​if word is "Kolkata" and yi​=B-LOCotherwise​
In our training data:

Kolkata → B-LOC

appears twice:

Riya lives in Kolkata
                    ↑
                 B-LOC

Kolkata is beautiful
↑
B-LOC

Therefore the observed count of this feature is:

2​
CRF training uses these empirical feature counts and compares them with the counts expected under its current probability distribution. MADlib+1

### 4. Another important feature: transition

Now consider:

f3​(yi−1​,yi​)={10​if yi−1​=B-PER,yi​=Ootherwise​
Look at:

Riya lives in Kolkata
B-PER O    O B-LOC
       ↑

and:

Rahul lives in Delhi
B-PER O    O B-LOC
       ↑

and:

Amit visits Mumbai
B-PER O    B-LOC
       ↑

and:

Sita visits Delhi
B-PER O    B-LOC
       ↑

So:

B-PER→O
occurs 4 times.

Thus:

Observed(f3​)=4

### 5. Initially, suppose all weights are zero

We can start with:

w1​=0
w2​=0
w3​=0
This is just an illustrative initialization.

Therefore the model initially has no preference.

### 6. How does the first weight get increased?

Consider:

f2​="Kolkata"→B-LOC
We observed this feature 2 times.

Suppose that, with the current weights, the model's expected count is only:

Expected(f2​)=0.4
Then:

Gradient=Observed−Expected
so:

Gradient=2−0.4
=1.6
The gradient is positive.

Therefore the optimization algorithm increases w2​.

If we temporarily use a simple learning rate:

η=0.1
then:

w2new​=w2old​+η(Gradient)
=0+0.1(1.6)
w2​=0.16​
This is the idea behind how the weight moves.

The actual CRF training process normally uses numerical optimization such as L-BFGS rather than this simplistic one-step update. MADlib

### 7. Why does the weight become positive?

Because the training data says:

Kolkata → B-LOC

is common.

The model initially doesn't expect this feature often enough.

Therefore:

Observed>Expected
and the optimizer pushes the weight upward.

In general:

Observed>Expected⇒increase weight​
and:

Observed<Expected⇒decrease weight​
This observed-vs-expected interpretation is the standard CRF gradient intuition. Springer+1

### 8. Now consider an incorrect feature

Suppose we have:

f4​="Kolkata"→B-PER
Our training data has:

Kolkata → B-LOC

not:

Kolkata → B-PER

So its observed count is:

Observed(f4​)=0
Suppose the model currently expects:

Expected(f4​)=0.5
Then:

Gradient=0−0.5
=−0.5
Therefore the optimizer pushes its weight downward.

So we might eventually get something like:

w4​<0​
This means:

"Kolkata being labeled B-PER is evidence against that label."

### 9. This produces the familiar positive and negative weights

After many optimization iterations, we might get something like:

Feature | Illustrative learned weight | 
Kolkata → B-LOC | +2.9 | 
Delhi → B-LOC | +3.0 | 
Mumbai → B-LOC | +2.8 | 
Riya → B-PER | +2.4 | 
Rahul → B-PER | +2.5 | 
Amit → B-PER | +2.3 | 
Kolkata → B-PER | −1.5 | 
Delhi → B-PER | −1.4 | 

These numbers are not obtained by simply counting occurrences.

The counts influence the weights through the CRF likelihood optimization.

### 10. What about "Sohan"?

Now comes the interesting part.

Suppose our test sentence is:

Sohan lives in Kolkata

But Sohan never appeared in training.

Therefore we cannot learn a feature specifically saying:

Sohan→B-PER
from this dataset.

Instead, we can define a generalized feature:

f5​={10​if word starts with capital letter and label=B-PERotherwise​
Training contains:

Riya
Rahul
Amit
Sita

all labeled:

B-PER

So this feature gets positive evidence.

After training, suppose its learned weight is:

w5​=+1.5​
Then for:

Sohan

the feature fires:

f5​(Sohan,B-PER)=1
and therefore contributes:

1.5×1=1.5
to the sequence score.

That's where my earlier:

+1.5​
came from.

It was an illustrative learned weight for a capitalization/person feature.

### 11. Similarly for Kolkata

Suppose the trained CRF has learned:

wKolkata,B-LOC​=+2.9
When it sees:

Kolkata

with candidate label:

B-LOC
the feature fires:

f=1
Therefore its contribution is:

wf=2.9×1
2.9​
If the candidate label were B-PER:

f=0
for this particular feature, so this feature contributes:

2.9×0=0

### 12. And what about the transition +1.6?

Suppose training has repeatedly shown:

B-PER → O

So we define:

f6​(yi−1​,yi​)=1
when:

yi−1​=B-PER
and:

yi​=O
After training, suppose its learned weight is:

w6​=+1.6​
Then whenever the candidate sequence contains:

B-PER→O
the score receives:

+1.6

### 13. Now let's revisit Sohan

Consider:

Y=B-PER,O,O,B-LOC
for:

Sohan lives in Kolkata

Suppose our illustrative trained CRF has these weights:

Feature | Weight | 
Capitalized + B-PER | +1.5 | 
B-PER → O | +1.6 | 
"lives" → O | +0.8 | 
O → O | +0.7 | 
O → B-LOC | +1.9 | 
"Kolkata" → B-LOC | +2.9 | 

Then:

### Sohan

1.5

### B-PER → O

1.6

### lives → O

0.8

### O → O

0.7

### O → B-LOC

1.9

### Kolkata → B-LOC

2.9
Total:

1.5+1.6+0.8+0.7+1.9+2.9
9.4​
That is the illustrative sequence score I used earlier.

### 14. But there is an important correction

We should distinguish three different things:

### A. Feature value

Usually:

fk​∈{0,1}
For example:

fKolkata,B-LOC​=1
if the feature fires.

### B. Learned weight

For example:

wKolkata,B-LOC​=2.9
This is learned during training.

### C. Feature contribution

The contribution to the score is:

wk​fk​
Therefore:

2.9×1=2.9
So:

Feature contribution=weight×feature value​
The total CRF score is the sum of these contributions across the sequence. MADlib

### 15. So where do the actual numbers come from?

This is the complete process:

Training sentences
        ↓
Define feature functions
        ↓
Initialize weights
        ↓
Calculate score/probability
        ↓
Calculate expected feature counts
        ↓
Compare:
Observed counts − Expected counts
        ↓
Calculate gradient
        ↓
Optimization algorithm
        ↓
Update weights
        ↓
Repeat many times
        ↓
Converged weights

Mathematically, for a feature fk​:

∂wk​∂L​=Observedk​−Expectedk​
(with a regularization term in a regularized CRF). Springer+1

### 16. One very important point about my previous example

When I wrote:

+1.5,+1.6,+0.8,+0.7,+1.9,+2.9
I did not mean that these exact numbers would be calculated from the six training sentences by hand.

They were deliberately chosen as illustrative learned weights so that we could demonstrate Viterbi/dynamic programming.

In a real CRF:

Training data→feature counts→likelihood→optimization→learned weights​
and the final weights could be, for example,

1.37,2.84,−0.92,0.63,…
rather than the convenient numbers I used.

### The most important distinction

Think of it this way:

Training determines the weights​
while:

Viterbi uses those weights​
to find the best label sequence.

So the training phase answers:

"What should the weights be?"

and the Viterbi phase answers:

"Given these weights, what is the best sequence?"

That distinction is crucial for understanding CRFs.

---

## 21. Teacher prompt

Convert to writing block

---

## 22. ChatGPT response

Here is the complete explanation in a writing block.

How CRF Scores Are Learned: Sohan Lives in Kolkata

### How Are the Illustrative CRF Scores Assigned in “Sohan Lives in Kolkata”?
This is an important point because, in the earlier “Sohan lives in Kolkata” example, scores such as +1.5, +1.6, +2.9, etc. were used. We now need to understand where these numbers actually come from.
The key point is:
Those scores are not manually assigned by the CRF. They are learned from the training data.
The numbers used in the earlier simulation were illustrative learned weights, chosen to make the CRF calculation easy to understand.
In a real CRF, the weights are estimated during the training process by maximizing the conditional log-likelihood, usually using a numerical optimization algorithm such as L-BFGS.

### 1. Start with a Training Dataset
Suppose we have the following NER training data:
Sentence | Correct labels | 
Riya lives in Kolkata | B-PER O O B-LOC | 
Rahul lives in Delhi | B-PER O O B-LOC | 
Amit visits Mumbai | B-PER O B-LOC | 
Sita visits Delhi | B-PER O B-LOC | 
Kolkata is beautiful | B-LOC O O | 
Delhi is crowded | B-LOC O O | 

We want the CRF to learn appropriate weights from this training data.

### 2. Define a Feature
Consider the following feature:
[
f_1(x_i,y_i)=
\begin{cases}
1 & \text{if the word is capitalized and } y_i=B\text{-PER}\
0 & \text{otherwise}
\end{cases}
]
This feature asks:
“Is the current word capitalized and labeled as the beginning of a person?”
For example:
Riya       B-PER     → feature = 1
Rahul      B-PER     → feature = 1
Amit       B-PER     → feature = 1
Sita       B-PER     → feature = 1
The same feature can also fire for Kolkata or Delhi if they are being considered with the candidate label B-PER, because the feature only checks capitalization and the candidate label.
This illustrates an important point:
A feature does not have to be a specific word. It can capture a general property of the observation.

### 3. A Word-Specific Feature
Now consider another feature:
[
f_2(x_i,y_i)=
\begin{cases}
1 & \text{if the word is “Kolkata” and } y_i=B\text{-LOC}\
0 & \text{otherwise}
\end{cases}
]
In our training data, this feature fires when we encounter:
Riya lives in Kolkata
                  ↑
                B-LOC
and:
Kolkata is beautiful
↑
B-LOC
Therefore, its observed count is:
[
Observed(f_2)=2
]
The observed count alone, however, is not the final weight.

### 4. A Transition Feature
CRF also learns relationships between neighboring labels.
Consider:
[
f_3(y_{i-1},y_i)=
\begin{cases}
1 & \text{if }y_{i-1}=B\text{-PER and }y_i=O\
0 & \text{otherwise}
\end{cases}
]
Look at the training sentences:
Riya lives in Kolkata
B-PER O O B-LOC
      ↑
Rahul lives in Delhi
B-PER O O B-LOC
      ↑
Amit visits Mumbai
B-PER O B-LOC
      ↑
Sita visits Delhi
B-PER O B-LOC
      ↑
Thus:
[
Observed(f_3)=4
]
The CRF therefore receives strong evidence that:
[
B\text{-PER}\rightarrow O
]
is a common transition.

### 5. Initially, Suppose the Weights Are Zero
For simplicity, suppose we initially have:
[
w_1=0
]
[
w_2=0
]
[
w_3=0
]
These are only illustrative initial values.
The model initially has no preference.

### 6. How Does a Weight Become Positive?
Consider:
[
f_2=\text{“Kolkata”}\rightarrow B\text{-LOC}
]
We observed this feature twice:
[
Observed(f_2)=2
]
Suppose that, using the current model, the expected count is:
[
Expected(f_2)=0.4
]
The CRF gradient contains the difference:
[
Gradient=Observed-Expected
]
Therefore:
[
Gradient=2-0.4
]
[
=1.6
]
The gradient is positive.
Therefore, the optimization procedure pushes the weight upward.
For illustration, suppose we temporarily use a learning rate:
[
\eta=0.1
]
Then:
w_2^{old}+\eta(Gradient)
]
[
=0+0.1(1.6)
]
[
=0.16
]
So the weight increases.
This is only a simplified illustration. Actual CRF implementations normally use numerical optimization methods rather than this single simple update.

### 7. Why Does the Weight Become Positive?
The training data says:
Kolkata → B-LOC
occurs frequently.
But the current model does not expect that feature often enough.
Thus:
[
Observed>Expected
]
and the optimizer increases its weight.
In general:
[
\boxed{
Observed>Expected
\Rightarrow
\text{weight tends to increase}
}
]
and:
[
\boxed{
Observed<Expected
\Rightarrow
\text{weight tends to decrease}
}
]
This observed-versus-expected feature-count idea is fundamental to CRF training.

### 8. What Happens to an Incorrect Feature?
Now consider:
[
f_4=\text{“Kolkata”}\rightarrow B\text{-PER}
]
In our training data, Kolkata is labeled:
[
B\text{-LOC}
]
not:
[
B\text{-PER}
]
Therefore:
[
Observed(f_4)=0
]
Suppose the current model expects:
[
Expected(f_4)=0.5
]
Then:
[
Gradient=0-0.5
]
[
=-0.5
]
The negative gradient pushes the weight downward.
Eventually, we might obtain:
[
\boxed{w_4<0}
]
Thus, a negative weight means that this feature provides evidence against the corresponding label or transition.

### 9. After Training
After many optimization iterations, the CRF might learn illustrative weights such as:
Feature | Illustrative learned weight | 
Kolkata → B-LOC | +2.9 | 
Delhi → B-LOC | +3.0 | 
Mumbai → B-LOC | +2.8 | 
Riya → B-PER | +2.4 | 
Rahul → B-PER | +2.5 | 
Amit → B-PER | +2.3 | 
Kolkata → B-PER | −1.5 | 
Delhi → B-PER | −1.4 | 

These numbers are not simply occurrence counts.
The counts influence the weights through likelihood optimization.

### 10. What About “Sohan”?
Now consider the test sentence:
Sohan lives in Kolkata
Suppose Sohan did not occur in the training data.
Therefore, the model cannot directly learn a feature such as:
[
Sohan\rightarrow B\text{-PER}
]
from this training set.
Instead, we can use a generalized feature:
[
f_5=
\begin{cases}
1 & \text{if the word starts with a capital letter and }y_i=B\text{-PER}\
0 & \text{otherwise}
\end{cases}
]
The training data contains:
Riya
Rahul
Amit
Sita
all labeled:
[
B\text{-PER}
]
Therefore, the model can learn that:
Capitalized words are often associated with B-PER.
Suppose, after training, this generalized feature receives the illustrative weight:
[
\boxed{w_5=+1.5}
]
When the CRF sees:
Sohan
the feature fires:
[
f_5(Sohan,B\text{-PER})=1
]
Therefore, its contribution to the score is:
1.5\times1
]
[
\boxed{1.5}
]
This explains the illustrative +1.5 used earlier.

### 11. What About “Kolkata → B-LOC = +2.9”?
Suppose the trained CRF learns:
[
w_{\text{Kolkata,B-LOC}}=+2.9
]
When the test sentence contains:
Kolkata
and we consider the candidate label:
[
B\text{-LOC}
]
the corresponding feature fires:
[
f=1
]
Therefore:
2.9\times1
]
[
\boxed{2.9}
]
So Kolkata → B-LOC contributes +2.9 to the total sequence score.

### 12. What About the Transition Weight +1.6?
Suppose the CRF learns the transition:
[
B\text{-PER}\rightarrow O
]
as a favorable transition.
After training, suppose its illustrative weight is:
[
\boxed{w=+1.6}
]
Whenever the candidate label sequence contains:
[
B\text{-PER}\rightarrow O
]
the feature fires:
[
f=1
]
and therefore contributes:
\boxed{1.6}
]
to the sequence score.

### 13. Revisit “Sohan Lives in Kolkata”
Suppose our illustrative trained CRF has learned:
Feature | Weight | 
Capitalized + B-PER | +1.5 | 
B-PER → O | +1.6 | 
“lives” → O | +0.8 | 
O → O | +0.7 | 
O → B-LOC | +1.9 | 
“Kolkata” → B-LOC | +2.9 | 

Consider the candidate sequence:
[
Y=
B\text{-PER},O,O,B\text{-LOC}
]
for:
Sohan lives in Kolkata
The score receives contributions from the active features.

### Sohan → B-PER
[
+1.5
]

### B-PER → O
[
+1.6
]

### lives → O
[
+0.8
]

### O → O
[
+0.7
]

### O → B-LOC
[
+1.9
]

### Kolkata → B-LOC
[
+2.9
]
Therefore:
1.5+1.6+0.8+0.7+1.9+2.9
]
[
\boxed{Score(X,Y)=9.4}
]
This is an illustrative sequence score.

### 14. Three Things Must Be Distinguished
It is very important not to confuse the following three quantities.

### A. Feature value
Usually:
[
f_k\in{0,1}
]
For example:
[
f_{\text{Kolkata,B-LOC}}=1
]
when that feature is active.

### B. Learned weight
For example:
[
w_{\text{Kolkata,B-LOC}}=2.9
]
This is learned during CRF training.

### C. Feature contribution
The contribution is:
[
w_kf_k
]
Therefore:
[
2.9\times1=2.9
]
Hence:
\text{weight}\times\text{feature value}
}
]
The total CRF score is obtained by adding the contributions of all active features over the entire sequence.

### 15. Where Do the Actual Numbers Come From?
The complete training process can be visualized as:
Training sentences
        ↓
Define feature functions
        ↓
Initialize weights
        ↓
Calculate sequence scores/probabilities
        ↓
Calculate expected feature counts
        ↓
Compare observed and expected counts
        ↓
Calculate gradient
        ↓
Optimization algorithm
        ↓
Update weights
        ↓
Repeat many times
        ↓
Converged learned weights
For a feature (f_k), the basic gradient intuition is:
[
\frac{\partial L}{\partial w_k}
\approx
Observed_k-Expected_k
]
with a regularization term when regularized training is used.

### 16. Important Correction About the Earlier Example
When the earlier example contained numbers such as:
[
+1.5,\quad +1.6,\quad +0.8,\quad +0.7,\quad +1.9,\quad +2.9
]
these numbers were not calculated directly from the small training dataset.
They were deliberately selected as illustrative learned weights to demonstrate how CRF scoring and Viterbi decoding work.
In an actual implementation, the learned weights might instead look like:
[
1.37,\quad 2.84,\quad -0.92,\quad 0.63,\ldots
]
depending on:
the training data,

the feature definitions,

regularization,

optimization procedure,

and implementation details.

### 17. Training and Prediction Are Two Different Stages
This distinction is extremely important.

### Training asks:
What should the feature weights be?
The process is:
[
Training\ Data
\rightarrow
Features
\rightarrow
Optimization
\rightarrow
Weights
]

### Prediction asks:
Given these learned weights, what is the best label sequence?
The process is:
[
New\ Sentence
\rightarrow
Feature\ Scores
\rightarrow
Sequence\ Scores
\rightarrow
Viterbi
\rightarrow
Best\ Label\ Sequence
]
Therefore:
[
\boxed{
\text{Training determines the weights}
}
]
while:
[
\boxed{
\text{Viterbi uses the weights}
}
]
to find the best label sequence.

### 18. Final Picture
For:
Sohan lives in Kolkata
the CRF works conceptually as:
                 TRAINING
                    ↓
          Labeled training data
                    ↓
             Feature functions
                    ↓
          Learn feature weights
                    ↓
              Trained CRF
                    ↓
                 TESTING
                    ↓
          Sohan lives in Kolkata
                    ↓
         Calculate feature values
                    ↓
         Multiply by learned weights
                    ↓
         Calculate sequence scores
                    ↓
          Viterbi / Dynamic Programming
                    ↓
              Backtracking
                    ↓
       Sohan/B-PER lives/O in/O Kolkata/B-LOC
The central idea is therefore:
[
\boxed{
\text{Training data}
\rightarrow
\text{learned feature weights}
\rightarrow
\text{sequence scoring}
\rightarrow
\text{Viterbi decoding}
\rightarrow
\text{best label sequence}
}
]
The numbers such as +1.5 or +2.9 are therefore not arbitrary numbers in a real CRF. They represent the outcome of the weight-learning process. In a classroom simulation, however, we can choose convenient illustrative values so that the mechanics of CRF scoring and dynamic programming can be demonstrated clearly.

---

## 23. Teacher prompt

Convert to writing block

---

## 24. ChatGPT response

CRF Label Sequence

→ visits/O → Delhi/LOC

---

## 25. Teacher prompt

Show the functionality of CRF (dynamic programming) for the sentence: Riya lives in Kolkata

---

## 26. ChatGPT response

Certainly. Let us simulate the dynamic programming (Viterbi) operation of a linear-chain CRF for:

Riya lives in Kolkata

We will use three possible NER labels:

B-PER = Beginning of a person name

B-LOC = Beginning of a location name

O = Outside any named entity

So the tokens are:

Position | Word | 
1 | Riya | 
2 | lives | 
3 | in | 
4 | Kolkata | 

The expected labeling is:

Riya → B-PER, lives → O, in → O, Kolkata → B-LOC

### 1. CRF scoring idea

For a particular label sequence Y, CRF calculates a total score:

Score(X,Y)=i∑​Ei​(yi​)+i∑​T(yi−1​,yi​)
where:

Ei​(yi​) = emission score for assigning label yi​ to word i

T(yi−1​,yi​) = transition score from previous label to current label.

For this simulation, we will use illustrative scores to demonstrate the dynamic-programming mechanism. In a real CRF, these scores are learned from training data.

### 2. Assume the emission scores

Suppose the trained CRF gives us the following scores:

### Position 1: "Riya"

Label | Emission score | 
B-PER | +2.5 | 
O | +0.2 | 
B-LOC | -0.5 | 

Because Riya is a person's name, B-PER receives a high score.

### Position 2: "lives"

Label | Emission score | 
B-PER | -0.5 | 
O | +1.0 | 
B-LOC | -0.5 | 

### Position 3: "in"

Label | Emission score | 
B-PER | -0.6 | 
O | +0.8 | 
B-LOC | -0.5 | 

### Position 4: "Kolkata"

Label | Emission score | 
B-PER | -1.0 | 
O | +0.1 | 
B-LOC | +2.8 | 

Because Kolkata is a location, B-LOC receives a high score.

### 3. Assume transition scores

Suppose the CRF has learned these transition scores:

From ↓ / To → | B-PER | O | B-LOC | 
B-PER | -0.5 | +1.5 | -0.5 | 
O | -0.5 | +0.7 | +1.2 | 
B-LOC | -0.5 | +0.3 | -0.5 | 

For example:

T(B-PER,O)=+1.5
means that after a person's name, an O label is quite plausible.

And:

T(O,B-LOC)=+1.2
means that an ordinary word followed by a location is also plausible.

### 4. Dynamic programming starts

The CRF does not need to calculate all possible label sequences explicitly.

There are:

34=81
possible label sequences.

Instead, dynamic programming keeps only the best score reaching each label at each position.

The Viterbi recurrence is:

Vi​(y)=Ei​(y)+y′max​[Vi−1​(y′)+T(y′,y)]

### 5. Position 1 — "Riya"

There is no previous word.

Therefore:

V1​(y)=E1​(y)
So:

Label | Score | 
B-PER | 2.5 | 
O | 0.2 | 
B-LOC | -0.5 | 

Therefore, the best label for Riya is:

B-PER​

### 6. Position 2 — "lives"

Now we calculate the score for each possible label.

### Case 1: lives → B-PER

We consider all possible previous labels.

From B-PER:

2.5+(−0.5)=2.0
From O:

0.2+(−0.5)=−0.3
From B-LOC:

−0.5+(−0.5)=−1.0
The maximum is:

max(2.0,−0.3,−1.0)=2.0
Add the emission score for lives → B-PER:

V2​(B-PER)=2.0+(−0.5)
V2​(B-PER)=1.5​

### Case 2: lives → O

From B-PER:

2.5+1.5=4.0
From O:

0.2+0.7=0.9
From B-LOC:

−0.5+0.3=−0.2
Maximum:

4.0
Add emission +1.0:

V2​(O)=4.0+1.0
V2​(O)=5.0​
The backpointer is:

O←B-PER

### Case 3: lives → B-LOC

From B-PER:

2.5−0.5=2.0
From O:

0.2−0.5=−0.3
From B-LOC:

−0.5−0.5=−1.0
Maximum = 2.0.

Add emission −0.5:

V2​(B-LOC)=2.0−0.5
V2​(B-LOC)=1.5​

### DP table after "lives"

Word | B-PER | O | B-LOC | 
Riya | 2.5 | 0.2 | -0.5 | 
lives | 1.5 | 5.0 | 1.5 | 

So the best path so far is:

Riya/B-PER→lives/O​

### 7. Position 3 — "in"

Now repeat the same process.

### For in → B-PER

Previous best:

max⎩⎨⎧​1.5−0.5=1.05.0−0.5=4.51.5−0.5=1.0​=4.5
Add emission −0.6:

V3​(B-PER)=4.5−0.6
3.9​
Backpointer:

B-PER←O

### For in → O

1.5+1.5=3.0
5.0+0.7=5.7
1.5+0.3=1.8
Maximum:

5.7
Add emission +0.8:

V3​(O)=5.7+0.8
6.5​
Backpointer:

O←O

### For in → B-LOC

1.5−0.5=1.0
5.0−0.5=4.5
1.5−0.5=1.0
Maximum = 4.5.

Add emission −0.5:

V3​(B-LOC)=4.5−0.5
4.0​
Backpointer:

B-LOC←O

### DP table

Word | B-PER | O | B-LOC | 
Riya | 2.5 | 0.2 | -0.5 | 
lives | 1.5 | 5.0 | 1.5 | 
in | 3.9 | 6.5 | 4.0 | 

The best state at position 3 is:

O​

### 8. Position 4 — "Kolkata"

This is the most interesting step.

### Kolkata → B-PER

From B-PER:

3.9−0.5=3.4
From O:

6.5−0.5=6.0
From B-LOC:

4.0−0.5=3.5
Maximum:

6.0
Add emission −1.0:

V4​(B-PER)=6.0−1.0
5.0​

### Kolkata → O

From B-PER:

3.9+1.5=5.4
From O:

6.5+0.7=7.2
From B-LOC:

4.0+0.3=4.3
Maximum:

7.2
Add emission +0.1:

V4​(O)=7.2+0.1
7.3​

### Kolkata → B-LOC

From B-PER:

3.9−0.5=3.4
From O:

6.5+1.2=7.7
From B-LOC:

4.0−0.5=3.5
Maximum:

7.7
Add emission for Kolkata → B-LOC:

+2.8
Therefore:

V4​(B-LOC)=7.7+2.8
10.5​
Backpointer:

B-LOC←O​

### 9. Final DP table

We now have:

Position | Word | B-PER | O | B-LOC | 
1 | Riya | 2.5 | 0.2 | -0.5 | 
2 | lives | 1.5 | 5.0 | 1.5 | 
3 | in | 3.9 | 6.5 | 4.0 | 
4 | Kolkata | 5.0 | 7.3 | 10.5 | 

The highest final score is:

10.5​
at:

B-LOC​

### 10. Backtracking

Now we follow the backpointers.

At position 4:

Kolkata→B-LOC
Its backpointer says:

B-LOC←O
Therefore position 3 is:

in→O
For position 3, the backpointer for O says:

O←O
Therefore position 2:

lives→O
For position 2:

O←B-PER
Therefore position 1:

Riya→B-PER
Thus the final sequence is:

Riya/B-PERlives/Oin/OKolkata/B-LOC​

### 11. What did dynamic programming actually do?

The important point is that CRF did not simply decide:

Riya = B-PER

lives = O

in = O

Kolkata = B-LOC

independently.

Instead, it considered the interaction between neighboring labels.

For example, at Kolkata, it compared:

B-PER→B-PER
O→B-PER
B-LOC→B-PER
and similarly for the other possible labels.

The best choice was:

O→B-LOC
because:

best score reaching O at "in"6.5​​+O→B-LOC1.2​​+"Kolkata"→B-LOC2.8​​=10.5​
This is the essence of dynamic programming in a CRF: at each position and label, retain only the best score from the previous position, rather than enumerating all 34=81 possible sequences.

### In one line

CRF→Emission scores + Transition scores→Viterbi Dynamic Programming→Backtracking→Best label sequence​
And for our sentence:

Riya lives in Kolkata→B-PER, O, O, B-LOC​

---

## 27. Teacher prompt

How the expected weight is getting calculated? It is not clearly shown in (Shohan lives in Kolkata. Kolkata is beautiful.)

---

## 28. ChatGPT response

Yes. The key point is that in a CRF, “expected weight” is not actually the right term. What we calculate is the expected feature count, and that expected count is then used to update the feature weight.

Let us use the small training example you mentioned:

Sohan lives in Kolkata

Kolkata is beautiful

and calculate it explicitly.

### 1. Training data

Suppose the correct labels are:

Sentence | Correct labels | 
Sohan lives in Kolkata | B-PER O O B-LOC | 
Kolkata is beautiful | B-LOC O O | 

Suppose we want to learn the weight of this feature:

f(X,Y,i)={10​if word = Kolkata and label = B-LOCotherwise​
Call its weight:

wKolkata,B-LOC​

### 2. Observed feature count

First look at the actual training labels.

### Sentence 1

Sohan lives in Kolkata

Correct labeling:

B−PER, O, O, B−LOC
At the word Kolkata, the label is B-LOC.

Therefore:

f=1

### Sentence 2

Kolkata is beautiful

Correct labeling:

B−LOC, O, O
Again, Kolkata has label B-LOC.

Therefore:

f=1
So the observed feature count is:

Observed=1+1=2​
This part is easy.

### 3. But where does EXPECTED count come from?

This is the important part.

The CRF asks:

Given the current weights, how often would I EXPECT this feature to occur across all possible label sequences?

It does not simply count the training examples.

For example, for:

Sohan lives in Kolkata

there are 3 possible labels per word:

B−PER, O, B−LOC
Therefore there are:

34=81
possible label sequences.

The CRF assigns a probability to each possible sequence.

For example:

P(Y∣X)=Z(X)eScore(X,Y)​
where

Z(X)=Y′∑​eScore(X,Y′)
is the partition function.

### 4. Let's make the calculation small enough to see

To clearly understand expected count, let's temporarily simplify the example.

Suppose we are interested only in the label of Kolkata, and assume the model currently gives these probabilities:

### Sentence 1: "Sohan lives in Kolkata"

Possible label for Kolkata | Probability | 
B-PER | 0.10 | 
O | 0.20 | 
B-LOC | 0.70 | 

The feature

Kolkata + B-LOC

fires only when Kolkata receives B-LOC.

Therefore its expected feature count for this sentence is:

0(0.10)+0(0.20)+1(0.70)
=0.70​
Notice what happened.

We didn't say:

Kolkata is B-LOC → count 1

Instead, CRF says:

According to my current model, there is a 70% probability that Kolkata will be B-LOC.

So the expected count is 0.70.

### 5. Second sentence

For:

Kolkata is beautiful

suppose the current CRF gives:

Possible label for Kolkata | Probability | 
B-PER | 0.05 | 
O | 0.15 | 
B-LOC | 0.80 | 

Again, our feature fires only for B-LOC.

Therefore:

Expected=0(0.05)+0(0.15)+1(0.80)
=0.80​

### 6. Total expected feature count

Now combine the two sentences:

Expected=0.70+0.80
Therefore:

Expected=1.50​
Compare:

Observed=2
Expected=1.50
So:

Observed−Expected=2−1.5
0.5​
The model is underestimating this feature.

Therefore its weight should increase.

### 7. Weight update

For a simple gradient-ascent illustration:

wnew​=wold​+η(Observed−Expected)
Suppose:

wold​=1.0
and learning rate:

η=0.1
Then:

wnew​=1.0+0.1(2−1.5)
=1.0+0.05
wnew​=1.05​
So the weight of:

Kolkata → B-LOC

increases from 1.0 to 1.05.

### 8. Why does the weight increase?

Because the training data says:

Kolkata→B-LOC​
happens 2 times.

But according to the current model, its expected occurrence is only:

1.5​
Therefore:

Observed>Expected
The CRF says:

"I am not giving enough probability to this feature. I should increase its weight."

Hence:

w↑​

### 9. What if Expected > Observed?

Suppose another feature is:

Kolkata → B-PER

This feature never occurs in our correct training labels.

Therefore:

Observed=0
Suppose the current model nevertheless expects it to occur 0.6 times:

Expected=0.6
Then:

Observed−Expected=0−0.6=−0.6
So:

wnew​=wold​+η(−0.6)
The weight decreases.

Thus:

Observed<Expected⇒weight decreases​

### 10. The complete idea

This is the central CRF training mechanism:

Observed feature count−Expected feature count=Gradient​
Then:

Gradient→Weight update​
For our example:

Feature | Observed | Expected | Difference | Effect | 
Kolkata → B-LOC | 2.0 | 1.5 | +0.5 | Increase weight | 
Kolkata → B-PER | 0 | 0.6 | −0.6 | Decrease weight | 

### 11. One very important correction

In the real CRF, the expected count is not calculated merely from the marginal probability of one word independently as I simplified above.

The proper calculation considers all possible label sequences:

E[fk​]=Y∑​P(Y∣X)fk​(X,Y)
For a feature at position i, this can be calculated from the appropriate marginal probability. For transition features, we use pairwise marginals such as:

P(yi−1​,yi​∣X)
These probabilities are obtained efficiently using forward-backward dynamic programming.

So there are actually two related dynamic-programming procedures:

During inference:

Viterbi→best label sequence​
During training:

Forward-Backward→expected feature counts​
This distinction is extremely important for understanding CRFs.

If you want, I can next show the complete Forward-Backward calculation for “Sohan lives in Kolkata”, including the partition function Z(X), forward values, backward values, marginal probability of Kolkata → B-LOC, and finally the exact expected count.

---

## 29. Teacher prompt

But still the partition function is not clearly understood. Illustrate how it is being calculated with an example.

---

## 30. ChatGPT response

Absolutely. The partition function Z(X) is probably the most important part to understand before the “expected feature count” becomes clear.

Let's calculate it completely with a very small CRF example, and then connect it to:

Sohan lives in Kolkata

### 1. What is the partition function?

For a CRF,

P(Y∣X)=Z(X)eScore(X,Y)​
where

Z(X)=Y′∑​eScore(X,Y′)​
In simple words:

The partition function is the sum of eScore over ALL possible label sequences.

It acts as the normalizing factor that converts raw sequence scores into probabilities.

### 2. Start with a very small example

Suppose our sentence contains only 2 words:

Sohan Kolkata

And suppose we have only two possible labels:

P = Person

L = Location

There are therefore:

22=4
possible label sequences.

They are:

Sequence | Meaning | 
P P | Sohan=P, Kolkata=P | 
P L | Sohan=P, Kolkata=L | 
L P | Sohan=L, Kolkata=P | 
L L | Sohan=L, Kolkata=L | 

The CRF must consider all four.

### 3. Give some illustrative scores

Suppose the CRF has calculated the following total sequence scores:

Label sequence | Score | 
P P | 1 | 
P L | 3 | 
L P | 0 | 
L L | 1 | 

These scores are obtained from:

Score=emission scores+transition scores
For the moment, we will assume these scores are already available.

### 4. Convert scores into eScore

The CRF doesn't directly use the scores as probabilities.

It calculates:

eScore
Therefore:

Sequence | Score | eScore | 
P P | 1 | e1=2.718 | 
P L | 3 | e3=20.086 | 
L P | 0 | e0=1 | 
L L | 1 | e1=2.718 | 

### 5. Now calculate the partition function

The partition function is simply the sum:

Z(X)=e1+e3+e0+e1
Therefore:

Z(X)=2.718+20.086+1+2.718
Z(X)=26.522​
That's it.

The partition function is the sum of the exponentiated scores of every possible label sequence.

### 6. Why do we need Z(X)?

Suppose we want the probability of:

P,L
Its score is:

Score(P,L)=3
So:

e3=20.086
But this isn't a probability yet.

We divide by Z(X):

P(P,L∣X)=26.522e3​
=26.52220.086​
P(P,L∣X)≈0.757​
So the CRF says that the sequence

Sohan/P, Kolkata/L​
has approximately 75.7% probability.

### 7. Calculate the probability of every sequence

Now you can see why the partition function is useful.

### P P

P(PP∣X)=26.5222.718​
≈0.103

### P L

P(PL∣X)=26.52220.086​
≈0.757

### L P

P(LP∣X)=26.5221​
≈0.038

### L L

P(LL∣X)=26.5222.718​
≈0.103
Check:

0.103+0.757+0.038+0.103≈1
Therefore the probabilities are properly normalized.

### 8. Now let's connect this to "Sohan lives in Kolkata"

Now consider the original sentence:

Sohan lives in Kolkata

Suppose we use only two labels to keep the calculation manageable:

P = Person

O = Outside

L = Location

Actually, with three labels and four words, we have:

34=81
possible sequences.

For example:

P,O,O,L
P,O,O,O
O,O,O,L
L,P,O,L
and so on.

There are 81 different sequences.

The CRF calculates a score for each one.

For example:

Label sequence | Score | 
P O O L | 9.4 | 
P O O O | 5.8 | 
P P O L | 6.2 | 
O O O L | 7.1 | 
O O O O | 4.5 | 
... | ... | 
L L L L | 1.2 | 

There are 81 rows in the complete table.

The partition function is:

Z(X)=e9.4+e5.8+e6.2+e7.1+e4.5+⋯+e1.2​
In other words:

Z(X)=all 81 label sequences∑​eScore(X,Y)​
We don't normally write out all 81 terms because that would be inefficient.

### 9. This is where Dynamic Programming becomes important

You might now ask:

"If there are 81 sequences, don't we have to calculate all 81?"

For a tiny sentence, we could.

But for a real sentence, this becomes enormous.

Suppose:

sentence length n=20

number of labels K=10

Then possible sequences:

1020
That's:

100,000,000,000,000,000,000
which is impossible to enumerate directly.

Therefore, CRF uses dynamic programming to calculate Z(X) efficiently.

### 10. Forward algorithm for calculating Z(X)

Let's return to our tiny example:

Sohan Kolkata

with labels:

P,L
Suppose the emission scores are:

Word | P | L | 
Sohan | 2 | 0 | 
Kolkata | 0 | 2 | 

And suppose transition scores are:

From → To | P | L | 
P | 0 | 1 | 
L | 0 | 0 | 

### Position 1: Sohan

Forward values are simply the emission scores:

α1​(P)=2
α1​(L)=0
But because Z requires exponentiated scores, we can work in exponentiated form:

α1​(P)=e2=7.389
α1​(L)=e0=1

### 11. Position 2: Kolkata

Now calculate the forward value for P.

There are two ways to reach P:

P→P
or

L→P
Therefore:

α2​(P)=eEmission(Kolkata,P)[α1​(P)eT(P,P)+α1​(L)eT(L,P)]
Substitute:

α2​(P)=e0[7.389e0+1e0]
=7.389+1
α2​(P)=8.389​

### 12. Calculate forward value for L

There are two ways to reach L:

P→L
or

L→L
Therefore:

α2​(L)=e2[α1​(P)e1+α1​(L)e0]
Substitute:

α2​(L)=e2[7.389e1+1e0]
Since:

e2=7.389
and

e1=2.718
we get:

α2​(L)=7.389[(7.389)(2.718)+1]
=7.389[20.086+1]
=7.389(21.086)
α2​(L)≈155.77​

### 13. Finally calculate Z(X)

At the end, the sequence can finish in either P or L.

Therefore:

Z(X)=α2​(P)+α2​(L)
Z(X)=8.389+155.77
Z(X)≈164.16​
That is the partition function.

### 14. Notice something important

Look at what happened.

Instead of explicitly calculating:

PP
PL
LP
LL
and then adding their exponentiated scores, the forward algorithm merged paths that share the same current state.

For example, when we reached Kolkata with label L, both paths:

P→L
and

L→L
were combined into:

α2​(L)
This is exactly why dynamic programming makes CRFs computationally practical.

### 15. The relationship with expected feature count

Now we can finally connect the three concepts you've been asking about:

### Step 1 — Calculate sequence scores

Score(X,Y)

### Step 2 — Calculate partition function

Z(X)=Y∑​eScore(X,Y)​
using forward dynamic programming.

### Step 3 — Calculate sequence probabilities

P(Y∣X)=Z(X)eScore(X,Y)​​

### Step 4 — Calculate expected feature counts

E[fk​]=Y∑​P(Y∣X)fk​(X,Y)​

### Step 5 — Compare with observed count

Gradient=Observed−Expected​

### Step 6 — Update the weight

wnew​=wold​+η(Observed−Expected)​
So the complete flow is:

Weights→Scores→Z(X)→Probabilities→Expected feature counts→Gradient→New weights​
The partition function is therefore the bridge between CRF scores and probabilities. Once Z(X) is understood, the calculation of expected feature counts becomes much easier to understand.

If you want, I can:

Show forward algorithm computation for partition function

Explain backward algorithm for expected count calculation

Demonstrate numeric example of partition function computation
