#### This file contains documentation for each notebook contained in this set of VAST pulsar variability analysis notebooks. Each notebook is labeled in the order in which they should be run using the hexadecimal system.

### get_all_pulsar_measurements.ipynb

This notebook queries ATNF for a set of pulsars with their properties, filters this set of pulsars to those contained in the VASTGalactic footprint, and saves this set to a csv. The notebook then queries the VAST observations to find detections of these pulsars, and saves these observations to another csv.

###### PRECONDITION: access to the VAST pipeline on this machine

###### IN: nothing

###### OUT: 'all_pulsar_measurements.csv', 'paper_dfv0.csv'


### make_raw_stats.ipynb

This notebook is for generating stats (such as chi squared and modulation index) for a set of measurements. An example set of measurements might be a dataframe of pulsar measurements from VAST. This notebook should be run on raw pulsar measurements.

###### IN: 'all_pulsar_measurements.csv', 'paper_dfv0.csv'

###### OUT: 'paper_dfv1.csv'


### get_candidate_controls.ipynb

This notebook contains a script to query the VAST pipeline to get all point like sources within a degree of each pulsar. We use these as candidates to be control sources, which will later be used to make corrected fluxes for each pulsar.

###### IN: 'paper_dfv2.csv'

###### OUT: 'all_candidate_controls.csv'


### cut_controls.ipynb

