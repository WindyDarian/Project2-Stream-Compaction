CUDA Stream Compaction
======================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 2**

* Ruoyu Fan
* Tested on: (TODO) Windows 22, i7-2222 @ 2.22GHz 22GB, GTX 222 222MB (Moore 2222 Lab)

![preview](/screenshots/preview_optimized.gif)

### Description

#### Things I have done

* Implemented __CPU scan and compaction__, __compaction__, __GPU naive scan__, __GPU work-efficient scan__, __GPU work-efficient compaction__, __GPU radix sort (extra)__, and compared my scan algorithms with thrust implemention

* I optimized my work efficient scan, and __speed is increased to 270%__ of my original implementation. 

* I also wrote an __invlusive version__ of __work-efficient scan__ - because i misunderstood the requirement at first! The difference of the inclusive method is that it creates a buffer that is 1 element larger and swap the last(0) and and second last elements before downsweeping. Although I corrected my implemention to exclusive scan, the inclusive scan can still be called by passing ScanType::inclusive to scan_implenmention method in efficient.cu.

* __Radix sort__ assumes inputs are between [0, a_given_maximum) . I compared my radix sort with std::sort and thrust's unstable and stable sort.

* I added a helper class PerformanceTimer in common.h which is used to do performance measurement.


#### Sample Output

```
CIS-565 HW2 CUDA Stream Compaction Test (Ruoyu Fan)
    Block size for naive scan: 1024
    Block size for up-sweep: 1024
    Block size for down-sweep: 1024
    Block size for boolean mapping: 1024
    Block size for scattering: 1024
    Block sizes for radix sort: 1024 1024 1024 1024

****************
** SCAN TESTS **
****************
Array size (power of two): 67108864
Array size (non-power of two): 67108861
    [   8  18  37  41  15  25  27   8  36  28  13  40  24 ...  35   0 ]
==== cpu scan, power-of-two ====
   elapesd time: 134.408ms    (std::chrono Measured)
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625502 1643625537 ]
==== cpu scan, non-power-of-two ====
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625408 1643625440 ]
   elapesd time: 149.901ms    (std::chrono Measured)
    passed 
==== naive scan, power-of-two ====
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625502 1643625537 ]
   elapesd time: 113.867ms    (CUDA Measured)
    passed 
==== naive scan, non-power-of-two ====
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625408 1643625440 ]
   elapesd time: 113.687ms    (CUDA Measured)
    passed 
==== work-efficient scan, power-of-two ====
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625502 1643625537 ]
   elapesd time: 44.2491ms    (CUDA Measured)
    passed 
==== work-efficient scan, non-power-of-two ====
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625408 1643625440 ]
   elapesd time: 44.3104ms    (CUDA Measured)
    passed 
==== thrust scan, power-of-two ====
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625502 1643625537 ]
   elapesd time: 7.73741ms    (CUDA Measured)
   elapesd time: 0ms    (std::chrono Measured)
    passed 
==== thrust scan, non-power-of-two ====
    [   0   8  26  63 104 119 144 171 179 215 243 256 296 ... 1643625408 1643625440 ]
   elapesd time: 7.74371ms    (CUDA Measured)
   elapesd time: 0ms    (std::chrono Measured)
    passed 

*****************************
** STREAM COMPACTION TESTS **
*****************************
Array size (power of two): 67108864
Array size (non-power of two): 67108861
    [   0   1   0   3   3   1   2   1   1   2   1   0   3 ...   3   0 ]
==== cpu compact without scan, power-of-two ====
    [   1   3   3   1   2   1   1   2   1   3   3   1   3 ...   2   3 ]
   elapesd time: 155.403ms    (std::chrono Measured)
    passed 
==== cpu compact without scan, non-power-of-two ====
    [   1   3   3   1   2   1   1   2   1   3   3   1   3 ...   2   2 ]
   elapesd time: 154.901ms    (std::chrono Measured)
    passed 
==== cpu compact with scan ====
    [   1   3   3   1   2   1   1   2   1   3   3   1   3 ...   2   3 ]
   elapesd time: 421.621ms    (std::chrono Measured)
    passed 
==== work-efficient compact, power-of-two ====
    [   1   3   3   1   2   1   1   2   1   3   3   1   3 ...   2   3 ]
   elapesd time: 54.2043ms    (CUDA Measured)
    passed 
==== work-efficient compact, non-power-of-two ====
    [   1   3   3   1   2   1   1   2   1   3   3   1   3 ...   2   2 ]
   elapesd time: 54.1137ms    (CUDA Measured)
    passed 

*****************************
** RADIX SORT TESTS **
*****************************
Array size (power of two): 67108864
Array size (non-power of two): 67108861
Max value: 100
    [  78  22  68  49  66  85  83  63  52  58  25   5  35 ...  84   0 ]
==== std::sort, power-of-two ====
    [   0   0   0   0   0   0   0   0   0   0   0   0   0 ...  99  99 ]
   elapesd time: 1522.95ms    (std::chrono Measured)
==== thrust unstable sort, power-of-two ====
    [   0   0   0   0   0   0   0   0   0   0   0   0   0 ...  99  99 ]
   elapesd time: 429.389ms    (std::chrono Measured)
   elapesd time: 0.001184ms    (CUDA Measured)
    passed 
==== thrust stable sort, power-of-two ====
    [   0   0   0   0   0   0   0   0   0   0   0   0   0 ...  99  99 ]
   elapesd time: 418.915ms    (std::chrono Measured)
   elapesd time: 0.001216ms    (CUDA Measured)
    passed 
==== radix sort, power-of-two ====
    [   0   0   0   0   0   0   0   0   0   0   0   0   0 ...  99  99 ]
   elapesd time: 419.691ms    (CUDA Measured)
    passed 
==== std::sort, non power-of-two ====
    [   0   0   0   0   0   0   0   0   0   0   0   0   0 ...  99  99 ]
   elapesd time: 1516.39ms    (std::chrono Measured)
==== radix sort, non power-of-two ====
    [   0   0   0   0   0   0   0   0   0   0   0   0   0 ...  99  99 ]
   elapesd time: 416.676ms    (CUDA Measured)
    passed 
```

