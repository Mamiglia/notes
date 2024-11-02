[![](https://upload.wikimedia.org/wikipedia/commons/3/35/Chi-square_pdf.svg)](https://upload.wikimedia.org/wikipedia/commons/3/35/Chi-square_pdf.svg)

The FIM is a way to attribute parameter importance of a DNN, it’s used transversally in many domains such as _model merging, continual learning, federated learning,_ etc… but for us it’s interesting thanks to the implications in **machine unlearning** and **mechanistic interpretability**.

Currently the way to estimate it is by computing the mean of the squared gradients across the whole dataset, which could be quite expensive depending on the size of model and dataset.

I propose to use some bayesian prior in the estimation of such matrix, in order to use the **bias-variance tradeoff** to improve:

- **efficacy**: in scenarios of data scarcity.
- **efficiency**: as less data is necessary to compute the matrix.
- **noise elements**: currently most ways of using the FIM involves using only its top-k% values, so maybe by skewing the values toward zero we can remove this necessity and obtain less noisy values.

### Experiment Design

1. Gather a number of experiments involving the approximate fisher matrix computation, reproducing them
2. Store the fisher matrix of these models, compute statistics on its distribution
3. Develop the theory behind
4. Compute the new fisher matrices
5. validate results, compute statistics

### Impact

If something like this works out, then we’ll have improved a very common tool. Even though we might not have proven anything too different from what was avaliable in the past, we’ll have improved and solidified a tool transversal to many different fields, including machine unlearning.

### Relevant papers

- Machine Unlearning: [https://arxiv.org/abs/2308.07707](https://arxiv.org/abs/2308.07707)
- Model Merging: [https://arxiv.org/abs/2111.09832](https://arxiv.org/abs/2111.09832)
- Continual Learning: [https://arxiv.org/abs/1612.00796](https://arxiv.org/abs/1612.00796)
- Federated Learning: [https://arxiv.org/abs/2403.12329](https://arxiv.org/abs/2403.12329)

  

## Theory

Currently the FIM is approximated with the diagonal of the hessian of the model. in particular:

$F_\theta = \frac{1}{|\mathcal{D}| |\mathcal{Y}|}\sum_x \sum_y (\nabla_\theta \log p_\theta(y|x))^2$

i.e. the mean of the squared gradients computed for each sample and label of the dataset.

**IF** we assume the gradients to be independent, identically distributed normal random variables, then the elements of the FIM would all be noncentral Chi-Squared distribution with N degrees of freedom.

> [!important]  
> This IF statement is huge and probably just plainly false, still it’s interesting to see that we could easily impose some kind of prior shape on the FIM