This notebook takes a csv of all point like sources around each pulsar (which are our "candidate control sources") and slices it into a separate dataframe for the candidate control sources of each pulsar. It then saves each dataframe to the same folder (in this case 'cand_controls_bypsr'. This noteboook should be run after 'get_candidate_controls'.

###### PRECONDITION: empty folder 'candidate_controls_by_pulsar' must exist

###### IN: 'all_candidate_controls.csv'

###### OUT: fills folder called 'candidate_controls_by_pulsar' with csvs of the candidate controls associated with each pulsar


### pulsarbypulsar_truncation.ipynb

This notebook takes a folder which contains a set of csv files which are a list of candidate control sources for each pulsar, and removes duplicates while also removing sources that are too faint to be viable controls. After doing this, it knits together the separate dataframes for each pulsar in to one dataframe, which it then saves as a csv. In this case, that csv is 'candidate_controls_truncated'. This notebook should be run after 'cut_controls'.

###### IN: 'candidate_controls_by_pulsar' folder filled with csv files named a pulsar Jname where each row is a detection of a new control source, 'paper_dfv2.csv'

###### OUT: 'candidate_controls_truncated.csv'


### get_all_control_measurements.ipynb

Script to get all measurements of all control sources given a csv of control source detections, one per control source. In this notebook, that csv is 'all_final_controls_truncated.csv'. This notebook should be run after pulsarbypulsar_truncation.

N.B. This notebook queries the VASTGalactic survey once for each candidate control source associated with a pulsar. This leads to a very large number of queries done in series. As such, this notebook can take an extremely long time (~hours) to run, and will save a csv of the measurements of candidate controls associated with each single pulsar to a 'controls_w_meas' folder.

This runtime problem may be fixed by changing the query from a search using coordinates to a search using source names, but I have not been able to explore this possibility.

###### PRECONDITION: must create an empty folder named 'controls_w_meas'

###### IN: 'candidate_controls_truncated.csv', 'paper_dfv2.csv'

###### OUT: 'controls_measurements.csv'


### get_control_stats.ipynb

This notebook contains code to compute a chi squared for each of the candidate control sources associated with each pulsar. This allows us to find the least variable candidate in the set of potential controls associated with each pulsar. This will streamline the process for generating corrected fluxes, and will ensure that lower-variability control sources are used correct pulsar fluxes when possible. This notebook should be run after 'get_all_control_measurements.csv'.

###### IN: 'controls_measurements.csv', 'paper_dfv2.csv'

###### OUT: 'controls_measurements_with_stats.csv' which is a version of 'controls_measurements.csv' with the computed statistics inserted


### make_corrected_data.ipynb

This notebook contains code to create a csv of all pulsar measurements with corrected fluxes instead of the raw fluxes detected by ASKAP. It should be run after 'get_all_control_measurements.ipynb'

###### IN: 'all_candidate_control_measurements.csv', 'all_pulsar_measurements.csv', 'paper_dfv2.csv'

###### OUT: 'all_pulsar_measurements_corrected.csv'


### make_corrected_stats.ipynb

This notebook is for generating stats (such as chi squared and modulation index) for a set of measurements. An example set of measurements might be a dataframe of pulsar measurements from VAST. This notebook should be run on corrected pulsar measurements.

###### IN: 'all_pulsar_measurements_corrected.csv'

###### OUT: a version of 'paper_df.csv' with corrected statistics, named 'paper_dfv2.csv'


### get_scintillation_data.ipynb

The below cell is a command that uses the RISS19 model to add expected scintillation data to your csv of pulsar properties. This will require that your machine has a copy of the RISS19 source files saved to the same directory as this set of notebooks and data. The RISS19 package can be found at https://github.com/PaulHancock/RISS19. This notebook should be run after 'make_corrected_stats.ipynb'.

###### PRECONDITION: access to the RISS19 package in the working directory

###### IN: 'paper_dfv2.csv'

###### OUT 'paper_dfv3.csv'


### make_raw_acf.ipynb

This notebook contians code to generate and save plots of autocorrelation functions for the lightcurves of each pulsar. It will also generate an approximate decorrelation timescale based on the lag at which the ACF drops below 0.5. The notebook finds this value using a quadratic fit, and these timescales will be saved in units of years. These metrics will be appended to a new version of a 'paper_df' csv. This notebook should be run after 'get_scintillation_data.ipynb'.

NB: This version of the notebook is meant to be run on uncorrected pulsar fluxes. Another version of this notebook ('make_corrected_acf.ipynb') exists for corrected data, and will save all plots to a different folder.

NB: This notebook currently does not determine errors associated with the polynomial fit. It should be edited to include these errors.

###### PRECONDITION: a folder named 'ACF_raw' must have been created

###### PRECONDITION: the astroML package must be installed

###### IN: 'all_pulsar_measurements.csv', 'paper_dfv3.csv'

###### OUT: 'paper_dfv4.csv'


### make_corrected_acf.ipynb

This notebook contians code to generate and save plots of autocorrelation functions for the lightcurves of each pulsar. It will also generate an approximate decorrelation timescale based on the lag at which the ACF drops below 0.5. The notebook finds this value using a quadratic fit, and these timescales will be saved in units of years. These metrics will be appended to a new version of a 'paper_df' csv. This notebook should be run after 'get_scintillation_data.ipynb'.

NB: This version of the notebook is meant to be run on corrected pulsar fluxes. Another version of this notebook ('make_raw_acf.ipynb') exists for raw data, and will save all plots to a different folder.

NB: This notebook currently does not determine errors associated with the polynomial fit. It should be edited to include these errors.

###### PRECONDITION: a folder named 'ACF_corrected' must have been created

###### PRECONDITION: the astroML package must be installed

###### IN: 'all_pulsar_measurements_corrected.csv', 'paper_dfv4.csv'

###### OUT: 'paper_dfv5.csv'


### make_lightcurves.ipynb

This notebook contains code for making separate lightcurves for the raw and corrected fluxes for each pulsar and saving them to separate folders. It should be run after 'make_corrected_acf.ipynb'.

###### PRECONDITION: Two folders, one named 'lightcurves_raw' and one named 'lightcurves_corrected' must exist in the working directory

###### IN: 'all_pulsar_measurements.csv', 'all_pulsar_measurements_corrected.csv', 'paper_dfv5.csv'

###### OUT: a set of png lightcurves filling each of the above named folders


### make_comparison_plots.ipynb

This notebook contains code to generate and save plots comparing quantities predicted by the RISS19 model to quantities determined by our analysis using observations from VAST. It will save these plots to a folder named 'comparison_plots', which must exist prior to running this notebook. It additionally contains functions for creating generic linear- and log-axis scatter plots with on-axis histograms. It should be run after all other notebooks in this set, as it requires data from both 'make_acf' notebooks and the RISS19 model.

###### PRECONDITION: a folder named 'comparison_plots' must exist in this directory.

###### IN: 'paper_dfv5.csv'

###### OUT: eight plots comparing RISS19 predictions to our computed quantities, which will be found in the aforementioned 'comparison_plots' folder
