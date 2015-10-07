You have to produce the light curve of the source to search for the
period. For that, we first need to process the raw event list sent down
to Earth from the satellite and make a so-called filtered event list. 

Start the environment to be able to run the XMM-Newton Software, SAS, to
produce the filtered event list:

> source ~/HEA/load_sas

I noticed that SAS and Xspec do not get along well. You may want to work
on different screens with one and the other. So in one screen use
"source ~/HEA/load_hea" and in the other screen "source ~/HEA/loas_sas".

***MKA:
> source ~/HEA/load_hea
> source ~/HEA/load_sas

There are 5 observations available (if you work fast, we can download
more later). The data are here:

/net/dataserver1/data/users/mariano/RXJ0720/

and the observations have ID numbers:

> ls /net/dataserver1/data/users/mariano/RXJ0720/
0124100101/  0132520301/  0158360201/  0161960201/  0670700301/

You don't need to copy these directories (they are large). You can
analyse them from where they are. You need only keep the files that you
produce out of the data in your own directories.

I suggest that you make separate directories for each observation in
your own directory to keep things neat. Go to whatever is going to be
your working directory, and create the directories (you can use other
naming convention if you want):

> mkdir ./RXJ0720

This will be the main directory for your work. Next:

> cd RXJ0720
> mkdir 0124100101
> mkdir 0132520301  
> mkdir 0158360201  
> mkdir 0161960201  
> mkdir 0670700301

Let's start with the first observations (the others should be similar;
I only explain one):

> cd 0124100101

The satellite has several instruments. For the timing analysis we will
use the MOS CCD which has the appropriate time resolution. Let's make a
subdirectory here for that instrument:

> mkdir MOS
> cd MOS

We will now do the standard processing to get the filtered event list,
mand later one produce the light curve(s). We need to tell the program
which observation we want to process, and which calibration files we
will use. I already produced a file for you (to simplify your life)
containing the list of calibration files  needed. The file is called
ccf.cif (it is a FITS file). We will indicate to the program that we
want to us that file as follows:

> setenv SAS_CCF /net/dataserver1/data/users/mariano/RXJ0720/0124100101/ccf.cif

For the other observations you will have to use later the ccf.cif that
corresponds to each of those observations.

Next, we indicate to the program which observation we want to process:

> setenv SAS_ODF /net/dataserver1/data/users/mariano/RXJ0720/0124100101/ODF

We now run the program to do the processing:

> emproc

This may take 15-30 minutes, depending on the observation. This command
produces many warnings, but hopefully no error. If you want to save the
warnings to review them later, do this instead:

> emproc > & emproc.log

and the warnings will be saved into the file emproc.log. As long as you
only get warnings, everything should be fine. Errors are not fine, and if
that happens we would need to see what is going on.

When this finishes, you'll have these files in the directory:

> ls
0078_0124100101_AttHk.ds                    0078_0124100101_EMOS2_S002_01_Badpixels.ds
0078_0124100101_EMOS1_S001_01_Badpixels.ds  0078_0124100101_EMOS2_S002_02_Badpixels.ds
0078_0124100101_EMOS1_S001_02_Badpixels.ds  0078_0124100101_EMOS2_S002_03_Badpixels.ds
0078_0124100101_EMOS1_S001_03_Badpixels.ds  0078_0124100101_EMOS2_S002_04_Badpixels.ds
0078_0124100101_EMOS1_S001_04_Badpixels.ds  0078_0124100101_EMOS2_S002_05_Badpixels.ds
0078_0124100101_EMOS1_S001_05_Badpixels.ds  0078_0124100101_EMOS2_S002_06_Badpixels.ds
0078_0124100101_EMOS1_S001_06_Badpixels.ds  0078_0124100101_EMOS2_S002_07_Badpixels.ds
0078_0124100101_EMOS1_S001_07_Badpixels.ds  0078_0124100101_EMOS2_S002_ImagingEvts.ds
0078_0124100101_EMOS1_S001_ImagingEvts.ds

(the first part of the names will be different in other observations).

There are 2 MOS CCD cameras in the satellite. You will be interested in
any of these two files:

0078_0124100101_EMOS2_S002_ImagingEvts.ds
0078_0124100101_EMOS1_S001_ImagingEvts.ds

The part EMOS1 and EMOS1 indicates to which of the two CCD cameras
corresponds the file. In other observations there may be more than one
of these files per camera.

