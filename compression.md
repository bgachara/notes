# Data Compression

ref:`data compression complete reference`
    `managing compression`
    `introduction of data compression`

## Introduction

- This is the process of converting an input data stream (source / original raw data) into another data stream (output / bitstream / compressed) that has a smaller size.
- a stream is either a file or buffer in memory
- Data compression aims to counter
  - inevatability of storage overflow
  - eliminate wait time of data transfers
- Also known as source coding.
- source can either be memoryless or it can have memory.
- All data compression techniques are based on the same principle, i.e remove redundancy form original data.
- Any non-random data has some structure that can be exploited to achieve a smaller representation of the data, where no structure is discernable.
- General law
  - assing short codes to common events and long codes to rare events
- The redundancies in data depend on the type of data(text, images, sound ) which is why a new compression method has to be developed for a specific type of data and it performs best on this type of data.
- No such thing as a universal, efficient data compression algorithm.
- What is the SP theory?(simplicity and power)
- Broad categories of data compression
  - Run Length Encoding
  - Statistical methods
  - Dictionary-based methods
  - Transforms
  
