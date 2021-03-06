# CORSIKA wrapper 

[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

[![Build Status](https://travis-ci.org/fact-project/corsika_wrapper.svg?branch=master)](https://travis-ci.org/fact-project/corsika_wrapper)

A wrapper for the [CORSIKA](https://www.ikp.kit.edu/corsika/) cosmic ray air shower simulation by the [Karlsruhe Institute for Technology](https://www.kit.edu/)

The CORSIKA simulation is certainly one of the most advanced simulation tools in modern astroparticle physics, and even beyond its practicle use, you have to admire the sheer efficiency and speed of the fortran77 based CORSIKA. However, over the years multithread machines showed up, and todays students might not be used anymore to the user interfaces of the time when CORSIKA was created. This wrapper makes the CORSIKA call easier, more comfortable, and thread safe. It allows you to:

- Call multiple CORSIKA instances in parallel (thread safe)
- Call CORSIKA from anywhere, systemwide on your machine
- Specify your output path in the CORSIKA call on the command line [optional `-o`]
- Have write access to your CORSIKA output files
- Store the CORSKA stdout and stderror next to your output [optional `-s`]

## Install
```bash
pip install git+https://github.com/fact-project/corsika_wrapper
```

## How to use
### Once
tell the corsika wrapper which CORISKA executable you want to run, using the `-c` option.
```bash
user@machine:~$ corsika -c /home/user/corsika/corsika-74005/run/corsika74005Linux_QGSII_urqmd
```

### Call CORISKA
like a modern program and specify (if you want) your output path on the command line.
```bash
user@machine:~$ corsika -i /home/user/reaserch/my_steering_card.txt -o /home/user/results/my_run.evtio
```
## Python API
Use the corsika wrapper API directly in python to script your corsika calls.

```python
In [1]: import corsika_wrapper as cw
In [2]: return_value = cw.corsika(
                        steering_card=cw.read_steering_card('/home/user/reaserch/my_steering_card.txt'), 
                        output_path='/home/user/results/my_run.evtio', 
                        save_stdout=True)
```

## How it is done
1. The CORSIKA wrapper creates a temporary directory. 
2. All CORSIKA dependencies in CORSIKA's 'run' directory are copied into the temporary working directory. 
3. CORSIKA is called in the temporary working directory and gets the steering card piped in via stdin. If an output path is specified on the command line `[-o]`, the output path in the steering card piped into CORSIKA is overwritten. 
4. When CORSIKA is done, the temporary working directory is removed. 
5. Finally the output file's write protection is removed. The CORSIKA wrapper returns the CORSIKA return value. 

Optionally `[-s]`, CORSIKA's stdout and stderr can be written into textfiles next to the output.

## Why
Calling CORSIKA is special. CORSIKA demands stdin, it can only be called in a certain 'run' directory environment, and it must not be called in parallel within the same working directory because CORSIKA is writing to the fiels in its run directory while running. (private communication with Konrad Bernloeh and Dieter Heck).
Further, the output path can not be specified on the command line and the output files are write protected.
