# 1 Intro

affordagle digital audio since mid 80s (CD)
DSP maths been around since 60s

digital sampling = aquisition of data points from a continuous analog signal
sampled at a regular interval = sample period/interval
-> sampling frequency $f_s$

e.g CD 44.1 kHz == 22.7 microseconds

Virtual Studio Technology spec

in this book, both DSP theory and C++ software applications

access to dozens of C++ objects that implement every dsp alg in the book
ch9-22 reveal conversion algo->C++
these objects not tied to any format/framework

## 1.1 Using this book

## 1.2 Fundamentals of Audio Signal Processing

Analog audio -> LPF -> ADC -> Encoder -> Digital audio

Sampling theorem:

> A continuous analog signal can be sampled into disrete data points and then reconstructed into the original analog signal without any loss of information-including inter-sample fluctuations- if and only if the input signal has been band-limited so that it contains no frequencies higher than half the sample rate, aaka the Nyquist frequency/rate.

> Band-limiting the input signal prior to sampling is known as adhering to the Nyquist criteria

violating the nyquist criteria will cause aliasing, frequencies higher than nyquist will fold back into the spectrum

## 1.3 Reconstruction of the Analog Signal

Digital audio -> Decoder -> DAC -> LPF -> analog

output filter = reconstruction filter
-> is responsible for recreating the inter-sample fluctuations
-> works bc LPFing the impulses turns them into sinc() which then overlap

## 1.4 Numerical Representation of Audio Data

unipolar vs bipolar
integer vs fixed point vs floating point

problem: always an even number of quantization levels
in bipolar systems, more negs than pos
-> causes problem with phase inversion
-> limit the negs to -min

audio data in hardware and wav often int
but float prefered for DSP
-> most audio file decoders can deal with fixed and floating point and will produce floating point data in [-1.0f, 1.0f]

floats are more resilient to overflow

## 1.5 Analytical DSP Test signals

fundamental digital signals
- DC and step
- Nyquist
- 1/2 Nyquist
- 1/4 Nyquist
- impulse

the first four give us a ballpark idea of the frequency response of some basic DSP filters

-> DC and Step (0 Hz)

{0, ..., 0, 1, ..., 1}
step portion and DC portion

step portion tells the transient attack time
DC portion gives response at 0 Hz

-> Nyquist

{..., +1, -1, +1, -1, ...}

highest frequency that can be encoded

-> 1/2 Nyquist
{..., -1, 0, 1, 0, -1, ...}

-> 1/4 Nyquist
{... 0.0, 0.707, +1.0, 0.707, 0.0, −0.707, −1.0, −0.707, 0.0 ...}

-> Impulse

single sample with value 1.0 with a bunch of 0.0s
Impluse Response of a DSP algorithm fully characterizes the behavior of the system
-> we can genereate the frequency and phase response from the IR

## 1.6 Signal Processing Algorithms

for real-time processing, the alg. must accept a new input sample, do the processing and have the output sample available before the next input arrives
synthesizing a waveform to output also qualifies but there is no rt audio input to process

terms:
- x(n) always the input sequence
- y(n) always the output sequence
- h(n) the impulse response
