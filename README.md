# Wavelines
A program designed to detect seismic activity within seismograms and extract event details, significantly reducing data size for efficient transmission.


# Seismic Event Detection Using Wavelet Analysis

This program analyses raw seismic velocity time-series data using discrete wavelet transforms to detect, localise, and extract seismic events. Instead of relying on fixed band-pass filters, the signal is examined across multiple wavelet scales, allowing seismic activity to be identified based on its multi-scale energy structure. The output is a cleaned, time-bounded representation of the detected event suitable for further scientific analysis or data reduction.

The seismic data used in this analysis is sourced from NASA’s Open Data Portal, specifically from the Mars' planetary seismology datasets.

Prerequisites

The script requires a Python environment with the following modules installed:

	•	pandas for data handling
	•	numpy for numerical operations
	•	matplotlib for visualisation
	•	pywt (PyWavelets) for wavelet transforms
	•	scipy for signal processing utilities

Dependencies can be installed using:

	•	pip install pandas numpy matplotlib pywavelets scipy

The input data must be provided as a CSV file containing seismic velocity measurements with absolute timestamps, relative time in seconds, and velocity in metres per second.

How to run the script

	1.	Launch Jupyter Lab in the directory containing the notebook and the seismic data file.
	2.	Confirm that the input CSV file path in the notebook matches the location of the dataset.
	3.	Execute the notebook cells sequentially. The workflow includes:
   
	•	visualisation of the raw seismic signal
	•	wavelet decomposition of the velocity time series
	•	storage and inspection of wavelet coefficients
	•	multi-scale coefficient recombination and smoothing
	•	detection of seismic event boundaries
	•	isolation and export of the detected event

The notebook generates multiple plots and saves processed outputs to CSV files for downstream use. All parameters, including wavelet type, decomposition level, smoothing width, and detection thresholds, can be adjusted directly within the notebook.


# Detailed science of this approach:

The analysis begins by loading seismic velocity data and plotting the raw signal as a function of relative time. This step is intentionally retained as part of the scientific workflow, as it provides insight into noise characteristics, signal amplitude, and potential transient behaviour before any transformation is applied.

A discrete wavelet transform is then applied to the velocity signal using a Daubechies-4 wavelet. The signal is decomposed up to level 6, producing a set of wavelet coefficients that represent the signal’s behaviour across progressively coarser time-frequency scales. Unlike Fourier methods, this representation preserves temporal localisation, which is critical for identifying transient seismic events.

All wavelet coefficients are stored by decomposition level in a single data structure alongside the original absolute and relative time columns. Because wavelet coefficient arrays differ in length between levels, padding is applied to maintain dimensional consistency when exporting to CSV. This intermediate representation allows the wavelet-domain signal to be inspected, reused, or analysed independently of the raw time series.

The analysis then focuses on higher-level wavelet coefficients, specifically levels 4, 5, and 6. These scales tend to capture the dominant seismic motion while suppressing higher-frequency noise and instrumental artefacts. Coefficients from these levels are combined to form a single composite signal that reflects seismic energy across relevant scales.

To improve stability and reduce spurious fluctuations, the combined wavelet signal is smoothed using a Gaussian filter. Event detection is performed on this smoothed signal rather than on the raw data. The onset of a seismic event is identified by searching for statistically significant peaks that exceed a conservative threshold defined relative to the signal’s mean and standard deviation. This approach reduces false positives and ensures that detected events correspond to sustained energy increases rather than isolated noise spikes.

The end of the seismic event is determined by scanning backwards through the signal and identifying the point at which the amplitude returns to a stable baseline. Both the start and end points are visualised on the time series, allowing manual validation and parameter tuning where necessary.

Once the event boundaries are identified, the signal outside this window is removed, leaving only the portion corresponding to the seismic event itself. The resulting waveform is significantly cleaner and smaller than the original dataset and is saved to a separate CSV file for further analysis, classification, or transmission.

Overall, this method treats wavelet coefficients as a physically meaningful representation of seismic activity rather than a preprocessing step; Furthermore, it is devoid of any machine learning techniques that require more processing power than required. The workflow emphasises interpretability, scale awareness, and manual validation, making it suitable for research-grade seismic analysis using data from NASA’s open planetary datasets.
