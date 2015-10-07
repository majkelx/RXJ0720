Before you start, you need to set up your envirnoment. Read the previous
set of instructions, and use the command "load_hea" as before.

Copy the following subdirectory to your home directory:

> cp -r ~mariano/HEA/exercise5 ~/HEA/

Go to the directory of the exercise:

> cd ~/HEA/exercise5

> ls 

There is a file called "rates.ds". This is a light curve of a source.
The format of this file is FITS. That is a standard format used to
transport data (images, tables, like spectra or light curves) between
computers and avoiding compatibility problems, while saving space. You
can see the file with a command like "more" or "less", but since this is
not an ASCII file, you will not necessarilly see it right:

> less rates.ds

or 

> more rates.ds

The best way to see the file is to use the program "fv":

> fv rates.ds

This opens a graphical interface. The big window shows the structure of
the file. In this case, it has 10 block units (0 to 9), each containing
different information. In FITS, block 0 is always reserved for images.
Since this is not an image, that block is in this case empty (Dimension
is 0).

You can click on the "Header" of block 0 to see the header of that
block, if you want. It contains information about the file, the
observation date and time, name of the source, etc. You can close that
window with the header information when you are done looking at it. (By
the way, the spectra and response files of the previous exercises are
also FITS files, and you can also look at them with fv.)

Block 1 contains the light curve. It has 3 columns and 43998 rows. The
columns are TIME, RATE, ERROR, and each row is one point of the light
curve. (NB: A light curve gives the number of photons within a time
interval and the error in that number as a function of time.) Look at
the header of block 1. It contains information of what is stored in the
data part of that block. Have a look and close it when you're done. 

To see the data themselves click on "All" in block 1. You can see the
light curve. What is the time resolution of this light curve? And what
are the errors? (I mean, how do you think the errors were calculated?)
Close the window of the light curve when you are done looking.

The other blocks contain other (not so important at this time)
information. The block called REG00103 contains information about the
size and shape of the region in the sky used to extract the light
curve. You can also look at the header and the data (All) as usual.

The blocks with names STDGTI01 to 07 contain the so-called "Good Time
Intervals" (GTI for short) of each of the 7 CCDs of the detector used to
take this observation. The GTI indicate the time intervals in which the
data are "good", e.g., since the detectors were working at nominal
conditions, or data were not lost in the transmission to Earth,
etc....). Have a look at them if you want, but they are not really
important at this time.

Quit fv (use Quit in the main window). We will now plot the light curve.
The command is:

> fplot rates.ds

and I reproduce the questions/answers part of the program:

Name of X Axis Parameter[error][] time
Name of Y Axis Parameter[error] up to 8 allowed[] rate
Lists of rows[] -
Device: /XWindow, /XTerm, /TK, /PS, etc[] /xs
Any legal PLT command[] 
PLT>

When you give the answers above, you should have a light curve plotted
on the screen, rate vs. time. (Notice that the last answer is left
blank.)

To exit the plotting program, use:

PLT> quit

(the abbreviation q is enough)

You can also plot the errors in the y axis (notice that the program
now offers you the answers you used in the last run as default. Just
press enter to use that answer):

> fplot rates.ds
Name of X Axis Parameter[error][time] 
Name of Y Axis Parameter[error] up to 8 allowed[rate] rate[error]
Lists of rows[-] 
Device: /XWindow, /XTerm, /TK, /PS, etc[/xs] 
Any legal PLT command[ ] 
PLT> 

To plot the errors as error bars, that variable must be between "[]".
Notice the difference when you answer as follows:

PLT> q

> fplot rates.ds
Name of X Axis Parameter[error][time] 
Name of Y Axis Parameter[error] up to 8 allowed[rate[error]] rate,error
Lists of rows[-] 
Device: /XWindow, /XTerm, /TK, /PS, etc[/xs] 
Any legal PLT command[ ] 
PLT> 

The labels in the x axis are not nice. try this:


PLT> q

> fplot rates.ds offset=yes
Name of X Axis Parameter[error][time] 
Name of Y Axis Parameter[error] up to 8 allowed[rate,error] rate[error]
Lists of rows[-] 
Device: /XWindow, /XTerm, /TK, /PS, etc[/xs] 
Any legal PLT command[ ] 
PLT> 

As I said above, the default answer to the questions is the one that you
gave the last time. If you press Enter, it will take that as the answer
(you do not need to type the same answer every time).


Now the x axis starts at 0, since the first time was subtracted from
all time (and the offset is indicated above of the plot).

PLT is a plotting program (actually, the same used by Xspec). You can do
many things within it. Write help to see all the options:

PLT> help

To exit the help, just keep pressing Enter until you get the PLT prompt
again. Once you find a command you want to use, you can get help by
typing:

PLT> help help

