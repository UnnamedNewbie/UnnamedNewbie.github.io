Work in progress...

**TODO**: Use a consistent academic citation style where appropriate?

# Assemblers & Disassemblers & Friends
[CuAssembler](https://github.com/cloudcores/CuAssembler): Innovative approach supporting most NVIDIA GPUs (Pascal to Ampere officially but should work on Hopper?)

[maxas by Scott Gray](https://github.com/NervanaSystems/maxas): This is probably the most widely referenced and complete assembler for any NVIDIA GPU architecture (Maxwell + Pascal forward compatible). It was written in Perl by Scott Gray who used it to beat the performance of NVIDIA's own SGEMM kernels.
It includes an amazing and widely referenced [SGEMM optimisation walkthrough](https://github.com/NervanaSystems/maxas/wiki/SGEMM) that also partially explains reuse flags for operand caching on Maxwell (possibly changed since then). The actual SGEMM part doesn't have all the details so someone [reverse engineered it](https://github.com/Stefan20162016/maxas-explained) in more depth (which I have only skimmed).
Scott Gray later introduced efficient Winograd kernels for Deep Learning convolutions, then went to work at OpenAI, and some of the small-tile SGEMM kernels he developed there were even included in the official NVIDIA libraries (cuBLAS 8.0.61.2).

[DocumentSASS](https://github.com/0xD0GF00D/DocumentSASS): Not an assembler but extracts information directly from strings in NVIDIA binaries which reveals some interesting details (including which long latency instructions share queues, some semi-reliable latency information, etc...)

[TuringAs](https://github.com/daadaada/turingas): Supports many but not all instructions for SM_75 (Turing) and SM_80 (Ampere). This is probably a viable alternative for CuAssembler in some cases but I haven't used it myself.

[Decuda and cudasm for G80](https://github.com/laanwj/decuda): Not just the first NVIDIA CUDA assembler but also the very first disassembler before the official cuobjdump was released by NVIDIA.

[AsFermi](https://github.com/hyqneuron/asfermi): Includes some interesting discussion on 

[KeplerAs](https://github.com/xiuxiazhang/KeplerAs): Derived from MaxAs so it also uses Perl, but quite different which highlights how big a jump Maxwell was in many ways.

[NVIDIA announcement of cuobjdump for G80 to GT200](https://forums.developer.nvidia.com/t/cuobjdump-now-available-to-registered-developers/18443): Official NVIDIA Disassembler announcement for SM_1x (G8x/G9x/GT2xx) which now supports the latest GPU architectures and is included in every CUDA installation. NVIDIA has continued the exact same strategy and level of discolosure to this day: release an official disassembler but no assembler, and very little information on the assembly format.

[NVIDIA cuda-binary-utilities](https://docs.nvidia.com/cuda/pdf/CUDA_Binary_Utilities.pdf): Official documentation for cuobjdump and nvdisasm with minimal description of the instruction set for each generation (instruction names for each opcode + source/destination types in general... and that's pretty much it!)

# Papers (Volta & Newer)
[Dissecting the NVIDIA Volta GPU Architecture via Microbenchmarking](https://arxiv.org/abs/1804.06826): Probably the most referenced paper of its kind! It's really good although it has a few mistakes (e.g.: 64-bit register banks is a really bad description of how the hardware really works) and it's heavily focused on the memory hierarchy rather than the SM multiprocessor itself.

[Dissecting the NVidia Turing T4 GPU via Microbenchmarking](https://arxiv.org/abs/1903.07486): Updated version of the Volta paper for the Turing T4 GPU from the same team. Note that there is a [GTC presentation of their A100 analysis](https://www.nvidia.com/en-us/on-demand/session/gtcspring21-s33322/) but the paper was never released.

[Numerical Behavior of the NVIDIA Tensor Cores](https://eprints.maths.manchester.ac.uk/2761/1/fhms20.pdf): In-depth analysis of how all the ways in which Volta FP16 tensor cores are not IEEE compliant (intentionally for the sake of area/power savings).

[Optimizing Batched Winograd Convolution on GPUs](https://www.cse.ust.hk/~weiwa/papers/yan-ppopp20.pdf): Interesting findings on "yield" placement by NVIDIA compiler (10% higher performance with fewer yields due to better operand reuse and fewer bank conflicts).

[rNdN: Fast Query Compilation for NVIDIA GPUs](https://dl.acm.org/doi/10.1145/3603503): Cool idea & interesting latency data. But it's not clear how it's measured, why is FP32 FMA 5 cycles rather than 4? That's technically true in a very narrow sense since the pipeline is half-rate which adds a cycle, but if you start counting that, you could argue for lots of other numbers including other parts of the pipeline too...

[Low Overhead Instruction Latency Characterization for NVIDIA GPGPUs](https://arxiv.org/abs/1905.08778): Data is somewhat questionable (no sanity checking of assembly resulting in obvious anomalies) but somewhat interesting and included for completeness.

[Recovering single precision accuracy from Tensor Cores while surpassing the FP32 theoretical peak performance](https://arxiv.org/abs/2203.03341): Using multiple Tensor Core operations in a clever way to achieve FP32 SGEMM precision faster than with the native FMA pipeline (presumably IEEE compliant when using TF32 tensor cores since it matches cuBLAS).

# Papers (Older than Volta)
[Demystifying GPU Microarchitecture through Microbenchmarking](https://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=62DAE8884BBB61C622E0CFFE1534BD3E?doi=10.1.1.189.5309&rep=rep1&type=pdf): GT200

[Micro-benchmarking the GT200 GPU](https://www.eecg.toronto.edu/~moshovos/CUDA08/arx/microbenchmark_report.pdf)

# NVIDIA Papers & Whitepapers
[Energy-efficient Mechanisms for Managing Thread Context in Throughput Processors](https://research.nvidia.com/publication/2011-06_energy-efficient-mechanisms-managing-thread-context-throughput-processors) (June 2011): 1st of 3 landmark papers on operand caching and hierarchical scheduling which eventually made it into Maxwell nearly 3 years later (seemingly in a very different form).

[A Compile-Time Managed Multi-Level Register File Hierarchy](https://research.nvidia.com/publication/2011-12_compile-time-managed-multi-level-register-file-hierarchy) (December 2011): 2nd paper.

[A Hierarchical Thread Scheduler and Register File for Energy-Efficient Throughput Processors](https://research.nvidia.com/publication/2012-04_hierarchical-thread-scheduler-and-register-file-energy-efficient-throughput) (April 2012): 3rd and final paper (still doesn't seem to match Maxwell or Volta at all!)

[NVIDIA H100 Tensor Core GPU Architecture](https://resources.nvidia.com/en-us-tensor-core): Note the only official URL I could find for this is a bit strange so this might eventually stop working... There is a [copy on hpctech.co.jp](https://www.hpctech.co.jp/catalog/gtc22-whitepaper-hopper_v1.01.pdf) but that also doesn't feel like a very long-term URL either unfortunately.

[NVIDIA Ada GPU Architecture](https://images.nvidia.com/aem-dam/Solutions/Data-Center/l4/nvidia-ada-gpu-architecture-whitepaper-v2.1.pdf)

[NVIDIA Ampere GA102 GPU Architecture](https://www.nvidia.com/content/PDF/nvidia-ampere-ga-102-gpu-architecture-whitepaper-v2.pdf)

[NVIDIA A100 Tensor Core GPU Architecture](https://images.nvidia.com/aem-dam/en-zz/Solutions/data-center/nvidia-ampere-architecture-whitepaper.pdf)

[NVIDIA Turing GPU Architecture](https://images.nvidia.com/aem-dam/en-zz/Solutions/design-visualization/technologies/turing-architecture/NVIDIA-Turing-Architecture-Whitepaper.pdf)

[NVIDIA Tesla V100 GPU Architecture](https://images.nvidia.com/content/volta-architecture/pdf/volta-architecture-whitepaper.pdf)

# NVIDIA Documentation
[Nsight Graphics Activities - Advanced Learning](https://docs.nvidia.com/nsight-graphics/AdvancedLearning/index.html): A few interesting microarchitectural details including: (on L1) "The T-Stage address coalescer can output up to 4 tags per cycle, for divergent accesses. This ensures T-Stage is almost never a limiter, compared to Data-Stage". The 4 tags/clk has been confirmed in my testing on Ada Lovelace (with hashing unlike the data banks).

[CUDA Toolkit Documentation](https://docs.nvidia.com/cuda/): Main page linking to lots of other key CUDA documentation including: Programming Guide, Best Practices Guide, Tuning Guide (per-architecture), PTX ISA, etc... Some of these are linked below.

[CUDA C++ Programming Guide: 5.4. Maximize Instruction Throughput](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#maximize-instruction-throughput): Official table of instruction throughput for all key operations on all NVIDIA architectures. Plenty of GPU (micro)architecture information spread around the rest of the document too!

[PTX ISA](https://docs.nvidia.com/cuda/parallel-thread-execution/index.html): Key documentation of the PTX ISA which reveals a lot of microarchitectural details (but you need to be careful not to assume the HW/SASS is a 1:1 match with the PTX which is just an intermediary representation of the program a bit like SPIR-V for Vulkan!) - especially interesting are the sm_90a bits which are the parts of Hopper where NVIDIA does not guarantee forward compatibility. This includes a crazy instruction that changes the number of allocated registers in a warp group of 4 warps/128 threads, but only from a per-thread-block pool of registers - this seems to be highly specialised towards Warp Specialisation (see 

[CUDA C++ Best Practices Guide](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html)

[Hopper Tuning Guide](https://docs.nvidia.com/cuda/hopper-tuning-guide/index.html) and [Ampere Tuning Guide](https://docs.nvidia.com/cuda/ampere-tuning-guide/index.html): Similar pages exist for other architectures including Ampere and Ada.

[Kernel Profiling Guide](https://docs.nvidia.com/nsight-compute/ProfilingGuide/index.html): A lot of using information on interpreting the data in NVIDIA's tools but also a lot of microarchitectural information, especially on the L1 cache and the concept of "wavefronts" (which have nothing to do with AMD's wavefronts that are equivalent to NVIDIA's warps). This highlights that there is no attempt at smoothing bank conflicts over multiple cycles (for either shared or global memory) and that any bank conflict will simply result in one or more wasted clock cycles. If I understand correctly (to be confirmed), this is true for both data bank conflicts and for excessive tag checks (but 4 tag checks/clk seems like a lot except for very sparse accesses, so this should not affect performance in most cases).

# NVIDIA Presentations (Volta & Newer)
## General
[Requests, Wavefronts, Sectors Metrics: Understanding and Optimizing Memory-Bound Kernels with Nsight Compute](https://www.nvidia.com/en-us/on-demand/session/gtcspring21-s32089/): A lot of really good information on the "Volta and newer" L1 & L2 caches with detailed graphs of microbenchmarks. Slide 41 is especially interesting as this is exactly the behaviour you would expect given the 4 tags/clk throughput mentioned in [Nsight Graphics Activities - Advanced Learning](https://docs.nvidia.com/nsight-graphics/AdvancedLearning/index.html) even though this presentation doesn't explicitly confirm that figure. It also points out that ECC results in additional DRAM reads for partial writes.

## Hopper
[Hot Chips video of "NVIDIA Hopper GPU: Scaling Performance"](https://www.youtube.com/watch?v=hxcAA32Htt0#t=8m20s): Full video of NVIDIA's Hopper presentation at Hot Chips 34 (2022). No more microarcitectural information than elsewhere unfortunately, but in the Q&A they confirmed that there is a dedicated interconnect for moving data across SMs inside a GPC with Cluster Thread Block (it apparently doesn't reuse the wires for L2<->L1 communication). The slides also highlight that A100/Ampere did not have full HW support for barrier arrive() and effectively spinned on a value in shared memory (unlike Hopper which sleeps the warp for exactly the right duration).

[https://www.nvidia.com/en-us/on-demand/session/gtcspring23-s51413/](https://www.nvidia.com/en-us/on-demand/session/gtcspring22-s41489/): Tensor Core microarchitecture is partially hinted at on Page 44 which shows that the layout changes from 16x8x8 on Ampere to 64x16x16 on Hopper, and the sources now typically come directly from shared memory rather than registers. This is a really in-depth analysis of the algorithms used in CUTLASS for efficient matrix multiplication including Persistent Warp Specialization.

[Inside the NVIDIA Hopper Architecture](https://www.nvidia.com/en-us/on-demand/session/gtcspring22-s42663/) (2022): Good high-level introduction to what's new in Hopper. Relatively little microarchitectural information. See also NVIDIA's official whitepaper ([NVIDIA H100 Tensor Core GPU Architecture](https://resources.nvidia.com/en-us-tensor-core)).

[Optimizing CUDA Applications for NVIDIA Hopper Architecture](https://www.nvidia.com/en-us/on-demand/session/gtcspring22-s41489/): Concrete examples optimising workloads with Thread Block Clusters and asynchronous compute including TMA. Not much microarchitectural information.

[Optimizing applications for NVIDIA Hopper Architecture](https://www.nvidia.com/en-us/on-demand/session/gtcspring23-s51119/) (2023): Newer version of the above by the same engineers, no new microarchitectural information, slightly different (less detailed?) examples. Probably not worth reading unless you are interested in optimising Pairwise Distance or FFT algorithms specifically.

[Enabling Hopper-Specific Optimizations in CUDA Applications](https://www.nvidia.com/en-us/on-demand/session/gtcfall22-a41147/): Not microarchitecture, but highlights how Hopper microarchitecture has functionality that is NOT forward compatible and requires using SM_90a which will not work on any future device (unlike SM_10 PTX which still works on Hopper!)

[Programming Model and Applications for the Grace Hopper Superchip](https://www.nvidia.com/en-us/on-demand/session/gtcspring23-s51120/): Not GPU microarchitecture, but explains how data migration works etc... including the fact that for the default system malloc(), the physical pages are not allocated until later, and it is stored in the RAM of the device which touches it first (CPU or GPU). So doing a CPU memset() on an output array actually has a really big impact! But it is really cool you can just pass that pointer from malloc() to a CUDA kernel and it just works.

## Ampere
[Inside the NVIDIA Ampere Architecture](https://developer.nvidia.com/gtc/2020/video/s21730): Good high-level introduction of what's new in Ampere. This also highlights that A100 and C++20 barriers were co-developed as Olivier Giroux was very active in the C++ standards committee at NVIDIA (he is now at Apple).

[(AnandTech) Hot Chips 2020 Live Blog: NVIDIA A100 Performance](https://www.anandtech.com/show/15992/hot-chips-2020-live-blog-nvidia-a100-performance-500pm-pt): AnandTech's live blog of the A100 HotChips presentation (Video is not publicly available unfortunately).

[Optimizing Applications for NVIDIA Ampere GPU Architecture](https://developer.nvidia.com/gtc/2020/video/s21819): Very interesting information on Ampere compute compression and L2 persistence etc...

[CUDA on NVIDIA Ampere GPU Architecture: Taking Your Algorithms to the Next Level of Performance](https://developer.nvidia.com/gtc/2020/video/s21170): Excellent information with some graphs of microbenchmarks for async copies and L2 residency.

[Developing CUDA Kernels to Push Tensor Cores to the Absolute Limit on NVIDIA A100](https://www.nvidia.com/en-us/on-demand/session/gtcsj20-s21745/): Very in-depth including bank conflict analysis.

## Volta & Turing
[Volta: Architecture and Performance Optimization](https://www.nvidia.com/en-us/on-demand/session/gtcsiliconvalley2018-s81006/): Really good presentation with a lot of microarchitecture information for Volta which still applies to Ampere/Hopper. A lot of detail about shared memory, including Slide 66 "Bank Conflict Resolution" which points out that conflict resolution works across all 32 threads in a warp for 4B accesses, but only 16 threads for 8B and 8 threads for 16B. This makes a lot of sense from a HW perspective as it can only do perfect uniquification across 32 addresses and going for 128-way uniquification would be mind blowingly expensive since uniquification is O(n^2). This will result in better performance in the typical case than doing uniquification for 4B at a time on all 32 threads which seems to be what AMD's MI200 is doing based on [gpu-benches's gpu-strides microbenchmark](https://github.com/te42kyfo/gpu-benches#gpu-strides).

[Volta & Turing: Architecture and Performance optimization](https://www.nvidia.com/en-us/on-demand/session/gtcsiliconvalley2019-s9234/): Not much new information. There's a fair bit of focus on FP16 with the somewhat detailed example of a 2D filter and how to use half/half2 (half wastes 50% of every 32-bit!)

# NVIDIA GTC 3rd Party Videos & PDFs (Volta & Newer)
[Dissecting the Ampere GPU Architecture through Microbenchmarking](https://www.nvidia.com/en-us/on-demand/session/gtcspring21-s33322/): Overview of **UNRELEASED** Ampere paper by by the same team as the Volta/Turing papers - so this is much more interesting than their presentations below where the paper is publicly available with more information than the presentation. It highlights some interesting behaviour of the A100 L2 and also atomic performance with the new "increment" atomic operation.

[https://www.nvidia.com/en-us/on-demand/session/gtcsiliconvalley2018-s8122/](https://www.nvidia.com/en-us/on-demand/session/gtcsiliconvalley2018-s8122/): Just an overview of the well-known paper by its authors.

[Discovering the Turing T4 GPU Architecture with Microbenchmarks](https://www.nvidia.com/en-us/on-demand/session/gtcsiliconvalley2019-s9839/): I was actually in the room for this one :) Citadel printed a ridiculous number of high quality copies of the paper with laminated covers and literally put one on every single chair in the oversized conference room (which was at less than 25% capacity). I think the message was pretty clear: "We have loads of money and don't know what to do with it, come work for us!"

# NVIDIA Forum Posts by NVIDIA employees
https://forums.developer.nvidia.com/t/why-the-performance-of-tf32-tensor-core-is-poor/261150/17: Some information on L1 cache microarchitecture.

# NVIDIA Forum Posts by 3rd Parties
[Hand-Tuned SGEMM on GT200 GPU 10%~20% improvement of SGEMM](https://forums.developer.nvidia.com/t/hand-tuned-sgemm-on-gt200-gpu-10-20-improvement-of-sgemm/14816): Includes paper. This is a deep analysis of SGEMM performance on GT200 including throughput of MAD with shared memory read being 6 cycles/warp instead of 4 cycles/warp for a regular MAD. So separate LDS instructions with simple MADs are typically faster than a stream of MADs also reading shareds, which implies NVIDIA didn't lose much when removing this functionality and going (slightly!) more RISC-like in Fermi.
