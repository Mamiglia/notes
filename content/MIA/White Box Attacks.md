---
title: White-Box Attacks
description: 
date: 18/12/2024
tags:
  - paper
status: 
url:
---
[Review](https://pdf.sciencedirectassets.com/271505/1-s2.0-S0950705122X0021X/1-s2.0-S0950705122011078/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEJT%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJHMEUCIAaWzluenNc%2B%2FC5C81N5QXgRpXLfvZ9Ejh5WnmfvN%2B9YAiEAqAlnZekj7d%2Fvp6geav3K1h9fAfPDg5AQSJn1H47jSqIqsgUIXRAFGgwwNTkwMDM1NDY4NjUiDPs6gP8ErtzKXG1YWSqPBSUnoNYTC7UXF3mjWN1%2BJqdBgvS8kGizrtLjAGe12%2BYNlP2vlmIh4CojSw%2FxONrXfuDRVchfOqSg%2B4v2gNooQtRKszAUfvRXS5%2FY1nofZwiHcIVbvUI2GG%2BcO3Eb6DTwADrL%2FO0PDwddW2klRpY%2BurA0a8hvbj65HzEQdwVCzZJYzVTd58gyuWAsJ7iXyDUMTGF6m0fdnoJj768W8XjlQbohs3r10Qrvg11CKLFt6fA%2FzwX8%2BJuSLTjzf3cw%2BZlwvExaFUDmGQZykIioJZOCiJXfsxLVIrlTIXklXwVctAwORjM6FVGlEbSdLyKPuXXfT91SrrQADh0BMwDyhtYAO2d2BkvfQHTSez5JFuGXhFt04XDxgVVzJ5GrSVx2EoGG56EhB6CyRdoKQowtYC10SML5iTyMGRxaTDuxStkKYrTMgtaYJKr5K5ZHoDHfb%2FMHnqFyUlJzEoNjRVuJgR5ZCIKg4aKUQKn7WU6CTrj1exLoOpAdW%2B4qILB%2FwC11KRxo0bXHoX7CoXP2X2Lu5ETkZPlBWYgUVvHXjAiqYK8%2BaIt34tLolUXdFgsqqaiBR0vULEU%2FaZJcRUOcjpRaui1gX8OzfiuN9U6vGOGGSuW38%2F%2Bngjs2f1NCboun5jtmHquvEt9aEJyxwmQGSmc5TDiLjWIzPs3QSRg%2BXTIgoqVPrZYd65MMj%2BRKjAvFLeWst5v49X1pc6RHJ7UGOhjWoJmUTuLTOstQpGDo4gRawi4nZs0u1FPIMaREPhzRUlNkoDZXvXHu3mpCmq9AdbI7R6IVIgXous9%2FJrHPIm5Ch4vcVQoYUIcczs7we6Ph95pJIdjjgWrrb6a91fIX%2Fd13oVrnMrN3IKr2Ootmk%2B8KqEfXpAMw7%2FOKuwY6sQGONXSzZV7duOnIXWynHPvorn8uKPjcKvl0hlEyW1TXEcEvmwOW%2F0%2FW%2BkGw%2Fg0yqqdPB%2Fq1B2xUF1Sxy06Q%2BStnDy3bsI0R%2BfC0BRFFXlnIZt4J0gmFbHzhTMQH2%2BJPAOBuKwmPJ1md17%2FeZ5hDKeIkokcHwDy%2Fk2oT91TPws%2FoB0WFIn91W0AaRYJZXAbsrFXs1%2FE4equ2EDNMejy2Xd84KNVLsySvNsoqosNissrP%2FeU%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20241218T122436Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTYQUCNJZ3N%2F20241218%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=2d8d5a6b0093cb44f941e9ce6190767b9aaf274451b4aa3a2f454a76f9d05885&hash=44741c4f046940f2e4eba6f70a536836559a9251400b8ae59c4cdc7b037d1ac9&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S0950705122011078&tid=spdf-74ee7762-47a2-4572-b0cf-06754d953aad&sid=dbb84f939bb3584b123a401893ea518f8e10gxrqa&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=0a1b59005607505259&rr=8f3f2178bd5cea43&cc=it)

## [Nasr Attack](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8835245)
They design a few white-box attacks:
- **Layer-Outputs**: They use the outputs (or activations) of the intermidiate layers, and find out that the best performing one is the last layer (equivalent to black-box)
- **Gradients**: 

## [Leino](https://www.usenix.org/system/files/sec20-leino.pdf)
Aggregate gradients, logits, activations etc..

## [Suri](https://arxiv.org/abs/2406.11544)
Use the inverse Hessian matrix to compute a score. Very expensive.

## [Li](https://arxiv.org/abs/2407.01306v1)
Use SHAP and Explainable AI techniques to compute a score.