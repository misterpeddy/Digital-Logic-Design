##So Let's get to Designing a CPU

###Overview

So what exactly does the inside of a CPU look like? Well, we can't really start by looking at a modern CPU because too many optimizations have taken place that make understanding a bit more complicated. But the fundamentals of CPUs have not changed. So let's look at a slightly simpler CPU (I drew it up using Logisim, but a lot of the configurations were inspired by the [Scott CPU](http://www.buthowdoitknow.com)).

Here is a rough sketch of the different components you might see on a motherboard:

![Components of a CPU](https://cloud.githubusercontent.com/assets/8053664/6542856/fdcac9ac-c4d4-11e4-8545-ee5b42709298.png)

Easy enough? The discussion in the [previous section](/beginner.md) should have given you enough background to understand each one of these guys (minus the external devices - but that't not really our concern here). Let's look at how these guys are connected.

![Connections to/from a CPU](https://cloud.githubusercontent.com/assets/8053664/6542890/fb7d2540-c4d5-11e4-82f2-c76185250525.png)


Whoa! OK.. so things are getting complicated and you're starting to remember why you stay away from digital logic circuits... But it's OK! Let's analyze these connections one by one.

These wires are the **busses** we talked about. They transfer information (bits) between two components. It's important to keep in mind that they carry _digital_ signals. I.e. at any given instant, there is either electrical current flowing through a bus or not. This construction allows the different components to communicate using numbers (in our example the numbers can range from 0 to 2^8 -1). Let's look at the communication between the CPU and RAM first.


##The Connection Between CPU & RAM

Notice how there are three sets of busses going from the CPU to the RAM

1. A collection of 8 **address busses**
2. A collection of 8 **data busses**
3. A **Set** and an **Enable** connection


Again, recall that all RAM is composed of is 256 8-bit (1-byte) storage units. Why 256? Well because that's how many unique spots we can refer to using 8 binary digits (2^8). If we had 16 address busses, then our RAM could have up to 2^16 1-byte storage units.


Let's recall, the CPU really needs to be able to do 2 things with the RAM.

1. Read from a specific location (memory address) in RAM
2. Write to a specific location (memory address) in RAM

So how does it carry out these two processes?

1. Read from location 35 in RAM

![cpu-ram-read](https://cloud.githubusercontent.com/assets/8053664/6543275/f42d339e-c4e3-11e4-816f-e6b128a63906.png)


  1. Load the number 35 (00100011) onto the address busses
  2. Load 1 onto the Enable bus
  3. By setting the enable bus, it signals the RAM to go to memory address 00100011 (35) and output whatever 8-bit number is stored there onto the data busses. The CPU can then do whatever with that data it wants (we'll look at this in a little bit).


2. Write 134 to location 35 in RAM

![cpu_ram_write](https://cloud.githubusercontent.com/assets/8053664/6543276/f686d424-c4e3-11e4-943b-0ca74f1e4867.png)

  1. Load the number 35 (00100011) onto the address busses
  2. Load 134 (10000110) onto the data busses
  3. Load 1 onto the Set bus
  4. By setting the set bus to 1, we signal the RAM to goto memory address 00100011 (35) and writes the number 10000110 (134), thereby saving it in memory.


##So What is stored on RAM?

So that's how The CPU talks to RAM. But what do these bytes that are being read from and written to RAM _mean_? Well, really anything. Here are some examples:
  1. Memory address
  2. Numbers (literally a number. for example the value that a variable could hold)
  3. A code for a letter (Ever heard of ASCII codes? Each letter or frequently used symbol is represented by a number.)
  4. Instruction code. An instruction code tells the CPU what to do next. Remember, the CPU doesn't actually know what it's doing. All it does is retrieves instructions and data from RAM, execute whatever the instructions tell it to, and write back the result onto RAM or send it to an external device.

##Inside the CPU

So now we know how to read/write onto a stick of memory (RAM). How does this allow the CPU to do all the incredible things it does? Suprisingly, the answer is very simple. Here is the outline:

A program is stored on the RAM in the form of a series of instructions and data.For example, consider the following chunk of memory:

Memory Adress (Hex)|Memory Address (Binary) | Content  |
|: ------------:|: ------- :|
|0x28| 00101000| Read 0x2f|
|0x29| 00101001| Jump to 0x2b if equals 0|
|0x2a| 00101010| Halt|
|0x2b| 00101011| Display next 2 characters to monitor|
|0x2c| 00101100| H|
|0x2d| 00101101| I|
|0x2e| 00101101| Halt|
|0x2f| 00101110| 0|


Whoa... talk about hand-waving.. yes yes I know! You can't actually encode "Display next 2 characters to monitor" in 8 bytes. Want to be more rigorous? OK, here is a more low-level version of this what address 0x2b should really say: "Here is the address of the port where you can find the monitor. The following 2 memory slots hold values you should decode using ASCII mapping. Once you decode the letters, send them to the monitor using whatever text-communication-protocol the monitor's driver has defined (found somewhere else in RAM)." 

So how could all that be encoded in 8 bits? Well it's not. 1) most RAMs have memory slots that exceed our 1-byte model. And 2), many processor follow RISC (Reduced Instruction Set Chip) principles, which basically means each instruction will be _extremely_ simple; but on the other hand, the design is such that each instruction also is carried out _really really_ quickly (think billions per second). 


