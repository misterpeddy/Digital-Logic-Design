##So Let's get to Designing a CPU

###Overview

So what exactly does the inside of a CPU look like? Well, we can't really start by looking at a modern CPU because too many optimizations have taken place that make understanding a bit more complicated. But the fundamentals of CPUs have not changed. So let's look at a slightly simpler CPU (I drew it up using Logisim, but a lot of the configurations were inspired by the [Scott CPU](http://www.buthowdoitknow.com)).

Here is a rough sketch of the different components you might see in a CPU:

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





 
