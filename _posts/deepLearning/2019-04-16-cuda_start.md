---
layout: post
title: cuda入门教程
tags:
- cuda
categories: deepLearning
description: 实验笔记
---

[CUDA官方入门教程](https://devblogs.nvidia.com/even-easier-introduction-cuda/)

[如何学习cuda/知乎小小将](https://zhuanlan.zhihu.com/p/34587739)

[书籍笔记：GPU高性能编程 CUDA实战](https://blog.csdn.net/fishseeker/article/details/75093166)

# cuda编程基础知识

运行程序前添加命令 `CUDA_VISIBLE_DEVICES=0` ，可以使得只有0号设备可用。也可以用命令 `CUDA_VISIBLE_DEVICES=0,2,3`

GPU并不是一个独立运行的计算平台，而需要与CPU协同工作，可以看成是CPU的协处理器，因此当我们在说GPU并行计算时，其实是指的基于CPU+GPU的异构计算架构。

GPU包括更多的运算核心，其特别适合数据并行的计算密集型任务，如大型矩阵运算，而CPU的运算核心较少，但是其可以实现复杂的逻辑运算，因此其适合控制密集型任务。另外，CPU上的线程是重量级的，上下文切换开销大，但是GPU由于存在很多核心，其线程是轻量级的。因此，基于CPU+GPU的异构计算平台可以优势互补，CPU负责处理逻辑复杂的串行程序，而GPU重点处理数据密集型的并行计算程序，从而发挥最大功效。

CUDA是NVIDIA公司所开发的GPU编程模型，它提供了GPU编程的简易接口，基于CUDA编程可以构建基于GPU计算的应用程序。CUDA提供了对其它编程语言的支持，如C/C++，Python，Fortran等语言.

CUDA编程模型是一个异构模型，需要CPU和GPU协同工作。在CUDA中，host和device是两个重要的概念，我们用host指代CPU及其内存，而用device指代GPU及其内存。CUDA程序中既包含host程序，又包含device程序，它们分别在CPU和GPU上运行。同时，host与device之间可以进行通信，这样它们之间可以进行数据拷贝。典型的CUDA程序的执行流程如下：

1. 分配host内存，并进行数据初始化；
2. 分配device内存，并从host将数据拷贝到device上；
3. 调用CUDA的核函数在device上完成指定的运算；
4. 将device上的运算结果拷贝到host上；
5. 释放device和host上分配的内存。

上面流程中最重要的一个过程是调用CUDA的核函数来执行并行计算，kernel是CUDA中一个重要的概念，kernel是在device上线程中并行执行的函数，核函数用`__global__`符号声明，在调用时需要用`<<<grid, block>>>`来指定kernel要执行的线程数量，在CUDA中，每一个线程都要执行核函数，并且每个线程会分配一个唯一的线程号thread ID，这个ID值可以通过核函数的内置变量threadIdx来获得。

由于GPU实际上是异构模型，所以需要区分host和device上的代码，在CUDA中是通过函数类型限定词开区别host和device上的函数，主要的三个函数类型限定词如下：

* __global__：在device上执行，从host中调用（一些特定的GPU也可以从device上调用），返回类型必须是void，不支持可变参数参数，不能成为类成员函数。注意用__global__定义的kernel是异步的，这意味着host不会等待kernel执行完就执行下一步。
* __device__：在device上执行，单仅可以从device中调用，不可以和__global__同时用。
* __host__：在host上执行，仅可以从host上调用，一般省略不写，不可以和__global__同时用，但可和__device__，此时函数会在device和host都编译。

要深刻理解kernel，必须要对kernel的线程层次结构有一个清晰的认识。首先GPU上很多并行化的轻量级线程。kernel在device上执行时实际上是启动很多线程，一个kernel所启动的所有线程称为一个网格（grid），同一个网格上的线程共享相同的全局内存空间，grid是线程结构的第一层次，而网格又可以分为很多线程块（block），一个线程块里面包含很多线程，这是第二个层次。线程两层组织结构如下图所示，这是一个gird和block均为2-dim的线程组织。grid和block都是定义为dim3类型的变量，dim3可以看成是包含三个无符号整数（x，y，z）成员的结构体变量，在定义时，缺省值初始化为1。因此grid和block可以灵活地定义为1-dim，2-dim以及3-dim结构，对于图中结构（主要水平方向为x轴），定义的grid和block如下所示，kernel在调用时也必须通过执行配置<<<grid, block>>>来指定kernel所使用的线程数及结构。

```
dim3 grid(3, 2);
dim3 block(5, 3);
kernel_fun<<< grid, block >>>(prams...);
```

![kernel](https://pic1.zhimg.com/80/v2-aa6aa453ff39aa7078dde59b59b512d8_hd.jpg)

所以，一个线程需要两个内置的坐标变量（blockIdx，threadIdx）来唯一标识，它们都是dim3类型变量，其中blockIdx指明线程所在grid中的位置，而threaIdx指明线程所在block中的位置，如图中的Thread (1,1)满足：

```
threadIdx.x = 1
threadIdx.y = 1
blockIdx.x = 1
blockIdx.y = 1
```

一个线程块上的线程是放在同一个流式多处理器（SM)上的，但是单个SM的资源有限，这导致线程块中的线程数是有限制的，现代GPUs的线程块可支持的线程数可达1024个。有时候，我们要知道一个线程在blcok中的全局ID，此时就必须还要知道block的组织结构，这是通过线程的内置变量blockDim来获得。它获取线程块各个维度的大小。**对于一个2-dim的block $(D_x,D_y)$，线程 $(x,y)$ 的ID值为 $(x+y*D_x)$ ，如果是3-dim的block  $(D_x,D_y,D_z)$，线程 $(x,y,z)$ 的ID值为 $(x+y*D_x+z*D_x*D_y)$。另外线程还有内置变量gridDim，用于获得网格块各个维度的大小。**

kernel的这种线程组织结构天然适合vector,matrix等运算，如我们将利用上图2-dim结构实现两个矩阵的加法，每个线程负责处理每个位置的两个元素相加，代码如下所示。线程块大小为(16, 16)，然后将N*N大小的矩阵均分为不同的线程块来执行加法运算。

```Python
// Kernel定义
__global__ void MatAdd(float A[N][N], float B[N][N], float C[N][N])
{
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    int j = blockIdx.y * blockDim.y + threadIdx.y;
    if (i < N && j < N)
        C[i][j] = A[i][j] + B[i][j];
}
int main()
{
    ...
    // Kernel 线程配置
    dim3 threadsPerBlock(16, 16);
    dim3 numBlocks(N / threadsPerBlock.x, N / threadsPerBlock.y);
    // kernel调用
    MatAdd<<<numBlocks, threadsPerBlock>>>(A, B, C);
    ...
}
```

此外这里简单介绍一下CUDA的内存模型，如下图所示。可以看到，每个线程有自己的私有本地内存（Local Memory），而每个线程块有包含共享内存（Shared Memory）,可以被线程块中所有线程共享，其生命周期与线程块一致。此外，所有的线程都可以访问全局内存（Global Memory）。还可以访问一些只读内存块：常量内存（Constant Memory）和纹理内存（Texture Memory）。内存结构涉及到程序优化，这里不深入探讨它们。

![CUDA内存模型](https://pic2.zhimg.com/80/v2-6456af75530956da6bc5bab7418ff9e5_hd.jpg)

GPU硬件的一个核心组件是SM，前面已经说过，SM是英文名是 Streaming Multiprocessor，翻译过来就是流式多处理器。SM的核心组件包括CUDA核心，共享内存，寄存器等，SM可以并发地执行数百个线程，并发能力就取决于SM所拥有的资源数。当一个kernel被执行时，它的gird中的线程块被分配到SM上，一个线程块只能在一个SM上被调度。SM一般可以调度多个线程块，这要看SM本身的能力。那么有可能一个kernel的各个线程块被分配多个SM，所以grid只是逻辑层，而SM才是执行的物理层。SM采用的是SIMT (Single-Instruction, Multiple-Thread，单指令多线程)架构，基本的执行单元是线程束（wraps)，线程束包含32个线程，这些线程同时执行相同的指令，但是每个线程都包含自己的指令地址计数器和寄存器状态，也有自己独立的执行路径。所以尽管线程束中的线程同时从同一程序地址执行，但是可能具有不同的行为，比如遇到了分支结构，一些线程可能进入这个分支，但是另外一些有可能不执行，它们只能死等，因为GPU规定线程束中所有线程在同一周期执行相同的指令，线程束分化会导致性能下降。当线程块被划分到某个SM上时，它将进一步划分为多个线程束，因为这才是SM的基本执行单元，但是一个SM同时并发的线程束数是有限的。这是因为资源限制，SM要为每个线程块分配共享内存，而也要为每个线程束中的线程分配独立的寄存器。所以SM的配置会影响其所支持的线程块和线程束并发数量。总之，就是网格和线程块只是逻辑划分，一个kernel的所有线程其实在物理层是不一定同时并发的。所以kernel的grid和block的配置不同，性能会出现差异，这点是要特别注意的。还有，由于SM的基本执行单元是包含32个线程的线程束，所以block大小一般要设置为32的倍数。

在进行CUDA编程前，可以先检查一下自己的GPU的硬件配置，这样才可以有的放矢，可以通过下面的程序获得GPU的配置属性：

`test_gpu.cu`
```cpp
#include <cuda_runtime.h>
#include <iostream>
using namespace std;
int main(){
    int dev,count;
    cudaDeviceProp devProp;
		cudaGetDeviceCount(&count);
		for(int i=0;i<count;i++){
				cudaGetDeviceProperties(&devProp, i);
				std::cout << "使用GPU device " << dev << ": " << devProp.name << std::endl;
				std::cout << "SM的数量：" << devProp.multiProcessorCount << std::endl;
				std::cout << "每个线程块的共享内存大小：" << devProp.sharedMemPerBlock / 1024.0 << " KB" << std::endl;
				std::cout << "每个线程块的最大线程数：" << devProp.maxThreadsPerBlock << std::endl;
				std::cout << "每个EM的最大线程数：" << devProp.maxThreadsPerMultiProcessor << std::endl;
				std::cout << "每个EM的最大线程束数：" << devProp.maxThreadsPerMultiProcessor / 32 << std::endl;
		}
}
```

测试GPU信息：`nvcc test_gpu.cu -o test_gpu` `./test_gpu`


# 向量加法实例

共有四个程序：
* add.cpp 用C++实现向量加法
* add.cu 函数增加global标识、内存分配、但是只使用了一个线程块中的一个线程
* add_grid.cu 修改为多线程块多线程程序

内存管理函数包括：
* cudaError_t cudaMalloc(void** devPtr, size_t size);在cuda上分配内存
* cudaError_t cudaMemcpy(void* dst, const void* src, size_t count, cudaMemcpyKind kind)拷贝数据从cpu到gpu

用这两个函数，我们需要单独在host和device上进行内存分配，并且要进行数据拷贝，这是很容易出错的。但是从cuda6.0之后引入统一内存(Unified Memory)来避免这种麻烦，简单来说就是统一内存使用一个托管内存来共同管理host和device中的内存，并且自动在host和device中进行数据传输。CUDA中使用cudaMallocManaged函数分配托管内存：

`cudaError_t cudaMallocManaged(void **devPtr, size_t size, unsigned int flag=0);`

* add.cpp

```cpp
#include <iostream>
#include <math.h>
#include <ctime>
#include <cstdio>

// function to add the elements of two arrays
void add(int n, float *x, float *y)
{
  for (int i = 0; i < n; i++)
      y[i] = x[i] + y[i];
}

int main(void)
{
  int N = 1<<20; // 1M elements

  float * x = new float[N];
  float * y = new float[N];

  // initialize x and y arrays on the host
  for (int i = 0; i < N; i++) {
    x[i] = 1.0f;
    y[i] = 2.0f;
  }

  // Run kernel on 1M elements on the CPU
  clock_t start = clock();
  add(N, x, y);
  clock_t finish = clock();
  double duration = (double)(finish - start) / CLOCKS_PER_SEC;
  printf("%f seconds\n",duration);

  // Check for errors (all values should be 3.0f)
  float maxError = 0.0f;
  for (int i = 0; i < N; i++)
    maxError = fmax(maxError, fabs(y[i]-3.0f));
  std::cout << "Max error: " << maxError << std::endl;

  // Free memory
  delete [] x;
  delete [] y;

  return 0;
}
```

C++ 测试时间：0.030126 seconds

修改为CUDA：
1. 将说明符`__global__`添加到函数中，该函数告诉cuda编译器这是一个在GPU上运行并可以在CPU调用的函数。这些  `__global__` 函数称为内核。
2. cuda中的内存分配。在GPU中进行计算需要分配GPU可访问的内存。通过调用`cudaMallocManaged()`获得一个可以供CPU或GPU调用的指针，要释放该指针，只需要将指针传递给`cudaFree()`.因此需要将上面代码中的new和delete分别更换如下：

```cpp
// Allocate Unified Memory -- accessible from CPU or GPU
float *x, *y;
cudaMallocManaged(&x, N*sizeof(float));
cudaMallocManaged(&y, N*sizeof(float));
// Free memory
cudaFree(x);
cudaFree(y);
```

3. 在GPU启动add()，用三角括号语法`<<<gridSize,blockSize>>>`指定CUDA内核启动。`add<<<1, 1>>>(N, x, y);`。需要注意的是，cuda内核并不会阻塞CPU线程，因此CPU要等GPU完成后才能访问结果，需在CPU进行错误检查之前调用`cudaDeviceSynchronize()`

4. 完整代码如下，注意此代码保存为`.cu`格式的cuda文件。

* add.cu

```cpp
#include <iostream>
#include <math.h>
#include <ctime>
#include <cstdio>

// Kernel function to add the elements of two arrays
__global__
void add(int n, float *x, float *y)
{
  for (int i = 0; i < n; i++)
    y[i] = x[i] + y[i];
}

int main(void)
{
  int N = 1<<20;
  float * x, * y;

  // Allocate Unified Memory – accessible from CPU or GPU
  cudaMallocManaged(&x, N*sizeof(float));
  cudaMallocManaged(&y, N*sizeof(float));

  // initialize x and y arrays on the host
  for (int i = 0; i < N; i++) {
    x[i] = 1.0f;
    y[i] = 2.0f;
  }

  // Run kernel on 1M elements on the GPU
  clock_t start = clock();
  add<<<1, 1>>>(N, x, y);
  clock_t finish = clock();
  double duration = (double)(finish - start) / CLOCKS_PER_SEC;
  printf("%f seconds\n",duration);

  // Wait for GPU to finish before accessing on host
  cudaDeviceSynchronize();

  // Check for errors (all values should be 3.0f)
  float maxError = 0.0f;
  for (int i = 0; i < N; i++)
    maxError = fmax(maxError, fabs(y[i]-3.0f));
  std::cout << "Max error: " << maxError << std::endl;

  // Free memory
  cudaFree(x);
  cudaFree(y);

  return 0;
}
```

5. 编译运行`nvcc add.cu -o add_cu` `./add_cu`

运行时间：0.588107 seconds

6. 其实，并不需要调用ctime库来查看运行时间，用`nvprof ./add_cuda`命令来显示内核运行时间
7. 现在已经运行了一个内核，其中一个线程可以进行一个计算，但是还没有并行，需要设置`<<<1,1>>>`语法，成为执行配置，显示cuda运行时有多少并行线程用于GPU上的启动。这里有两个参数，第二个参数：线程块中的线程数。CUDA GPU使用大小为32的线程块运行内核，因此256线程是合理的大小
8. 仅做以上修改还是为每个线程执行一个运算，而不是跨并行线程传播计算。要正确运行，我需要修改内核。CUDA C++提供的关键字让内核获得正在运行的线程的索引。
9. CUDA GPU有许多并行处理器，称为streaming Multiprocessors，或SMs。每个SM可以运行多个并发线程块。为了充分利用这些线程，我应该使用多个线程块启动内核。

![](https://devblogs.nvidia.com/wp-content/uploads/2017/01/cuda_indexing.png)

```
gridDim.x（线程块数）
blockDim.x（每个块中的线程数）
blockIdx.x（当前块内的索引）
threadIdx.x（块中当前线程的索引）
```

```python
__global__
void add(int n, float *x, float *y)
{
  int index = blockIdx.x * blockDim.x + threadIdx.x;
  int stride = blockDim.x * gridDim.x;
  for (int i = index; i < n; i += stride)
    y[i] = x[i] + y[i];
}
```

完整代码如下add_grid.cu

```cpp
#include <iostream>
#include <math.h>
#include <ctime>
#include <cstdio>

__global__
void add(int n, float *x, float *y)
{
  int index = blockIdx.x * blockDim.x + threadIdx.x;
  int stride = blockDim.x * gridDim.x;
  for (int i = index; i < n; i += stride)
    y[i] = x[i] + y[i];
}

int main()
{
    int N = 1 << 20;
    int nBytes = N * sizeof(float);

    // 申请托管内存
    float *x, *y, *z;
    cudaMallocManaged((void**)&x, nBytes);
    cudaMallocManaged((void**)&y, nBytes);
    cudaMallocManaged((void**)&z, nBytes);

    // 初始化数据
    for (int i = 0; i < N; ++i)
    {
        x[i] = 10.0;
        y[i] = 20.0;
    }

    // 定义kernel的执行配置
    dim3 blockSize(256);
    dim3 gridSize((N + blockSize.x - 1) / blockSize.x);
    // 执行kernel
    add << < gridSize, blockSize >> >(x, y, z, N);

    // 同步device 保证结果能正确访问
    cudaDeviceSynchronize();
    // 检查执行结果
    float maxError = 0.0;
    for (int i = 0; i < N; i++)
        maxError = fmax(maxError, fabs(z[i] - 30.0));
    std::cout << "最大误差: " << maxError << std::endl;

    // 释放内存
    cudaFree(x);
    cudaFree(y);
    cudaFree(z);

    return 0;
}
```


# 后续

学习教程[CSDN/卜居](https://blog.csdn.net/kkk584520/article/details/9413973)

不定期更新