for instance to get help about help.

Let's now rescale the x axis to see only the first 1000 s of the light
curve:

PLT> r x 0 1000

(r is the abbreviation of rescale)

or the first 100 s:

PLT> r x 0 100

Or connect the points with a line:

PLT> li on

Or make it a step (histogram) line:

PLT> li st

and rescale back to the full x scale.

PLT> r

Or not plot the errors:

PLT> err off

and rescale back to 0 100 to see the effect better:

PLT> r x 0 100

Or get back the errors plotted:

PLT> err on


You can play a lot more by putting other markers, or changing the axis
labels, or the fonts, etc, and quit when you are done:

PLT> q

Look the help for all the options to beautify your plot.

Question: If I show you this curve, can you figure out that this source
is periodic by looking in as many ways as you want?

I suppose the answer is no, but try...

POWER SPECTRUM:

We will search this light curve for periodicities. First, a couple of
concepts (read the chapter on Fourier Transforms in the book Numerical
Recipes, avialble in PDF on the web; it is very instructive):

If you have a light curve of total duration T seconds and a time
resoltuion of t seconds, the Fourier Transform (FT) of this light curve
will go from a minimum frequency 1/T Hz to a maximum frequency (also
called the Nyquist frequency) of 1/2t Hz in steps (frequency resolution)
of 1/T Hz.

The FT of the light curve consists of a set of complex numbers, one real
and one imaginary number for each frequency. (The lilght curve is a set
of real numbers, a real number at each time.) You can calculate the
Power Density Spectrum (PDS) of this light curve, which is defined as
the modulus squared of the FT at each frequency.

A periodic signal will have peaks in the PDS at the characteristic
frequency (=1/Period) of the light curve, and depending on the shape of
the periodicty in the light curve, at (some of) the harmonics of that
frequency. If the periodicty is sinusoidal (either a single sine or
cosine function), then you'll get a peak only at the frequency of that
sinusoidal signal. If the shape is not sinusoidal, you'll get other
harmonics.

Let's calculate the PDS of this light curve. We will use the program
"powspec".

A few conventions before we use the program:

The input (Ser. 1 filename below), is the light curve.

A window file is a file that (optionally) defines one or more time
intervals of the light curve that we want to analyse, for instance if we
know that there is an interval in which the data is wrong, or the source
is doing something strange and we want to filter that part out. If you
give "-" as an answer it will take the full light curve (no windows
used).

Minimum Newbin Time is the (original) time resolution of your light
curve. The program will determine this by itself, but you can chek
whether it is correct.

Newbin Time is the time resolution that you want to use for the
analysis. You can never use a time resolution that is smaller than the
original one (read the warning before the relevant question). That is
obvious: If your light curve has a time resolultion of 1 s, you cannot
analyse your light curve at a resolution of 0.5 s.

But you may want to analyse your light curve at a worse time resolution
than is available. For instance, if the available time resolution is 1
s, you can choose to analyse the data at 2 s time resolution, or 3 s
time resolution, etc.... Notice that you can only analyse your data at
an integer multiple of the available time resolution. If the available
time resolution is 1 s, you cannot choose to analyse the light curve at
1.5 s. 

Number of Newbins/Interval: When you do the FT of a light curve, you
have to decide how long is the time interval of the light curve you will
use to do the FT. You may choose to do one single FT of the the full
light curve, or make one FFT for an interval of duration T1, then for
another of the same duration T1, then another of the same duration T1,
etc. This question in the program asks how long should the interval be
in units of your Newbin Time. So, if your light curve has a resolution
of 1 s, and you choose to use that same resolution for the analysis, you
chose 1 for Newbin Time, and if here you choose 8192, you will do a FT
every 8192 s.

If the time resolultion of the light curve is 1 s, and you choose to
analyse it at 2 s time resolution (you chose 2 for Newbin Time), then if
you here choose 8192 you will do a FT every 16384 s (2 x 8192).

Suppose you use the 1-s resolution and choose 8192 here; if your light
curve is shorter than 8192 s, the program will likely fill the light
curve with the average value (this is called padding) until it makes it
to 8192 (I am not sure about this, but I presume this is what the
program does.) If your lighht curve is longer than 8192 s, it will first
do an FT of the first 8192 s, then try and do an FFT of the following
8192 s, etc, until the full light curve is transformed. (See below for
the output in cases when, like in this case, you make more than one FT
per light curve.) If the last interval is to short (less than 8192 s in
this case), it will padd the end of it with the average.

The FT is usually calculated using the Fast Fourier Transform algorithm
that, as the names suggests, calculates it much faster than the standard
methods. the drawback in this case is that the number of points in the
llight curve that one has to transform has to be a power of 2. That is
the reason that in my previous examples I always used 8192.

