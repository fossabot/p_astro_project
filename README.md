[![CI](https://github.com/jhyland01/p_astro_project/actions/workflows/main.yaml/badge.svg)](https://github.com/jhyland01/p_astro_project/actions/workflows/main.yaml)

# P_astro Project

Exploring the effect of using the probability of astrophysical origin as a thresholding statistic on the expected rate of detections and their purity.

## Installation instructions

This only needs installed if you wish to run tests, the .py files can be run without any installation.
To install, clone the project and navigate to the root of the project directory.
Run "pip3 install ." .

## Main Features

The scripts in this package are built to select a set of hyperposterior samples from the GWTC-3 data release and Gibbs sample posterior samples from this set of hyperposteriors.
The bilby package is then used to generate signal-to-noise ratios (SNRs) by injecting these event signals into a stationary noise background.
Using a simple parameterisation from a paper by Lynch et al. (citation needed), these SNRs are converted into False-alarm-rates (FARs).
To calculate the probability of astrophysical origin (P_astro) we need the true astrophysical event rate.
This is obtained by integrating over mass and spacetime volume from the power-law plus peak model fitted to GWTC-3 data (figure 10 of citation needed).

## Basic Usage [Contribution guidelines for this project](analysis/README.md)

This notebook will run through the steps to run the analysis for the $P_\text{astro}$ project.
More detailed notes are in `long_notes.ipynb`.

We want to run the following from the command line (unix):
* Download the GWTC-3 population data by running [bash download_data.sh](analysis/bash download_data.sh).
* Create the igwn environment by running `pip3 install -r requirements.txt`.
* Install the P_astro_project by navigating to the root of the P_astro directory and running `pip3 install -e .` -e means development package so you can make changes.
* Select a set of hyperposteriors, these are the parameters which describe the population models. 
This is achieved with the `select_hyper` function in [select_hyperposterior.py](analysis/select_hyperposterior.py) and defining how many sets of hyperparameters you want to select. 
This is run automatically within the [gibbs_sample.py](analysis/gibbs_sample.py) script which allows you to define the number of hyperparameters sets as an input arguement `nsets`.
* Sample from these sets of hyperposteriors to obtain posterior samples (mass, spin, redshift).
There is a dependency $m_1 > m_2$ and we sample mass and mass ratio so the values the mass ratio can take are dependent on what $m_1$ has been sampled as, we therefore use Gibbs sampling to account for this interdependence.
For each set of hyperposteriors, 1000 posterior samples are obtained, this can be changed withing the [gibbs_sample.py](analysis/gibbs_sample.py) script if necessary.
As an example, run `python3 gibbs_sample.py 30` to Gibbs sample from 30 sets of hyperposteriors.
The output will be saved as `params_for_SNR.csv` in the ouputs folder.
* Next we need to generate some signal-to-noise ratios (SNRs) and some false alarm rates (FARs).
To do this we run the [generate_SNRs_FARs.py](analysis/gibbs_sample.py) script which uses `bilby`.
This reads in the `params_for_SNR.csv` and uses the posterior samples to generate waveforms.
It currently uses the `IMRPhenomPv2` waveform approximant, this can be updated.
The FARs are approximated using a parameterisation presented in the Lynch et. al. paper.
This will output the `params_inc_FAR.csv` file.
* Following this we calculate the true alarm rates (TARs) and $P_\text{astro}$, underway as the [event_rate.ipynb](analysis/event_rate.ipynb) file.

Aside:
* The [create_database.py](analysis/create_database.py) script turns the `params_inc_FAR.csv` for searching/manipulating as an SQLite3 database if you are so inclined.

## Dependencies

See [requirements.txt](requirements.txt) for a full list of environment packages.
This does not include the packages required for using the mpl_utils.py which originates from the GWTC-3 data release and required packages in the LVK igwn environments.

## Future work

Predicted detection rates for future detectors (Advanced LIGO +/#, Einstein Telescope etc.)

## Contact information

Email: j.hyland.2@research.gla.ac.uk

## Citations

1. GWTC-3 catalog
2. GWTC-3 Population paper
3. Lynch et al.

to be updated