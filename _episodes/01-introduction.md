---
title: "Working with fMRI data using R"
teaching: 15
exercises: 15
questions:
- "What is fMRI?"
- "How can I read fMRI data in R?"
- "How can I visualise fMRI data with R?"
objectives:
- "Have a basic understanding of where the data comes from."
keypoints:
- "fMRI data is a time series of 3D images of the brain."
- "Visualising and manipulating fMRI nifti images can be done with neurobase."
---


### Recap: what is fMRI?

MRI is an imaging technique, with which we can take scans of muscles, bones, brains,... fMRI is when we repeatedly take MRI scans of the brain.  This allows us to see the brain _in action_ when it's performing tasks or in rest etc.

Imagine a subject listening to broadband noise for 15 seconds, and then listening to silence for 15 seconds, and repeat this for 10 times while taking brain scans every 3 seconds.  Afterwards, we can compare the scans during silence with the scans during music, and end up with an image like the following from [Okada et al., 2013](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0068959).  The brain image in the back is a structural MRI, the red blobs are the parts of the brain that show a significant difference between the two conditions.

![fmrifig]({{ page.root }}/fig/auditory.png){:width="50%"}

In reality, the analysis of the data is much more complex than 'comparing images from two conditions' and there exist thousands of ways to analyse the data.  During this lesson, we will touch a few very basic methods, but a more thorough discussion is outside the scope.  We will assume that all the data is preprocessed, which means that:
- the data are corrected for motion
- the data are corrected for distortions
- the data from different subjects are aligned to a common template
- ...

### Reading and visualising fMRI data with R/neuroconductor.

After opening RStudio, we want to set our working directory to the directory we created during set up, where we can find the data:

~~~
setwd("~/Desktop/validating-fmri")
~~~
{: .r}

Neuroconductor is an open-source platform for analysing neuroimaging (and other) data.  It hosts multiple packages to analyse data using different software.  We will mostly use its  base library `neurobase`. To install, run:

~~~
source("https://neuroconductor.org/neurocLite.R")
neuro_install("neurobase", release = "stable")
~~~
{: .r}

The data is located in the directory `data` in the working directory.  Now we can load the data into R using the function `readnii`.

~~~
library(neurobase)
sub70083 <- readnii("sub-70083_task-rest_bold_space-MNI152NLin2009cAsym_preproc.nii.gz")
~~~
{: .r}

The function `readnii` requires one argument: the name of the file we want to read.  This needs to be character string, which is why we put it in quotes.  We saved the data to the variable `sub70083`.  What comes out is an object of type `oro.nifti`.  Essentially, it is a 4D array, with extra metadata specific to fMRI data.  We can therefore apply functions that we can apply to `arrays`.  Let's look for example at the shape of our data.

~~~
dim(sub70083)
~~~
{: .r}
~~~
[1]  65  77  49 152
~~~
{: .output}

This shows us that the first three dimensions are `65 x 77 x 49`.  These correspond to the x-, y- and z-coordinates of the brain.  The fourth dimension is time, which means there are 152 time points measured.

We can also run a function from `neurobase` that only works on this type of object:

~~~
check_nifti(sub70083)
~~~
{: .r}
~~~
NIfTI-1 format
  Type            : nifti
  Data Type       : 16 (FLOAT32)
  Bits per Pixel  : 32
  Slice Code      : 0 (Unknown)
  Intent Code     : 0 (None)
  Qform Code      : 2 (Aligned_Anat)
  Sform Code      : 1 (Scanner_Anat)
  Dimension       : 65 x 77 x 49 x 152
  Pixel Dimension : 3 x 3 x 4 x 2
  Voxel Units     : Unknown
  Time Units      : sec
~~~
{: .output}

This shows some information that is stored in the "header" of the file.  For example, it tells us the pixel dimensions.  The first three numbers tell us the size of the voxels in space: 3 x 3 x 4 cm.  The last number tells us the dimension in time: a scan was taken every 2 seconds.

Another interesting function is the visualisation of nifti's.  Note that we only visualise one timepoint, the first one.  R will automatically show the first timepoint.

~~~
orthographic(sub70083)
~~~
{: .r}
![fmrifig]({{ page.root }}/fig/orthographic_fmri.jpeg){:width="50%"}


If you're not sure about how to use a certain function, you can call for help:
~~~
?orthographic
~~~
{: .r}
You will see that there are many more arguments that can be passed to the function `orthographic`.  For example the argument `xyz` allows us to specify where to draw the cross-hairs.  Let's try the following:

