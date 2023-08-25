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
[Dissecting the NVIDIA Volta GPU Architecture via Microbenchmarking](https://arxiv.org/abs/1804.06826): Probably the most referenced paper of its kind! It's really good although it has a few mistakes (e.g.: 64-bit register banks is a really bad description of how the hardware really works) and it's heavily focused on memory hierarchy rather than the SM multiprocessor itself.

[Dissecting the NVidia Turing T4 GPU via Microbenchmarking](https://arxiv.org/abs/1903.07486): Updated version of the Volta paper for the Turing T4 GPU from the same team. Note that there is a [GTC presentation of their A100 analysis](https://www.nvidia.com/en-us/on-demand/session/gtcspring21-s33322/) but the paper was never released.

[Optimizing Batched Winograd Convolution on GPUs](https://www.cse.ust.hk/~weiwa/papers/yan-ppopp20.pdf): Interesting findings on "yield" placement by NVIDIA compiler (10% higher performance with fewer yields due to better operand reuse and fewer bank conflicts).

[rNdN: Fast Query Compilation for NVIDIA GPUs](https://dl.acm.org/doi/10.1145/3603503): Cool idea & interesting latency data. But it's not clear how it's measured, why is FP32 FMA 5 cycles rather than 4? That's technically true in a very narrow sense since the pipeline is half-rate which adds a cycle, but if you start counting that, you could argue for lots of other numbers including other parts of the pipeline too...

# Papers (Older than Volta)
[Demystifying GPU Microarchitecture through Microbenchmarking](https://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=62DAE8884BBB61C622E0CFFE1534BD3E?doi=10.1.1.189.5309&rep=rep1&type=pdf): GT200

[Micro-benchmarking the GT200 GPU](https://www.eecg.toronto.edu/~moshovos/CUDA08/arx/microbenchmark_report.pdf)

# GTC Official NVIDIA Videos & PDFs (Volta & Newer)
https://www.nvidia.com/en-us/on-demand/session/gtcsj20-s21745/

https://developer.nvidia.com/gtc/2020/video/s21819

https://developer.nvidia.com/gtc/2020/video/s21170

https://developer.nvidia.com/gtc/2020/video/s21730

https://developer.nvidia.com/gtc/2020/video/s21745

# GTC 3rd Party Videos & PDFs (Volta & Newer)
[Dissecting the Ampere GPU Architecture through Microbenchmarking](https://www.nvidia.com/en-us/on-demand/session/gtcspring21-s33322/): Presentation on unreleased paper by the same team as [Dissecting the NVIDIA Volta GPU Architecture via Microbenchmarking](https://arxiv.org/abs/1804.06826)

# NVIDIA Forum Posts by NVIDIA employees
https://forums.developer.nvidia.com/t/why-the-performance-of-tf32-tensor-core-is-poor/261150/17

# NVIDIA Forum Posts by 3rd Parties
[Hand-Tuned SGEMM on GT200 GPU 10%~20% improvement of SGEMM](https://forums.developer.nvidia.com/t/hand-tuned-sgemm-on-gt200-gpu-10-20-improvement-of-sgemm/14816): Includes paper. This is a deep analysis of SGEMM performance on GT200 including throughput of MAD with shared memory read being 6 cycles/warp instead of 4 cycles/warp for a regular MAD. So separate LDS instructions with simple MADs are typically faster than a stream of MADs also reading shareds, which implies NVIDIA didn't lose much when removing this functionality and going (slightly!) more RISC-like in Fermi.
