# Session 01 HPC



![image-20241112161411839](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112161411839.png)



## **validates Moore’s law**

- Moore’s law is a statement on transistor count/hardware complexity for fixed price 
- Moore’s law used to translate directly into performance for decades
- Let’s study performance growth for the biggest machines in the world

计算性能受限于硬件的物理增长，当前硬件的发展更多依赖并行计算能力



**免费午餐已结束**：单线程性能的提高变得困难，计算性能的提升主要来自并发性

- Increase of performance stems from **increase in concurrency**



## **Three classic objectives of HPC/science:**

- Throughput & efficiency 吞吐量与效率
   Reduce cost as results are available faster; scientists and engineers can do more experiments in a given time frame or study more parameters (UQ)
- Response time 响应时间
   Urgent computing as required for tsunami prediction or in medical applications, e.g.; interactive parameter studies (computational steering)
- Problem size 问题规模
   New insight through never-seen resolution/zoom into scales

> Solution: If chips don’t become faster anymore, we have to squeeze more of them into one computer.

> Implication: If computers become “more parallel”, we have to be able to write “more parallel” codes.



## **Flynn’s taxonomy 分类法**

- **Instruction stream**: sequence of commands (top-down classification)

- **Data stream**: sequence of data (left-right classification)

The combination of data stream and instruction stream yields four combinations:

- SISD
- SIMD
- MISD
- MIMD

![image-20241112162323023](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112162323023.png)



### **SISD**

（单指令流，单数据流）：传统的单核处理方式

- SISD architecture: One ALU and one activity a time
- Runtime: (2·*load* +*add* +*store*)·*N*
- Usually *load* , *add* , *store* require multiple cycles (depend both on hardware and environment such as caches; cf. later sessions)



**Example: (almost) assembler code in SISD**

![image-20241112162522490](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112162522490.png)

This is the model of a computer most people will typically employ when reasoning about their program – a sequence of operations, completing in order, without the details of data locality or hardware.



### **MIMD**

**Assembler code running on MIMD hardware**

![image-20241112162628522](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112162628522.png)

Thus MIMD is frequently thought about as multiple SISD computers working collaboratively on sub-problems.



**MIMD remarks**

- SSID
  - von Neumann’s principle
  - classic single processors with one ALU
  - one instruction on “one” piece of data a time
- MIMD
  - classic multicore/parallel computer: multiple SISD
  - asynchronous execution of several instruction streams (though it might be copies of the same I program)
  - they might work on same data space (shared memory), but the processors do not (automatically) synchronise



### SIMD

- ALU: One activity a time
- Registers: Hold two pieces of data (think of two logical regs)
- Arithmetics: Update both sets at the same time
- Runtime: (4·*load* +*add* +2·*store*)·*N*/2
- *load*, *add*, *store* slightly more expensive than in SIMD
- *load* and *store* can grab two pieces of data in one rush when they are stored next to each other



**Almost assembler code running on SIMD hardware**

![image-20241112162941614](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112162941614.png)

SIMD operates at a lower level of hardware parallelism than the CPU core – it operates at the level of execution units (ALU & FPU).
 Likewise, it is frequently preferable to implement SIMD parallelism automatically, using compiler optimization or `#pragmas`.



#### AVX/SSE

> *Vectorisation:* Rewrite implementation to make it work with small/tiny vectors.

- Extension to four or eight (single precision) straightforward
- Technique: Vectorisation/vector computing
- Hardware realisation: Large registers holding multiple (logical) registers

**AVX/SSE**向量化处理，用于在小型向量上实现并行操作



#### SSE

- SSE = Streaming SIMD Extension
- SIMD instruction instruction set introduced with Pentium III (1999)
- Answer to AMD’s 3DNow (computer games)
- Concept stems from the days when Cray was a big name
- Around 70 single precision instructions
- AMD implements SSE starting from Athlong XP (2001)

**SSE**：流式SIMD扩展，由Intel推出，常用于3D游戏和图形处理

![image-20241112163312389](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112163312389.png)





#### AVX

- Double number of registers
- However, multiple cores might share registers (MIC)
- Operations may store results in third register (original operand not overwritten)
- AVX 2–AVX512: Gather and scatter operations and fused multiply-add

![image-20241112163518958](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112163518958.png)



#### **Classification**

- SISD
  - von Neumann’s principle
  - classic single processors with one ALU
  - one instruction on “one” piece of data a time
