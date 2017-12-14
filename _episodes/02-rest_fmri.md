---
title: "Computing and simulating connectivity."
teaching: 15
exercises: 15
questions:
- "What is functional connectivity?"
- "How can I compute functional connectivity between regions with R?"
- "What's with that paper from Steve Smith?"
- "How can I simulate functional connectivity between regions with R?"
objectives:
- "Have a basic understanding of where the data comes from."
keypoints:
- "fMRI data is a time series of 3D images of the brain."
- "Visualising and manipulating fMRI nifti images can be done with neurobase."
---

### Recap: what is functional connectivity?

We measure the brain as a 3D grid with thousands and thousands of voxels.  However, there is a logical coherence between parts of the brain.  One domain of research is figuring out which are the regions of the brain, and what are its borders.  But there are certain atlases that seem reproducible and

MRI is an imaging technique, with which we can take scans of muscles, bones, brains,... fMRI is when we repeatedly take MRI scans of the brain.  This allows us to see the brain _in action_ when it's performing tasks etc.

Imagine a subject listening to broadband noise for 15 seconds, and then listening to silence for 15 seconds, and repeat this for 10 times while taking brain scans every 3 seconds.  Afterwards, we can compare the scans during silence with the scans during music, and end up with an image like the following from [Okada et al., 2013](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0068959).  The brain image in the back is a structural MRI, the red blobs are the parts of the brain that show a significant difference between the two conditions.
