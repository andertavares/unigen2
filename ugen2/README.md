Installing UniGen2
------------------

UniGen2 must be built using GCC 4.8 or higher, and requires libz. You will also need "build-essential", i.e. automake, autoconf and libtool. It has been tested on 64-bit Linux, and should work on similar systems which support the POSIX Realtime Extension (notably, Mac OS X is not one of these).

To build UniGen2, issue the following commands from THIS directory:

```
make -f Makefile.cvs
mkdir build
cd build
../configure
make
./unigen -h
```

As described below, we provide the wrapper script 'UniGen2.py' in the directory above this one as an easy way to invoke UniGen2. This script can use the exact model counter sharpSAT to perform the preprocessing step of UniGen2, which is faster in some cases than UniGen2's internal model counter ApproxMC. To enable this functionality, provide binaries of the tools 'doalarm' and 'sharpSAT' in the script's directory (we have provided sources for them as part of this distribution). UniGen2.py will then try sharpSAT for 5 minutes before falling back on ApproxMC. If either binary is not present, ApproxMC will be used automatically.


Running UniGen2
---------------

You can run UniGen2 using the 'UniGen2.py' Python script in the directory above this one.
While UniGen2 will work without doalarm and sharpSAT scripts, we typically observe performance improvements using the two binaries, so we advise you to compile 
sharpSAT and doalarm. The source code is provided as is and the instructions are available in their corresponding README files
For example, the command

python UniGen2.py formula.cnf out

runs UniGen2 on the DIMACS CNF file 'formula.cnf', writing the generated samples to a file in the folder 'out'.
There are many command-line options that can be passed to UniGen2.py. For example,

python UniGen2.py -threads=1 -samples=100 formula.cnf out

will use 1 thread to generate 100 samples (actually slightly more, because UniGen2 generates 11 samples per sampling step with the default parameters and so will round up to the nearest multiple of 11). Running UniGen2.py with the argument '-h' will print a usage message explaining all of its command-line options.

For every generated solution, the output file will contain a line looking like this:

v-10 13 -15 -16 -25 28 39 41 -43 -45 5 -53 6 -69 -78 9 93 0:1

Between the 'v' and the '0' is standard DIMACS notation for expressing the literals made true by the solution. After the ':' is a number saying how many times this solution was generated. Note that the solution does not assign values to every variable. This is because 'formula.cnf' contained the following lines in its header:

c ind 10 13 15 16 25 28 39 41 43 45 0
c ind 5 53 6 69 78 9 93 0

These tell UniGen2 that the variables listed are an "independent support" of the formula, whose values determine the values of all other variables in any satisfying assignment. If you know an independent support for your formula which is smaller than the set of all variables (e.g. the set of primary input variables for a circuit encoded into CNF using the Tseitin transformation), specifying it like this will make UniGen2 faster (see our DAC-14 paper for details). Note that each "c ind" line can list only 10 variables at most, followed by a closing '0'.