We need to know which one we will use. We want the one in which the
central CCD of the instrument is in Small Window Mode. This is a mode in
which only part of the central CCD 9containing the source) is read.
Since a smaller area is used, the read-out of the CCD is faster (what we
need for this). There are many ways to find out. For instance, extract
images from the two event files in sky coordinates:

> evselect table=0078_0124100101_EMOS1_S001_ImagingEvts.ds imagebinning=binSize imageset=MOS1image.img withimageset=yes xcolumn=X ycolumn=Y ximagebinsize=80 yimagebinsize=80

> evselect table=0078_0124100101_EMOS2_S002_ImagingEvts.ds imagebinning=binSize imageset=MOS2image.img withimageset=yes xcolumn=X ycolumn=Y ximagebinsize=80 yimagebinsize=80

The commands should be more or less clear. They select events from the
event file, and projects them onto an X-Y matrix with pixels that are
rebinned by a factor 80 both in X and Y.

And now we'll display the images (one at a time):

> ds9 MOS1image.img 

> ds9 MOS2image.img

You can play with contrast and brightness clicking with the middle or
right mouse buttons on the screen and moving around. You can zoom in or
out, change the colour used to display the image, etc. Try changing the
Scale to Log (using the menu on the top). This program is fun to play
with.

If everything is okay, the image should look like the one on the bottom
left of Figure 24 here:

http://xmm.esac.esa.int/external/xmm_user_support/documentation/uhb/epicmode.html#3633

There you see that full field observed by the 7 CCDs, with only a small
part of the central one being used. Compare with the case in the Figure
of that link when the full central CCD is used (upper left panel of Fig.
24).

In this observation, the correct file is:

0078_0124100101_EMOS2_S002_ImagingEvts.ds

The other should look like the one of the upper left panel of Fig. 24.

Next, you have to barycenter the times of each photon. This is needed
cause the period that you measure will slightly change depending on the
relative motion of the Earth and the satellite with respect to the
source (Doppler shifts). If you want to compare periods between
observations taken at different epochs, hence different relative motions
between the satellite/Earth and the source, you must first convert the
time of arrival of each photon to the inertial frame of reference of the
barycenter of the solar system. That is what we will do below.

> cp 0078_0124100101_EMOS2_S002_ImagingEvts.ds MOS2_evt_barcen.fits

We first copy the file, and we operate on the copied version (in case
we make mistakes, we can always start all over from the original):

> barycen table=MOS2_evt_barcen.fits:EVENTS

The new file will have barycentered times now.

Next, we'll make a light curve with 1-s time resolution of the full
field (you can find out what is the intrinsic time resolution of the
file using fv on the barycentered event file, in the headers). We could
choose energies, or a region in the sky containing only the source, but
I believe this is not necessary if we are only interested in the period.
We will select only from CCDNR1 (the central one), PATTERN 0 to 12 (to
avoid pile up as much as possible), and use a keyword #XMMEA_EM which
contains a set of logical operations to select only good events (those
not close to CCD borders, bad pixels, etc.).

> evselect table=MOS2_evt_barcen.fits energycolumn=PI expression='CCDNR==1 && #XMMEA_EM && (PATTERN<=12)' withrateset=yes rateset="MOS2_source_lightcurve_1s.lc" timebinsize=1 maketimecolumn=yes makeratecolumn=yes

This command should be clear. It makes a histogram of number of photons
in time bins of 1 s duration, selecting only the events that satisfy the
expression given in "expression=". This is the light curve.

If we want only photon with energies between 0.2 and 1 keV (200 and 100
eV), we can use instead:

> evselect table=MOS2_evt_barcen.fits energycolumn=PI expression='CCDNR==1 && #XMMEA_EM && (PATTERN<=12) && (PI in [200:1000])' withrateset=yes rateset="MOS2_light_curve_background_1s_0.2-1000eV.lc" timebinsize=1 maketimecolumn=yes makeratecolumn=yes

You can try other energy selections, if you want. The detector is
sensitive from 200 to 12000 eV. (This source does not emit significantly
above ~ 1.5-2keV, so if you select only photons above that energy you
will be collecting only background photons. You can try, of course!)

You can now plot the light curve with fplot and search the period using
powspec and efsearch (see exercise 5 for details).


TO BE CONTINUED....




