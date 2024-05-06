## 1 Intro

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

### 1.1 Using this book

### 1.2 Fundamentals of Audio Signal Processing

Sampling theorem:

> A continuous analog signal can be sampled into disrete data points and then reconstructed into the original analog signal without any loss of information-including inter-sample fluctuations- if and only if the input signal has been band-limited so that it contains no frequencies higher than half the sample rate, aaka the Nyquist frequency/rate.

> Band-limiting the input signal prior to sampling is known as adhering to the Nyquist criteria
