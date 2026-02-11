---
title: Motion Dataset
description: 
date: 08/01/2025
tags:
  - paper
  - dataset
  - summary
url:
---
## [HumanML3D](https://github.com/EricGuo5513/HumanML3D)
HumanML3D is a 3D human motion-language dataset that originates from a combination of [HumanAct12](https://github.com/EricGuo5513/action-to-motion) and [Amass](https://github.com/nghorbani/amass/tree/master) dataset. It covers a broad range of human actions such as daily activities (e.g., 'walking', 'jumping'), sports (e.g., 'swimming', 'playing golf'), acrobatics (e.g., 'cartwheel') and artistry (e.g., 'dancing').

Each motion clip in HumanML3D comes with 3-4 single sentence descriptions annotated on Amazon Mechanical Turk. Motions are downsampled into 20 fps, with each clip lasting from 2 to 10 seconds, in SMPL format.

Overall, HumanML3D dataset consists of **14,616** motions and **44,970** descriptions composed by **5,371** distinct words. The total length of motions amounts to **28.59** hours. The average motion length is **7.1** seconds, while average description length is **12** words.

In order to obtain the dataset you need to clone the repo and run their code to process Amass dataset (they cannot distribute it directly).

AMASS is the union of 25 motion datasets. It can be downloaded after agreeing with the license. For each motion we know:
- the gender

For the [HumanAct12](https://drive.google.com/drive/folders/1TBY2x-gD6f3yzQ0WNmXP2-be3xu3qDkV) we know:
- The subject ID (12 total actors)
- group ID
- repetition number 
- This dataset is not annotated in natural language, rather there's a categorical amount of actions.
![[Pasted image 20250109164525.png]]
## [MotionX](https://motion-x-dataset.github.io/)
MotionX is the union of multiple datasets (including AMASS) of SMPL-X motions. It was obtained by aggregating datasets, scraping online videos and doing pose-estimation, or face/hands estimation in those datasets in which it was missing.

It appears that it doesn't provide any information about gender or subject ID, but it may depend on the single datasets that compose it. 


### Estimating Attributes
Since these datasets are so scarce of metadata we might want to estimate the actors' attributes, as it seems that you can estimate body attributes from SMPL data (height, gender, weight, etc...?), using [SHAPY](https://shapy.is.tue.mpg.de/).

Otherwise we can replicate their data pipeline and try to keep the metadata intact, for those datasets that have them available.
