# RainDisaggGAN

RainDisaggGAN is a method that uses Generative Adverserial Networks (GANs) for temporal downsampling of spatial precipitation patterns. It learns to
generate possible hourly patterns of precipitation, given the daily sum. It is trained on hourly precipitation radar
data from SMHI.

The method is described in our paper (INSERT LINK) by Sebastian Scher and Stefanie Peßenteiner


This repository contains the trained network, an example script on how to use it for temporaral disaggregation,
and plots of samples generated by the method. Additionally, it contains all software to do the training on your own.

The pretrained model can be loaded with [raindisagg_gan_pretrained.py](raindisagg_gan_pretrained.py)
The example script is [example.py](example.py)

Usage:
```python
import numpy as np
from raindisagg_gan_pretrained import generate_scenarios, plot_scenarios

ndomain = 16 #the domain used in the traing of the GAN. must be the same here
# create made-up input conditions (including empty last channel dimension) with 10mm/day at every gridpoint
# in a real application, here you would use your own data  (in mm/h).
cond1 = 10 * np.ones((ndomain, ndomain, 1))
# generate subdaily scenarios
n_scenarios = 10
scenarios1 = generate_scenarios(cond1, n_scenarios)
# plot the results
fig = plot_scenarios(scenarios1)

```
prerequesites:
tensorflow >=2.1 (pip install tensorflow)



Plots with SMHI radar data (the same dataset as used for trainind) are in the folder
 [plots_generated_wgancp_pixelnorm](plots_generated_wgancp_pixelnorm).
the files `generated_precip.....png` and `generated_fractions.....png` show examples of different daily scenarios
generated for the same daily sum. (like
figure 2 in the paper). the former shows absolute precipitation, the latter shows fractions of the daily sum.


In order to fully replicate the study, you need to use the following scripts:

[download_smhi_radardata.py](download_smhi_radardata.py) downloads the raw radar data in geotiff format

[convert_smhi_radardata.py](convert_smhi_radardata.py) converts the raw radardate to daily netcdf files, including conversion
from radar reflectivities to precipitation

[reformat_data.py](reformat_data.py) reads in the netcdf data, aggregates it to desired aggregation (default 1h), and
outputs it in a single .npy file in a format suitable for machine learning

[compute_valid_indices.py](compute_valid_indices.py) reads in the data from reformat_data.py and calculates all possible
indices that have (1) no missing data an (2) pass a selection criterion for enough rainfall.
it outputs a .pkl file with the indices

[gan_train_cwgangp_pixelnorm.py](gan_train_cwgangp_pixelnorm.py)  trains the GAN, and makes some intermediate plots

[generate_and_evaluate.py](generate_and_evaluate.py) evaluates the GAN and makes analysis plots

The GAN is built and trained with Tensorflow 2.1.
The software was developed on Linux and to some amount relies on UNIX-like system commands (directories are
created with the `mkdir - p` command).
Small adaptions might be necessary to run it on a windows computer. The example script 
[generate_and_evaluate.py](generate_and_evaluate.py) does not use UNIX-system commands and should run on any system.
Training the GAN is very slow without a GPU, but using the trained generator is computationally inexpensive and can 
easily be done on CPUs (even on a laptop)

[pr-disagg-env.yml](pr-disagg-env.yml) contains the anaconda environment used for training. If you use anaconda,
 you can install the same anaconda environment via `conda env create -f pr-disagg-env.yml`. 

[trained_models](trained_models) contains the trained generator and discriminator.

Note that all datapaths in the scripts (except in the example scripts) need to be adapted to your local system.
Also the SLURM commands on top of the files need to be adapted if you use SLURM. If you don't use SLURM, you can simply ignore them.







