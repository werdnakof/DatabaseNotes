# Data Types
**Types**
numeric, character, temporal, sptatial, image, text, audio, video

**Operations**
Comparison, arithemic, fuzzy searches, retrieve all documnets that contain a given term

**_Context Specific_**
data operations have to be meaningful i.e. context specific
data orderings/comparisons are also context based

## Temporal data Properties
**_Types_**: linear, possible futures, branching time, directed acyclic graph, periodic/cyclic

**_Boundedness of time_**: _start-end_ vs. _unbounded_

**_Density / Granularity_**
- _sequences_ vs _intervals_ vs _fixed periods_
- _Chronons_: fixed periods in a timeline
- _Granularity_: depending on chronon defined

We may wish to record:
-  _Valid Time of a fact_: when the fact is true in reality
-  _Transaction Time of a fact_ – when the fact is current in the database, and can be retrieved
- Both (bitemporal)

**_Operations_**
- WHEN clause in SQL to retrieve by timestamps
- TIME-SLICE: to specify a time domain
- BEFORE/AFTER, FOLLOWS/PRECEDES, DURING, ADJACENT, OVERLAPS are comparison operators

## Spatial data
- **_Types_**: points, regions (boxes, polynomial, polygons), vectors
- **_Operations_**: length, intersects, contains, overlaps, centre
- **_Characteristics_**: highly complex, large volume, query via specialised graphical front ends or special operators

## Multimedia data
- **_markup languages_** e.g. html
- **_XML_** (structured data)
- **_CLOBs (character large objects, large text documents)_** allowing text search
- **_BLOBs (binary large obejcts)_**
- **_QBIC (Query by Image Content)_**
- **_Digitised sounds_**: WAV, mp3 formats, compression techniques used
- **_MIDI (Musical Instrument Digital Interface)_**: Consists of a sequence of instructions (Note_On, Note_Off, Increase_Volume), Interpreted by a synthesiser
- **_Video_**: space hungry (sequency of frames of megabytes), To integrate video and audio, interleaved file structures incorporate times sequencing of audio/video playback
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTM0OTE3MzY1LDE0ODIyMjQyNTQsLTEyNj
AyMDkyNzRdfQ==
-->