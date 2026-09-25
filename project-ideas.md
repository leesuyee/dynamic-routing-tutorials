## Questions to explore 
The tools in the Intro-Tutorial notebook (generate PSTHs, assess response classification, attention modulation index) can be reused to answer further questions across the units, brain areas, and sessions in this dataset. Here are a few questions to follow-up on:  

### Sensory responses across the brain
- Which brain regions have the largest fraction of visually responsive neurons? Which are most auditory responsive?¹ 
- Do frontal cortical areas(e.g. `MOs`, `ACA`, `PL`) show sensory responses? If so, are they more often visual, auditory, or mixed?

References: 
- Siegle et al. (2021) [Survey of spiking in the mouse visual system reveals functional hierarchy](https://doi.org/10.1038/s41586-020-03171-x). *Nature*
- International Brain Laboratory (2025) [A brain-wide map of neural activity during complex behaviour](https://doi.org/10.1038/s41586-025-09235-0). *Nature*
- Harris et al. (2019) [Hierarchical organization of cortical and thalamic connectivity](https://doi.org/10.1038/s41586-019-1716-z). *Nature*

### Cell Types
- What features of the task are neurons responding to? Stimulus, context, licking, reward, running¹
- How are different cell types organized? For example, are there spatial clusters or gradients with respect to functional cell type profiles?²

References (in addition to references from previous section):
- Steinmetz et al. (2019) [Distributed coding of choice, action and engagement across the mouse brain](https://doi.org/10.1038/s41586-019-1787-x). *Nature*
- Bennett et al. (2026) [Map of spiking activity underlying change detection in the mouse visual system](https://doi.org/10.1016/j.cell.2026.06.025). *Cell*

### Context-dependent modulation

**Attention:**
- Where in the trial does the attention effect peak? Recompute AMI in sliding response windows (0-100ms, 100-250ms, 250-500ms) and compare across areas.
- Does the size of the AMI depend on behavioral performance? Further split trials by hit vs miss (for targets) or false alarm vs correct reject (for non-targets) and see how that affects attention modulation.

The AMI compares two contexts — attend-visual vs attend-auditory blocks. The same formula (or a slight variation) can quantify *any* pair of contexts you can define from the `trials` table. Propose a new "modulation index" of your own, then test it. Some starting points:

**Reward history:**
- Does prior outcome bias sensory responses? Compare neural activity responses following a rewarded trial vs an unrewarded trial.

**Arousal modulation:**
- How much of the attention effects are related to general arousal? We can measure arousal using the running speed and pupil size to classify a "high arousal" and "low arousal" state and compare neural activity. This is a useful control for the attention modulation index computed before.

References:
- Niell & Stryker (2010) [Modulation of visual responses by behavioral state in mouse visual cortex](https://doi.org/10.1016/j.neuron.2010.01.033). *Neuron*

¹ The Intro-Tutorial notebook shows a simple way to classify cell types by testing for changes in firing rate before and after the stimulus. This is straightforward, but are sensitive to the decisions we made when building the functions (time windows, thresholds, categories). Also remember 
that the function we wrote only tests for *excitatory* responses. More advanced, data-driven methods such as clustering or a generalized linear model can be used to identify and classify cell types based on their full response profiles. The following tutorials and reference are a good place to start. I recommend using these methods if you are already familiar with statistics, linear algebra, and regression, and comfortable with cross-validation in Python. 

- SWDB Data Book tutorials: [PCA](https://allenswdb.github.io/computational/data-analysis/PCA.html), [GLM](https://allenswdb.github.io/computational/data-analysis/GLM.html)

- Neuromatch Academy: [Dimensionality Reduction](https://compneuro.neuromatch.io/tutorials/W1D4_DimensionalityReduction/chapter_title.html) and [GLMs for Encoding](https://compneuro.neuromatch.io/tutorials/W1D3_GeneralizedLinearModels/student/W1D3_Tutorial1.html)

- scikit-learn user guide: [PCA](https://scikit-learn.org/stable/modules/decomposition.html#pca), [Poisson regression](https://scikit-learn.org/stable/modules/linear_model.html#generalized-linear-models) 

- Cunningham & Yu (2014) [Dimensionality reduction for large-scale neural recordings](https://doi.org/10.1038/nn.3776). *Nature Neuroscience*

² [BrainGlobe](https://brainglobe.info/documentation/brainglobe-atlasapi/index.html) is a useful package for plotting data in 2D and 3D renderings of the brain, with brain structure labels. Its Allen atlas uses the same axis order as the CCF columns (AP, DV, ML in µm). To use the package, install with `pip install brainglobe-atlasapi` and download the 25um allen mouse atlas. See this tutorial on how to use the [Allen Mouse Atlas](https://allenswdb.github.io/background/CCF.html?highlight=brainglobe+atlas)
