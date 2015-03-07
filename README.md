# Digital-Logic-Design

###### A high-level overview of the bridge between hardware and software
-------

##Introduction 

We use computers everyday. Yet, not many of us can claim they understand, even on a very high level, how exactly computers operate. Yes we all understand there is a RAM, a CPU and everything is represented as 1's and 0's, but do we really understand what happens from the second you press the "On button" and the moment when your operating system's icon comes on the screen? Well let's dive in!

##Overview of Computer Architecture

Computers are really not that complicated. Here is the big idea: a **CPU** (Central Processing Unit) reads bits of data from **memory**, performs calcuations or logical operations, and writes the resulting data back into memory. That's literally it. There are sometimes other devices called **peripheral devices** that provide input and output and secondary mass storage. To give you an idea of how fast processors are, Intel's "ancient" Pentium microprocessor can run at over 300 **MIPS** (million instructions per second). In the late 1990's computers had about a maximum of 64 "Megs of **RAM**" (Megabytes, millions of bytes, of Random-Access-Memory) packed in a few SIMMs (Single In-Line Memory Modules). By now, as technology has progressed, the price of RAM has dropped and now computers are equipped with Gigabytes of RAM.

Remember that by definition, a computer is a universally _programmable_ device, meaning it can model any task that involves logical and arithmetic operations. However, a computer is never "aware" of what it is doing. It must be following a certain program that tells it which operation it must carry out next. **Von Neumann Architecture** refers to a paradigm invented by John von Neumann in 1946 that basically said "why not just store the program that the computer is to run in its own memory?" Seems intuitive right? Well it was a huge discovery at the time! So under this architecture, instructions were written in a specific portion of RAM, where the CPU accessed and read them one by one, and carried out their orders and storing the result back in the RAM. Let's look at each one of these components individually.

  ###The CPU
CPUs are really characterized by their **instruction set** and internal **registers**. And instruction set is really a list of key value pairs mapping numbers (instruction code called **opcode**) to abstract operations that the **ALU** (Arithmetic Logic Unit) will compute. These operations could be "load", "add", "subtract", etc. Registers are simply one-unit (by unit, I mean it is architecture specific; so on a 32-bit architecture, each register is 32-bits wide) storage places that the **control unit** (the guy that does all the thinking)