If you make an FT for the first T1 s, then another for the following T1
s, etc., you have to decide what to do with the individual FT. The
option Number of Intervals/Frame is for this. If you say "1", it means
that at the end the program will display 1 FT at a time, for as many
intervals of duration T1 as there are in the light curve.

If you give the number offered by the program by default (INDEF the
first time you run it), it will take all the FTs (one per interval) and
average them to make one single FT for the whole light curve.

CHOICES, CHOICES, CHOICES....

There are science reasons to make different choices of these parameters.
For instance, if you look for a very narrow signal in your PDS due to a
pulsar, you want to have the finest possible frequency resolution in
your PDS. The best possible frequency resolution is obtained when you
make the longest possible FT (see definitions above). 

But sometimes you are not interested in the finest frequency resolution
(because your feature is not extremely narrow). Then you can choose a
shorter time interval in your data to do the FTs, and at the end average
the individual FTs. Your frequency resoltuion will not be that fine now,
but by the average process you reduce the errors of the powers at each
frequency by a factor ~sqrt(N), where N is the number of FTs that you
calculated.

You can also increase the signal to noise ratio of the PDS if at the end
you take several contiguous frequency bins and average the powers in
them. The errors decrease in this case by a factor sqrt(M), where M is
the number of bins that you averaged (we call this rebinning in
frequency). Of course, you again loose frequency resolution when you do
this (since M frequency bins become 1, you loose a factor M in frequency
resolution).

With teh above knowledge, now try to use the program powspec to
calculate the PDS of the light curve rates.ds. Here is an example of
running the program(in my case I have spome default answers already
cause I ran the program before):

> powspec
 
powspec 1.0 (xronos5.22)
 
Ser. 1 filename +options (or @file of filenames +options)[rates.ds] rates.ds

... (I skip the output of the program. It is informative, so have a look
to that at least once when you do it yourself):

Name of the window file ('-' for default window)[-] -

Read the output...

Newbin Time or negative rebinning[1] 1

Here I chose to use the original time resolution for my analysis. See
the output.

Number of Newbins/Interval[1024] 65536

I chose 65536 s (that is 65536 Newbin x 1 s) per interval. This is
longer than the duration of the light curve (see in the output what is
that); this number is a power of 2. 

Number of Intervals/Frame[1] 1

Since from my previous choices I will make 1 (and only 1) FT of the full
light curve, I cannot combine more than 1 interval.

Rebin results? (>1 const rebin, <-1 geom. rebin, 0 none)[0] 0

I do not want to rebin in frequencies at the end (I do not want to
degrade the frequency resolution, although this means I'll pay the price
that my errors will be bigger).


Name of output file[power_spectrum] power_spectrum

Any name is fine.


Do you want to plot your results?[yes] yes


Enter PGPLOT device[/xs] /xs

and at the end you are left in the plotting program:


PLT> 

You know how to play a bit here. Do that! Check what is the minimum and
maximum frequency. What is the (approximate) frequency resolution? Does
this match your expectations? 


Try another run, changing some things and see what happens. E.g. (but
try as many as you want; make sure you understand what you get and why):

(I skip all the answers that do not change from the previous run.
Remember to use a different name for the output file, or you will
overwrite the one from the previous run.)

Newbin Time or negative rebinning[1] 2
 
What will be the effect of the above answer? Do you see the change in
the plot when you finish?

Number of Newbins/Interval[65536] 8192

What will this do?

Number of Intervals/Frame[1] INDEF

(INDEF is the way to use the answer offered by the program), which is
indicated just before the question. It is not the same as the last
answer, which is in between [] here).


Rebin results? (>1 const rebin, <-1 geom. rebin, 0 none)[0] 



etc....


Do the same as above, but now answer "1" to the question:

Number of Intervals/Frame[3] 1

What happens?

Repeat all, and now go back to the maximum possible time resolution:

Newbin Time or negative rebinning[2] 1

Make FT of shorter time intervals:

Number of Newbins/Interval[8192] 1024

Plot the PDS one by one (do not average):

Number of Intervals/Frame[1] 

You will have to see 43 PDS (each time you quit PLT, you will get a new
one. Just keep entering "q" till the end. If you get fed up, just use
Ctrl C to quit).

You can plot again your PDS each time (provided that you saved it in a
file). First check what is saved in the FITS file. You can use "fv" as
before, or if you want simply:

> flcol power_spectrum.fps

(notice that the program added the extension .fps to the file).


___Column_Names_________Formats______Dims______Units___
FREQUENCY                  D                   Hz
XAX_E                      D
POWER                      E
ERROR                      E
NUM_POINT                  E
 

Then use fplot as (notice the different input from the example of a
light curve, and also that I include errors both in x and y):

> fplot power_spectrum.fps

