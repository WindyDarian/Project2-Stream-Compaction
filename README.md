CUDA Stream Compaction
======================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 2**

* (TODO) YOUR NAME HERE
* Tested on: (TODO) Windows 22, i7-2222 @ 2.22GHz 22GB, GTX 222 222MB (Moore 2222 Lab)

### (TODO: Your README)

Include analysis, etc. (Remember, this is public, so don't put
anything here that you don't want to share with the world.)

In CPU compact scan part, I reused output buffer as a temp buffer to save space without having trouble of (imagined) access violation.
(I did that in GPU compact too!)

I wrote an invlusive version of work-efficient scan - because i misunderstood the requirement at first