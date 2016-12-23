# NIST Statistical Test Suite

This project is a considerably improved version of the NIST Statistical Test Suite (**STS**), a collection of tests used in the 
 evaluation of the randomness of bitstreams of data.

## Purpose

STS can be useful in:

- Evaluating the randomness of bitstreams produced by hardware and software key generators for cryptographic applications.
- Evaluating the quality of pseudo random number generators used in simulation and modeling applications.
- Testing the computational capabilities of hardware (the suite might take a lot of time and resources to run).

## Usage
   
### Requirements

STS version 3.0.0 requires the external library [fftw3][fftw] to be installed in your system.
This library is also available to install in most of the package managers with the name _fftw3_.

If you are not able to install fftw3 in your system, but you still want to use STS, you can compile
the program with the command `make legacy` instead of `make`. This command will make STS use another 
algorithm to compute the discrete fourier transform, which is slower but does not require external libraries.

### Get data to test

As mentioned above, STS has been developed with the goal of testing cryptographic pseudo-random data. Therefore,
 if you want to use STS, we expect that you have some data generated by a pseudo-random number generator with you.

We also plan to upload online nine 8GB files of data generated by the built-in PRNGs provided by NIST, for everyone to test.
 The link to download them will be posted here once available.

As an additional note, you can use STS to test [other types of data][xkcd] of data as well.

### How to run

Depending on the size of the data you want to test, and on the time you have, there are two parameters you have to choose:

    1) Number of iterations (we suggest: 0 < number < 8560). 
       The results will be more accurate when this parameter is bigger. 
       However, the execution time will also be bigger.
       
    2) Length of a single bitstream  (must be a multiple of 8, we suggest: 2^20 = 1048576)

The data will be processed in _number of iterations (1)_ chunks of the specified _length of a single bitstream (2)_.
Therefore, it's required that the size of the data you use as input is at least:

    [(number of iterations) * (length of a single bitstream) / 8] BYTES

Once you have chosen the data to test and the two parameters and cloned the repository, `cd` into the sts folder, run `make` and
 then you can use the program as follows (this is a simple example):
       
```sh       
$ ./sts -v 1 -b -g 0 -i 32 -I 1 -w . -f /path/to/random/data 1048576
```
Here is the meaning of the flags used in this command:

```
[-v 1] indicates that we want a verbosity level of 1 for the printed output
[-b] indicates that we want to run sts in batch mode (without human intervention)
[-g 0] indicates that we want to test data from a file
[-i 32] indicates that we want to run 32 iterations (choose bigger numbers when possible)
[-I 1] indicates that we want the program to tell us every time it finishes testing 1 bitstream
[-w .] indicates the path of the folder where you want to store the testing results
[-f /path/to/random/data] indicates the path of the data we want to use as input
[1048576] indicates that our preferred length of a single bitstream is 1048576
```

Now be patient: depending on the hardware used and on the parameters specified, the suite might take minutes, hours or days 
 to finish the test.

After the run is done a report will be generated in a file called `result.txt`.

__NB__: When `make legacy` is used, the compiled program to execute will be called `sts_legacy_fft` 
 instead of `sts`.

__NB__: For more advanced usage run `./sts -h`

## Project structure

The STS version 3.0.0 comes with three folders:

- *src*: contains the source code of the suite
- *docs*: contains the papers explaining the underlying mathematical theory of the suite
- *tools*: contains some tools we used during the improvement of the suite

## Improvements

Our major improvement starts from source code of the [original source code of version 2.1.2][site] and consists in several
 bug fixes, code improvements and added documentation. Here are some of our improvements of the 3.0.0 version:

- Added an option to run tests in batch mode (executed without human intervention)
- Fixed the implementation of the tests where bugs or inconsistencies with the paper were found
- Improved the performance (execution time is now about 50% shorter)
- Eliminated the use of the global variables in the tests (now a struct state is instead passed around as pointer)
- Eliminated the use of files to pass data during the execution of the suite (now memory is used instead)
- Introduced a "driver-like" function interface to test functions (instead of one monolithic function for each test)
- Added minimum test conditions checks (now a test is disabled when its minimum requirements are not satisfied)
- Improved the memory allocation patterns of each test
- Eliminated the use of "magic numbers" (numeric values that were used without explanation)
- Minimized the use of fixed size arrays, to prevent errors
- Added checks for errors on return from system functions
- Added debugging, notice, warning and fatal error messages
- Fixed the use of uninitialized values (some issues were caught with valgrind)
- Improved the precision of test constants (by using tools such as Mathematica and Calc)
- Improved the variable names when they were confusing or had a name different than the one in the paper
- Improved command line usage by adding new flags (including a -h for usage help)
- Improved the coding style to be more consistent and easier to understand 
- Added extensive and detailed source code documentation
- Improved the support for 64-bit processors
- Improved the Makefile to use best practices and to be more portable
- Improved the program output and the contents of the file with the final results
- Fixed the warnings reported by compilers
- Fixed memory leaks

## Coming soon

- Graphical visualization of the final p-values in a gnu-plot
- Parallel run in multiple cores and hosts
- Revised version of the STS paper with fixes
- New (non-approximate) entropy test

## Contributors

The people who have so far contributed to this major improvement of the NIST STS are:

- Landon Curt Noll ([lcn2](https://github.com/lcn2))
- Riccardo Paccagnella ([ricpacca](https://github.com/ricpacca))
- Tom Gilgan ([tgilgs](https://github.com/tgilgs))

You can contact us using the following email address: *sts-contributors (at) external (dot) cisco (dot) com*

By no means we believe we have fixed every last bug in this code. Moreover we do not doubt that we have introduced
 new bugs during our transformation. For any such new bugs, we apologize and welcome contributions that fix either old bugs
 or our newly introduced ones. Improvements to the documentation are also a very appreciated contribution.

Please feel invited to contribute by creating a pull request to submit the code you would like to be included. 

## License

The original software was developed at the National Institute of Standards and Technology by employees of the Federal Government 
 in the course of their official duties. Pursuant to title 17 Section 105 of the United States Code this software is not subject
 to copyright protection and is in the public domain. Furthermore, Cisco's contribution is also placed in the public domain.
 The NIST Statistical Test Suite is an experimental system. Neither NIST nor Cisco assume any responsibility whatsoever for
 its use by other parties, and makes no guarantees, expressed or implied, about its quality, reliability, or any other 
 characteristic. We would appreciate acknowledgment if the software is used.
 
## Special thanks

Special thanks go to the original code developers. Without their efforts this modified code would not have been possible.
 The original NIST document, [SP800-22Rev1a][paper], was extremely valuable to us.

The above partial list of issues is presented to help explain why we extensively modified their original code.
 Our bug fixes are an expression of gratitude for their efforts.
 
We also thank the original authors for making their code freely available. We saw the value of their efforts
 and set about the tasks of extending their code to situations they did not intend.


   [site]: <http://csrc.nist.gov/groups/ST/toolkit/rng/documentation_software.html>
   [paper]: <http://csrc.nist.gov/groups/ST/toolkit/rng/documents/SP800-22rev1a.pdf>
   [xkcd]: <https://hardmath123.github.io/xkcd-random.html>
   [fftw]: <http://www.fftw.org>