- MIMD
  - classic multicore/parallel computer: multiple SISD
  - asynchronous execution of several instruction streams (though it might be copies of the same I program)
  - they might work on same data space (shared memory), but the processors do not (automatically) synchronise

- SIMD
  - synchronous execution of one single instruction
  - vector computer: (tiny) vectors
- MISD
  - pipelining (we ignore it here)



**Flynn’s taxonomy: lessons learned/insights**

- All three paradigms are often combined (see Haswell above
- There is more than one flavour of hardware parallelism
- There is more than one parallelisation approach
- MIMD substypes:
  - shared vs. distributed memory
  - races vs. data inconsistency
  - synchronisation vs. explicit data exchange





## **Speedup and efficiency**

We are usually interested in two metrics:

> Speedup: Let *t*(1) be the time used by one processor. *t*(*p*) is the time required by *p* processors. The speedup is 加速比公式
> $$
> S(p)  = \frac {t(1)}{t(p)}
> $$
> 

> Efficiency: The efficiency of a parallel application is消费比公式
> $$
> E(p) =\frac{s(p)}{p}
> $$
> 



### **Amdahl’s Law**

$$
t(p)=f\cdot t(1)+(1-f)\frac{t(1)}p
$$

Ideas:

- Focus on simplest model, i.e. neglect overheads, memory, . . .
- Assume that code splits up into two parts: something that has to run serially (*f*) with a remaining code that scales
- Assume that we know how long it takes to run the problem on one core.
- Assume that the problem remains the same but the number of cores is scaled up

Remarks:

- We do not change anything about the setup when we go from one to multiple nodes. This is called **strong scaling**.
- The speedup then derives directly from the formula.
-  In real world, there is some concurrency overhead (often scaling with *p*) that is neglected here.



### **Speedup and efficiency—Strong Scaling**

> Speedup: Let *t*(1) be the time used by one processor. *t*(*p*) is the time required by *p* processors. The speedup is *S*(*p*) = *t*(1)/*t*(*p*).

- What is the scaling of the speedup if *f* = 0?
- Are speedups smaller than 1 possible?
- What is super linear speed up?

> Efficiency: The efficiency of a parallel application is*E*(*p*)=*S*(*p*)/*p*.

- What is an upper bound for the efficiency?
- What is a lower bound for the efficiency?



### **Strong Scaling – intuition**

![image-20241112164657858](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112164657858.png)

- Given *f* as the serial fraction, and$t(p)=t(1)(1+\frac{1-f}p)$
- Then $S(p)=1/(f+(1-f)/p)$
- The speedup is limited by the serial fraction: $\lim_{p\to\infty}S(p)=1/f$
- Want to maintain a parallel efficiency of 80% – how does the parallel fraction of the code need to scale as we add more processors?



## **Gustafson’s Law**

$$
t(1)=f\cdot t(p)+(1-f)t(p)\cdot p
$$

Ideas:

- Focus on simplest model, i.e. neglect overheads, memory
- Assume that code splits up into two parts (cf. BSP with arbitary cardinality): something that has to run serially (*f* ) and the remaining code that scales.
- Assume that we know how long it takes to run the problem on *p* ranks. 
- Derive the time required if we used only a single rank.



Remarks:

- Single node might not be able to handle problem and we assume that original problem is chosen such that whole machine is exploited, i.e. problem size is scaled. This is called **weak scaling**.
- The speedup then derives directly from the formula.
- In real world, there is some concurrency overhead (often scaling with *p*) that is neglected here.



### **Weak Scaling – intuition**

![image-20241112165238543](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112165238543.png)

- Perfect *weak scaling* matches the number of processes to the amount of work to do – giving a *constant* run time rather than shorter with more processors
- Given, $t(1)=t(p)f+p(1-f)t(p)$, then $S(p)=f+(1-f)p$
- What is the efficiency as $\lim_{p\to\infty}E(p)?\lim_{p\to1}E(p)$?



**Python Exercise**

![image-20241112165444001](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112165444001.png)

Disclaimer: We will typically not be using Python!



**Python exercise: Comparison of the speedup laws**

![image-20241112165508759](https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112165508759.png)

- It depends on whether you fix the problem size.
- It is crucial to clarify assumptions a priori.
- It is important to be aware of shortcomings.

$$
\begin{aligned}t(p)&=f\cdot t(1)+(1-f)\frac{t(1)}{p}&&t(1)=f\cdot t(p)+(1-f)t(p)\cdot p\\S(p)&=t(1)/t(p)=\frac{1}{f+(1-f)/p}&&S(p)=t(1)/t(p)=f+(1-f)p\end{aligned}
$$





## **Shortcomings of simple performance models**

- These are very simple performance models with a number of assumptions.
  - That a program can be split into two separate parts which can be treated independently.
  - That changing a program to run in parallel will not affect the serial part.
  - That there is no communication overhead, or parallel management which scales with *p*.
  - That every processor in *p* is precisely the same, and experiencing the same environment.
- Frequently, real programs will change behaviour significantly when made parallel on the same hardware – e.g., due to frequency scaling or cache effects.
  - Computers are much more than a collection of loose CPU cores!
  - Phone SoCs are a great example of heterogeneity and ‘the other parts’ leading to dramatically improved performance.
- Is *time* the most relevant metric anymore?
- Some problems simply react differently to parallelisation!



> What about parallel search? If you and *p* friends need to find a phone number in a phone book (before your time!), does Amdahl’s law give us a reasonable model for the time to find the number *t*(*p*) given a known *t*(1)?





### **You have a problem. . . How do you parallelize it?**

Typically in HPC, we have a problem which is well-specified (e.g. in a serial program) but the current approach is insufficient for some reason:

1. Serial is too slow; we need a fast solution
2. Problem is too small; Need to solve a larger or more accurate version
3. Solution is inefficient; we need it solved more efficiently



There are typically three approaches to applying parallel computing to the serial problem (not mutually exclusive):

- Better align with existing hardware capabilities (e.g., SIMD, caches)
- Split the computation so that multiple cores can work on subproblems
- Split the data so that multiple machines can be used to solve parts of the problem simultaneously (distributed memory parallelism)



Split the data so that multiple machines can be used to solve parts of the problem simultaneously (distributed memory parallelism)





## **Programming techniques**

> Programming Techniques: For each parallelisation paradigm, there are many programming techniques/patterns/paradigms.

- We will more or less ignore the competition approach.
  - *Competitive* parallelism is mostly relevant for GUIs and games, where responsiveness to user action is paramount.
  - *Cooperative* parallelism is useful for solving large compute-bound problems without interactivity.
  - These are not necessarily antagonistic concepts: Muller, *et al*., *Competitive Parallelism: Getting Your Priorities Right*; https://www.cs.cmu.edu/~rwh/papers/priorities/paper.pdf
- We will today quickly run through three important patterns.
- More patterns will arise throughout the course.



### **Technique 1: BSP**

> BSP: Bulk-synchronous parallelism is a type of coarse-grain parallelism where inter- processor communication follows the disci- pline of strict barrier synchronization. De- pending on the context, BSP can be re- garded as a computation model for the de- sign and analysis of parallel algorithms, or a programming model for the development of parallel software.

<img src="https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112170742788.png" alt="image-20241112170742788" style="zoom:50%;" />



**Remarks:**

- Other name: Fork-join model
- No communication in original model
- Superstep

**Terminology:**

- Bulk
- Fork
- Join/Barrier/Barrier synchronisation
- Grain size (how many subproblems per thread)
- Superstep

**Analysis:**

- Parallel fraction *f*
- Max. concurrency
-  Fit to strong and weak scaling







### **Technique 2: SPMD**

> SPMD: Single program multiple data is a type of coarse-grain parallelism where ev- ery single compute unit runs exactly the same application (though usually on differ- ent data). The may communicate with each other at any time. Depending on the con- text, SPMD can be regarded as a com- putation model for the design and analy- sis of parallel algorithms, or a programming model for the development of parallel soft- ware.

<img src="https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112170954681.png" alt="image-20241112170954681" style="zoom:50%;" />



### **Technique 3: Pipelining**

Functional decomposition does not yield parallelism on its own, but ...

> Pipelining: In computing, a pipeline is a set of data processing elements connected in series, where the output of one element is the input of the next one. The elements of a pipeline are often executed in parallel.

**Applications:**

- Assembly line
- Graphics pipeline
- Instruction pipeline (inside chip) / RISC pipeline
- Now coming back into fashion (PyTorch, Gpipe; Huang *et al*) for large ML model training

<img src="https://wichaiblog-1316355194.cos.ap-hongkong.myqcloud.com/image-20241112171025782.png" alt="image-20241112171025782" style="zoom:50%;" />

## **Summary, outlook & homework**

**Concepts discussed:**

- Motivation of parallel programming through hardware constraints 
- Course structure
- Parallel Scaling laws – Amdahl & Gustafson
- Basics of parallelisation strategies

**Next:**

- OpenMP!