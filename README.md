# Mathematical library for calculating the signal spectrum
The main functionality is the calculation of raw spectrum values and the calculation of EEG spectrum values.
Working with the library is possible in iterative mode (adding new data to the internal buffer, calculating spectrum values) and in one-time spectrum calculation mode for a given array. When working in the iterative mode, the spectrum is calculated with the frequency set during initialization.
## Initialization
### Main parameters
1. Raw signal sampling frequency. Need to be >= 1.
2. Spectrum calculation frequency. Need to be <= 16 kHz.
3. Spectrum calculation window length. Need to be <= signal sampling frequency.
### Optional parameters
1. Upper bound of frequencies for spectrum calculation. Default value is sampling_rate / 2.
2. Normalization of the EEG spectrum by the width of the wavebands. Disabled by default.
3. Weight coefficients for alpha, beta, theta, gamma, delta waves. By default has 1.0 value.
## Creation
##### Java
```java
int samplingRate = 250; // raw signal sampling frequency
int fftWindow = 1000; // spectrum calculation window length
int processWinRate = 5; // spectrum calculation frequency
SpectrumMath math = new SpectrumMath(samplingRate, fftWindow, processWinRate);
```
## Optional initialization
1. Additional spectrum settings:
##### Java
```java
int bordFrequency = 50; // upper bound of frequencies for spectrum calculation
boolean normalizeSpectByBandwidth = true; // normalization of the EEG spectrum by the width of the wavebands
math.initParams(bordFrequency, normalizeSpectByBandwidth);
```
2. Waves coefficients:
##### Java
```java
double alphaCoef = 1.0;
double betaCoef  = 1.0;
double deltaCoef = 0.0;
double gammaCoef = 0.0;
double thetaCoef = 1.0;
math.setWavesCoeffs(deltaCoef, thetaCoef, alphaCoef, betaCoef, gammaCoef);
```
3. Setting the smoothing of the spectrum calculation by Henning (by default) or Hemming window:
##### Java
```java
math.setHanningWinSpect(); // by Hanning (by default)

math.setHammingWinSpect(); // by Hamming
```
## Initializing a data array for transfer to the library
Array of double values with length less or equals then 15 * saignal sampling frequency.
## Types
#### RawSpectrumData
Structure containing the raw spectrum values (with boundary frequency taken into library).

Fields:
- all_bins_nums - Integer value. Number of FFT bars. Contained only in the C++ interface.
- all_bins_values - Double array. Raw FFT bars values.
- total_raw_pow - Double value. Total raw spectrum power.

#### WavesSpectrumData
Structure containing the waves values.

Absolute frequency values (double type):
- delta_raw
- theta_raw
- alpha_raw
- beta_raw
- gamma_raw

Relative (percent) values (double type):
- delta_rel
- theta_rel
- alpha_rel
- beta_rel
- gamma_rel

## FFT band resolution
The library automatically matches the optimal buffer length (degree 2) to calculate the FFT during initialization, depending on the specified window length. Receiving resolution for the FFT bands (number of FFT bands per 1 Hz):
##### Java
```java
double numberBinsFor1Hz = math.getFFTBinsFor1Hz();
```
## Spectrum calculation in iterative mode
1. Adding and process data:
##### Java
```java
double[] samples = new double[SIGNAL_SAMPLES_COUNT];
math.pushData(data);
```
2. Getting the results:
##### Java
```java
RawSpectrumData[] rawSpectrumData = math.readRawSpectrumInfoArr();
WavesSpectrumData[] wavesSpectrumData = math.readWavesSpectrumInfoArr();
```
4. Updating the number of new samples. Is necessary for correct calculation of elements in the array of obtained structures, if a large portion of data is added to the library all at once.
##### Java
```java
math.setNewSampleSize();
```

## Spectrum calculation for a single array
1. Compute spectrum:
##### Java
```java
double[] samples = new double[SIGNAL_SAMPLES_COUNT];
math.computeSpectrum(samples);
```
2. Getting the results:
##### Java
```java
RawSpectrumData rawSpectrumData = math.readRawSpectrumInfo();
WavesSpectrumData wavesSpectrumData = math.readWavesSpectrumInfo();
```
## Finishing work with the library
##### Java
```java
math.clearData();
```