# 2 Anatomy of an Audio Plugin

specs for AAX, AU and VST3 are fundamentally the same
implement the same sets of atrtibutes and behaviours

plugins are ecapsulated in C++ objects, derived from classes provided byt the manufacturers

Plugin host instantiates the plugin object and receives a base class pointer to the newly created object.

Host may only call functions defined in the API, and the plugin is required to implement those functions correctly to be a valid plugin

if required functions are missing or incorrect, the plugin will fail

this forms a contract between the manufacturer and the plugin developer

Each plugin API is package in an SDK
None of the APIs come with precompiled library to link against so all the code is there 

## 2.1 Plugin packaging: DLLs

all plugins are package as DLLs/share library
-> a precompiled lib that the exec links with at runtime rather than at compile time
-> perfect to extend the functionality of a host without the host knowing anything about the component when the host is compiled

when host is started, it tries to load all the plugin DLLs in the path

validation phase
-> retrieving info such as plugin name and may include 
-> basic or even rigorous testing

host will likely crash the host if there is a runtime bug in the laoding code

--> validate plugins prior to running them in the host
AU and VST provide mechanisms to do this


Usuallt the hosts unload all the plugins after the validation phase
then waits for user to load plugin 
--> loading phase

when the plugin is loaded, the DLL is pulled into the binarys process address space, or less commonly from another binary's address space.
-> when most of the plugin description occurs
-> description = first aspect of plugin design you need to understand

after loading phase, processing phase
-> infinite loop on the host
-> host sends audio to plugin, alg. processes it and sends altered data back to host

eventually the plugin will be terminated
--> unloading phase
--> destroy allocated ressources and free up memory
potential place for crashes, so be sure to test repeated loading and unloading

## 2.2 Plugin Description: Simple strings

basic description with strings and flags

each API has a slightly different mechanism for plugin to let host know info about it

e.g.
- plugin name
- plugin type
- plugin vendor
- vendor contact

some FourCC are needed for AU AAX VST3
- product code: unique for each plugin sold by company
- vendor code

AAX requires AAX plugin ID
VST3 requires 128bit GUID
== globally unique identifier
aka universally blabla == UUID
for steinberg, FUID

### Features and options

- needs sidechain input y/n
- creates delay and needs to inform host
- creates reverb after playback stopped
- has custom GUI it wants to display
- wants to show a ProTools gain reduction meeter (AAX)
- plugin factory resets

lastly somme MacOS specific codes: bundle IDs

## 2.3 Init.: Defining the plugin parameter interface

almost all plugins requires some kind of GUI

each control on the GUI connected to a plugin _parameter_

the plugin must declare and describe the parameters to the host during the loading phase

three fundamental reasons that these parameters need to be exposed:
1. if automation, host needs to know how to alter the parameters
2. all APIs allow the plugin to declare "I dont have a GUI" -> default UI from DAW
3. parameters states must be saved when DAW session is saved

e.g. VBT plugin with parameters:
- volume
- treble boost/cut (dB)
- bass boost/cut (dB)
- channel IO: L/R/stereo

plugin must declare those during loading phase with one parameter per GUI control

without exception, these parameters are implemented as C++ objects
all parameters -> list of C++ objects

actual location of this list depends on API but generally stored in the plubin bass class object, with DAW having mechanism to get and set them

we will discuss the mechanisms that the host uses to update the parameters in a thread-safe way in the API programming guide chapters

2 types of parameters:
continuous vs string-list

continuous = float, double or int
strinh-list = choose from options

parameters attributes:
- name ("Volume")
- units ("dB")
- wether parameter is numerical or list of string, and the strings themselves 
- param min and max
- default value for new instance
- control taper information (log/lin)
- other API specific info
- auxiliary info

### Defining Channel I/O support

some combination of input and output channels
 most commonly:
- mono in, mono out
- mono in, stereo out
- stereo in, stereo out

but could be designed for a very specific channel processing, eg 7.1 DTS vs 7.1 Sony

others are designed to fold down into mono or stereo, e.g. 5.1 surround to stereo fold-down

each API has different ways of specifying channel IO support, during description or init or both

### Sample Rate dependency

almost all of the plugins are sensitive to DAW sample rate but many plugins will support just about any sample rate

a few plugins may be able to process only signals with certain sample rates

In all APIs, there is at least one mech. tht allows the plugin to obtain the current sample rate for use in its calculations

## 2.4 Processing: Preparing for Audio Streaming

each session requires preparation
usually involves resetting the algorithm, clearing old data out of memory structures, resetting buffer indexes ,pointers etc

Each API includes a function that is called prior to audio streaming

called `reset()` in ASPiK, `repareToPlay()` in JUCE, etc

one thing to note: trying to use transport information from the host is usually going to be problematic, unless the plugin specifically requires information about the process
In addition, some hosts are notoriously incorrect when reporting transport states (Logic) and may report audio streaming when it's not

### DSP

fx plugins process audio information, transforming it in some way

audio comes either from audi ofiles loaded into session, or real time audio streaming into the audio adaptor

almost always as a float between -1.0 and +1.0

AU AAX and RAFX use float data, while VST3 allows dloat or double

we will encode all of our C++ to operate iternally with double data types
