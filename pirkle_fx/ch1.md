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

### 1 Bookkeeping

- n = current sample
- n-1 = previous sample
- n+1 = next sample
- on next sample interval, everything is shuffled and referenced to new current

e.g. x(n+1) = next input sample relative to current

### 2 The One-Sample delay

analog circuits (like tone controls) use capacitors andinductors to alter phase and delay of signal

digital algorithms use time delay instead

represented with a box with z^int
neg for backward, pos for forward

z = the delay operator

nb: positive delay impossible for realtime processing, only for offline processing

Delay rules:
- each time a sample goes into the delay register, the previous stored sample is ejected
- the ejected sample can be used or deleted
- delay elemts can be cascaded together

### 3 Mult. with a scalar value

elementwise multiplication

represented by a triangle with the scalar inside or on top

### 4 Addition and subtraction

sigma symbol in circle
`-` next to branch being subtracted

### 5 Some alg. examples and Difference Equations

Difference Equation = mathematical equation that relates y(n) to x(n)
digital equivalent of the differential equation

e.g.
y(n) = a.x(n-3) - b.x(n-64)

nb: coefficient multiplier handles the 3 basic AP functions of
- gain (coeff > 1.0)
- attenation (coef in [0, 1])
- phase inversion (coeff < 0)

e.g. N-channel mixing incorporate attenuators with value 1/N to avoid overflow

## 1.7 1st order feed-forward and feedback algorithms

2 common structures: feedforward and feedback
realtes to the signal flow in the structures
contains only one delay register, one summer and two coefficient scalar multipliers

Order of the structure = number of delay elements

### Feedforward
y(n) = a0 x(n) + a1 x(n-1)

### Feedback
y(n) = a0 x(n) - b1 y(n-1)

