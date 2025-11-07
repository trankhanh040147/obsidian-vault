https://celerdata.com/glossary/single-instruction-multiple-data-simd
- Single Instruction, Multiple Data
- 1 CPU instruction --> multiple data points 
- operations can be vectorized, multiple data points processed in one go

> SIMD stands for **Single Instruction, Multiple Data**. This powerful approach allows a single [[CPU instruction]] to process multiple data points simultaneously. Imagine you're working with an image or two vectors. Normally, operations on these data points would be performed one at a time - a method known as [[scalar operation]]. However, with SIMD optimization, these operations can be vectorized, meaning multiple data points are processed in one go. SIMD architectures typically organize data into vectors or arrays, enabling synchronized execution and faster computational throughput.

SIMD techniques have evolved alongside advancements in computer architecture and instruction set extensions. Initial SIMD implementations emerged in the 1990s, and subsequent developments, such as Intel's Streaming SIMD Extensions (SSE) and Advanced Vector Extensions (AVX), expanded SIMD capabilities. These extensions introduced specialized SIMD instructions that significantly improved computational performance by enabling efficient execution of parallel operations.