~~~
orthographic(sub70083,xyz=c(20,20,30))
~~~
{: .r}
![fmrifig]({{ page.root }}/fig/orthographic_fmri.jpeg){:width="50%"}


To look at the `value` of one specific point in time and space, we can use indexes.  Remember our cross-hair in the figure was at the first timepoint at coordinates (20,20,30).  
The following example shows the exact value of this point in the figure at the first timepoint.

~~~
sub70083[20,20,30,1]
~~~
{: .r}
~~~
[1] 1000.697
~~~
{: .output}

This shows that the value at the coordinates (20,20,30) at the first timepoint in the matrix os 1000.697.  The units of the data are a derivative of how much oxygen is in the blood, so in itself pretty meaningless.  Especially when you know that during preprocessing, the average is set to 1000.  What is more interesting is to look at how the value changes over time, which we can do by omitting the index in the 4th dimension:

~~~
sub70083[20,20,30,]
~~~
{: .r}
~~~
[1] 1000.6973 1004.7805 1015.7791 1005.6710 1011.8536  997.8289  991.5651
[8] 1011.9357  992.8675 1010.6214 1011.2115 1013.7089 1002.4484 1000.9062
[15]  994.5741  996.5851 1011.2684 1004.9929 1021.9493 1015.8683 1006.8846
[22]  988.7683 1015.1233 1014.3602 1023.0661 1018.1912 1029.2290 1024.9069
[29] 1020.5861  996.5317 1009.8891 1016.9942 1035.5620 1012.9650 1014.9407
[36]  997.7166 1012.6581  999.4472 1017.8413 1008.0819 1017.7951 1001.2557
[43] 1012.1663 1012.0939  996.8324 1004.2199 1007.0089 1004.6786 1014.5038
[50] 1013.5101 1003.9077 1008.7249 1003.5610 1007.2527 1003.4789 1006.3681
[57] 1009.2558 1000.7733 1015.0810 1025.2957 1018.9713 1033.9100 1042.7651
[64] 1046.0997 1032.5608 1003.3085  997.7484  990.8006 1014.7328 1003.7858
[71] 1022.8207 1017.9227 1017.1747 1008.8672 1019.4747 1031.5552 1018.1984
[78]  996.5482 1007.0275 1000.2004  999.9133 1007.6235 1005.4750 1007.7396
[85] 1000.1245 1008.9327 1011.1164 1023.5939 1010.4206 1004.1788 1010.9327
[92] 1001.6094 1000.8570 1013.3934 1014.8335 1005.6849  996.9290 1012.7615
[99] 1005.9056  998.3813 1008.3497 1020.3425 1036.0526 1024.6921 1028.5922
[106] 1032.4692 1025.2697 1022.0928 1011.0522 1020.7457 1017.9132 1025.1190
[113] 1037.7913 1033.7122 1051.4027 1039.6948 1053.5726 1041.0554 1016.5016
[120] 1009.7083 1014.7555  993.9617 1002.3989 1031.2401 1024.6801 1043.6769
[127] 1021.7629 1039.6647 1022.5081 1032.5875 1024.9011 1003.8295 1010.6686
[134] 1015.8412 1005.4518 1015.1467 1006.5083 1018.5934 1020.0087 1006.8266
[141]  998.4078 1008.3459 1019.4109 1023.7689 1029.6477 1025.5107 1030.8313
[148] 1020.2449 1011.6490 1024.1982 1023.3171 1036.8221
~~~
{: .output}

If we plot this vector, we can see how the measured signal in this voxel changes over time.

~~~
plot(sub70083[20,20,30,],type='l')
~~~
{: .r}

![fmrifig]({{ page.root }}/fig/timeseries.jpeg){:width="80%"}

> ## Exercises
> Now that we know how to read, inspect and visualise fMRI data, it's time for some exercises !
> In the data-folder, there is for each subject 3 nifti-images:
> - mask: sub-XXXXX_task-rest_bold_space-MNI152NLin2009cAsym_brainmask.nii.gz
> - rest: sub-XXXXX_task-rest_bold_space-MNI152NLin2009cAsym_preproc.nii.gz
> - anatomical: sub-XXXXX_T1w_preproc.nii.gz
>
> We have looked at the functional rest data.  Now look at the anatomical scan yourself.
> - Read in the anatomical data.
> - What are the dimensions of the anatomical data?  Compare with the dimensions of the rest scan.
> - Visualise the data.
> - Set the color of the cross-hairs to `blue`
> - Look at what the function `double_ortho` does, and use it to plot the rest scan (the first timepoint) and the anatomical scan.
{: .challenge}
