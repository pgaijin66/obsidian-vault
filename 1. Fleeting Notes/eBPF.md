
- Revolutionary technology which helps to run sandboxed programs in OS kernel.
- It is used to safely and efficiently extend capabilities of the kernel without requiring to change kernel source code or load kernel modules.
- OS kernel is hard to evolve as it plays a central role
- Compared to other tech OS has not evolved much in past uears
- Application developers can run eBPF programs within OS and add additional capabilities to OS at runtime.
- OS then guarantees safety and execution efficiency as if natively compiled with aid of JIT compiler and verification engines.
- This has led a wave of eBPF based projects coverying wide array of use case using next gen networking, observability and security
- today eBPF is used for high performance networking and load balancing in modern data centers and cloud native environments extracting fine grainde security observabikuitt
- We need a BPF program running in kernel and a user space application running in user space which interact with BPF program
- eBPF with BCC ( BPF Compiler Collection ) 
- BCC is a toolkit for creating efficient kernel and tracing and manipulation programs
- when using BCC, we wwrite a program inpythonh which includes the both BPF prgram ( written in C but embedded in python code as a string ) and user space application ( written in python )
- eBPF with BCC works but has a lot of limitations like heavy resource utilization, dependencies, kernel headers, complex debugging etc
- eBPF with Libbpf
- To overcome BCC eBPF with Libbpf took different approach, with Libbpf you wriute btoh eBPF program and user space applciation in C and compile them in advanced
- Bumblebee
- It helps to build, run and distribute eBPF programs using OCI images. 
- It allows focus on writing eBPF code, while taking core of the user space components, automatically exposing your data as metrics or logs

## eBPF
Global Map


Ring Buffer


## Bumble bee

```
curl -sL https://run.solo.io/bee/install | BUMBLEBEE_VERSION=v0.0.13 sh export PATH=$HOME/.bumblebee/bin:$PATH

Scaffold eBPF program
bee init



```