#### Optimization

##### Run less threads for work-efficient scan

For work-efficient scan, my original implementation was using the same of amount of threads for every up sweep and down sweeps. Then I optimized it by using only necessary amount of threads for each iteration.
  
The performance for scanning an array of length 67108861 using work-efficient approach boosted __from ~120.5ms to ~44.4ms__, which is __270% speed__ of my original approach. You can see the data in the files under __test_results/__ folder 

Original implementation:

```c++
// running unnecessary threads
__global__ void kernScanUpSweepPass(int N, int add_distance, int* buffer)
{
    auto index = threadIdx.x + blockIdx.x * blockDim.x;
    if (index >= N) { return; }

    if ((index + 1) % (add_distance * 2) == 0)
    {
        buffer[index] = buffer[index] + buffer[index - add_distance];
    }
}

__global__ void kernScanDownSweepPass(int N, int distance, int* buffer)
{
    auto index = threadIdx.x + blockIdx.x * blockDim.x;
    if (index >= N) { return; }

    if ((index + 1) % (distance * 2) == 0)
    {
        auto temp = buffer[index - distance];
        buffer[index - distance] = buffer[index];
        buffer[index] = temp + buffer[index];
    }
}
```

New implementation:

```c++
// optimized: only launch necessary amount of threads in host code
__global__ void kernScanUpSweepPass(int max_thread_index, int add_distance, int* buffer)
{
    auto tindex = threadIdx.x + blockIdx.x * blockDim.x;

    if (tindex >= max_thread_index) { return; }

    // I encountered overflow problem with index < N here so I changed to tindex < max_thread_index
    size_t index = (add_distance * 2) * (1 + tindex) - 1;

    buffer[index] = buffer[index] + buffer[index - add_distance];
}

// optimized: only launch necessary amount of threads in host code
__global__ void kernScanDownSweepPass(int max_thread_index, int distance, int* buffer)
{
    auto tindex = threadIdx.x + blockIdx.x * blockDim.x;

    if (tindex >= max_thread_index) { return; }

    size_t index = (distance * 2) * (1 + tindex) - 1;  

    auto temp = buffer[index - distance];
    buffer[index - distance] = buffer[index];
    buffer[index] = temp + buffer[index];
}
```

And I calculated the number of threads needed as well as the maximum thread index for every up-sweep and down-sweep pass.

Originally I was still using length of buffer as first parameter, but when I was calculating indices for a thread by using the condition of __(distance * 2) * (1 + tindex) - 1 > N__. There can come some weird result because of the multiplication result is out of bound (even for size_t - it took me 2 hours to debug that). So lessons learned, and I'll use more __n > b/a__ instead of __a*n > b__ as condition in the future.