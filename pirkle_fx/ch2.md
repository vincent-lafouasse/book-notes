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
