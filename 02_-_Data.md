# Data tTypes
**Types**
numeric, character, temporal, sptatial, image, text, audio, video

**Operations**
Comparison, arithemic, fuzzy searches, retrieve all documnets that contain a given term

**_Context Specific_**
data operations have to be meaningful i.e. context specific
data orderings/comparisons are also context based

## Temporal data Properties
**_Characteristics_**
linear, branching time, directed acyclic, periodic

**_Boundedness of time_**
_Time starts and ends_ vs. _unbounded_

**_Density / Granularity_**
- _sequences_ vs _intervals_ vs _fixed periods_
- _Chronons_: fixed periods in a timeline
- _Granularity_: depending on chronon defined

We may wish to record:
-  _Valid Time of a fact_: when the fact is true in reality
-  _Transaction Time of a fact_ – when the fact is current in the database, and can be retrieved
- Both (bitemporal)

## Spatial data
- Types: points, regions (boxes, polynomial, polygons), vectors
- Operations: length, intersects, contains, overlaps, centre
- Characteristics: highly complex, large volume, query via specialised graphical front ends or special operators

# Multimedia data
- markup languages e.g. html
- XML (structured data)
- CLOBs (character large objects, large text documents) allowing text search
- BLOBs (binary large obejcts), QBIC (Query by Image Content)
- Digitised sounds: WAV, mp3 formats, compression techniques used
- MIDI (Musical Instrument Digital Interface): Consists of a sequence of instructions (Note_On, Note_Off, Increase_Volume), Interpreted by a synthesiser
- Video: space hungry (sequency of frames of megabytes), To integrate video and audio, interleaved file structures incorporate times sequencing of audio/video playback
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM4Nzc0MzU1NywtMTI2MDIwOTI3NF19
-->