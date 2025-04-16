We have two positive integer arrays, representing the distances between occurrences of a specific marker sequence in a DNA fragment.
$$
a = [a_0, \ldots, a_n] \, \,\,\,  b = [b_0, \ldots,b_m] ;\,\,\, 0<m\leq n
$$
We want to define a measure of distance between the two.

## MinConv
We dub this way a metric that computes the distance by first performing a sliding subtraction between $a$ and $b$ and then taking the minimum value for each of the positions of $a$ (i.e. the column-wise minimum)
$$
ConvMin(a,b)_i = \min_{0\leq j \leq m} |a_{i+j} - b_j| \quad \text{for } i = 0, 1, \ldots, n-m
$$
This gives out a vector $c = [c_0, \ldots, c_{n-m}]$, which we aggregate by computing the sum. 

### Analysis
This metric is quite fast to compute and results are very good:
```
================================================
    RANKING METRICS REPORT on CHM13 vs HAP1
================================================

--- ACCURACY METRICS ---
Top-1 Accuracy: 0.8261
Top-3 Accuracy: 0.8696
Top-5 Accuracy: 0.9130

================================================
```
![[cm_minconv.png]]

![[Pasted image 20250409141348.png]]
![[Pasted image 20250409163018.png]]![[Pasted image 20250409163026.png]]
## Min-L1
Thinking about MinConv, I realized that the metric doesn't consider the sequence in any way. In fact by computing a "Sliding subtraction" we're actually computing the subtraction between every element of $a$ and $b$ and taking the minimum for each of the indices of $a$. 
At this point it's more straightforward to compute the whole difference matrix (L1 distance) and just take the column-wise minimum.

$$
MinMatch(a,b) = \sum_{i\leq n} \min_{j\leq m} |a_i-b_j|
$$

This gives you the minimum distance between each element of $a$ and $b$. We then take the minimum for each element of $a$, and sum (or avg.) the remaining elements.

### Analysis
This metric is quite fast to compute and results are similar to MinConv, and it outperforms for fragments.

```
================================================
    RANKING METRICS REPORT on CHM13 vs HAP1
================================================

--- ACCURACY METRICS ---
Top-1 Accuracy: 0.8261
Top-3 Accuracy: 0.8696
Top-5 Accuracy: 0.9130
```
![[Pasted image 20250409141306.png]]

![[Pasted image 20250409141323.png]]![[Pasted image 20250409163409.png]]

## ToDo
- Prova Jaccard
- Evo2
- Prova non-centromeri (solo se hai tempo)