Name of X Axis Parameter[error][time] frequency[xax_e]
Name of Y Axis Parameter[error] up to 8 allowed[rate[error]] power[error]
Lists of rows[-] 
Device: /XWindow, /XTerm, /TK, /PS, etc[/xs] 
Any legal PLT command[ ] 
PLT> 

If you are plotting the PDS of the last example, the one with 43 FTs,
then you can choose which of the 43 you want to plot:



> fplot power_spectrum.fps+41

etc... 

where the number at the end indicates which one you want to plot (and is
the same as the block number in the FITS file; check with fv).

LET'S REFINE THE PERIOD:

If you think you've found a period in the signal (I tell you; the period
is about 8.39 s), we can get a better estimate using a different
techinque, called epoch folding. I will explain it on the board, cause
it is to complex to write here simply.

The program is called "efsearch". It takes a light curve as input, and
produces a plot of the chi^2 of a fit with a constant to the folded
light curve for each trial period... ufffff! Basic Chinese I
suppose... I will explain how it works on the board...

Here is an example of this program running:

> efsearch 
 
efsearch 1.1 (xronos5.22)
 
Ser. 1 filename +options (or @file of filenames +options)[rates.ds] rates.ds

Have a look at the output (at least once) to learn a bit what is
being read in.

Name of the window file ('-' for default window)[-] -

Some more informational output...

Epoch[0.1314700000E+05] INDEF

INDEF takes the value offered by the program. This is the time zero
used to calculate trhe phases (see my explanation on the board).

 Period format is seconds.
Period[] 8.3

I will search around 8.3 s period.

Phasebins/Period {value or neg. power of 2}[] 20

I will divide the folded light curve into 20 intervals between 0 and
1.

Number of Newbins/Interval[] INDEF

I take the proposed number here. It will take the full light curve as
one single interval.

Resolution for period search {value or neg. power of 2}[] .00001

This is the step (in seconds) I will use to search periods around 8.3 s. 

Number of periods to search[] 50000

I want ot test 50000 periods around 8.3 with a step of 0.00001 s
(previous question).

Write the output to a file:

Name of output file[] filename

I choose to plot the result onto the screen, /xs.

This takes a while (about 2-3 minutes....)

Look at te plot. Note down the most likely period.

We now want to see the folded light curve. Use "efold" for that:

Here is a run of that program (only some answers shown. The rest
should be obvious):

Number of time series for this task[1] 1

(only one light curve as input).

Ser. 1 filename +options (or @file of filenames +options)[] rates.ds

Epoch[ 0.1039200000E+05] INDEF

(this is the zero time for the folding)

Period[] 8.39114
Phasebins/Period {value or neg. power of 2}[20]20

Number of Newbins/Interval[] INDEF

Just one single interval for the whole light curve

...


Try other value for this question:

Phasebins/Period {value or neg. power of 2}[20] 100

or 10, or 8... JUst try. Notice that changing this number means some
oter values may need to be changed.

Next, try to fold on the wrong period. Try for instance 8.2 s (and go
back to 20 Phasebins/Period).

Now plot the light curve of the same observation extracted with 10 s
time resolution (In my case, since I also plotted a PDS, the default
answers are not good, and need to change them):

> fplot rates_10.ds offset=yes
Name of X Axis Parameter[error][frequency[xax_e]] time
Name of Y Axis Parameter[error] up to 8 allowed[power[error]] rate[error]
Lists of rows[-] 
Device: /XWindow, /XTerm, /TK, /PS, etc[/xs] 
Any legal PLT command[ ] 

Hummm.... Something wrong happens at the end of the observation ....

Plot the light urve with 100s time resolution:

> fplot rates_100.ds offset=yes
Name of X Axis Parameter[error][time] 
Name of Y Axis Parameter[error] up to 8 allowed[rate[error]] 
Lists of rows[-] 
Device: /XWindow, /XTerm, /TK, /PS, etc[/xs] 
Any legal PLT command[ ] 


Now it is clear that something wrong happens at the end of the
observation.... We probably need to ignore that part.

Things will get messy... but is how science is....

Plot the 10-s reslution light curve, and find the time where the data
starts to be bad. Use the offset=yes option in fplot, and change the x
scale until you identify the time where the data starts to be bad.

I find that the time is 201635340.There is also a small drop out at
about 8000-9000 s. The count rate drops below 1 count/s, and then to
0. Let's filter that out. There is a command that will do that

>ftcopy 'rates_100.ds[(TIME < 201635340) && (RATE > 1)]' outfile_100.ds

do 

>fhelp ftcopy 

to see how it works. The syntax should make it more or less clear: I
filter times less than 201635340 and rate (counts/s) larger than 1.
The input is rates_100.ds and the output outfile_100.ds.

Plot now outfile_100.ds

fplot outfile_100.ds offset=yes


























