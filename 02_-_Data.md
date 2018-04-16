Data types
e.g. numeric, character, temporal, sptatial, image, text, audio, video

Data Operations
E.g. comparison, arithemic, fuzzy searches, retrieve all documnets 
that contain a given term

data operations have to be meaningful i.e. context specific
data orderings/comparisons are also context based

Temporal data
# Time characteristics:
- linear, branching time, directed acyclic, periodic
# Boundedness of time:
- time starts and ends, unbounded
# Density
- sequences/intervals/fixed periods
- chronons: fixed periods in a timeline
- Granularity: depending on chronon defined

We may wish to record
– The Valid Time of a fact – when the fact is true in reality
– The Transaction Time of a fact – when the fact is current in
the database, and can be retrieved
– Both of these (bitemporal)


# Spatial data
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
- Video: space hungry (sequency of frames of megabytes), To integrate video and audio, interleaved file
structures incorporate times sequencing of audio/video playback
