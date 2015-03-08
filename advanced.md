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

(1) Read from location 35 in RAM

![cpu-ram-read](https://cloud.githubusercontent.com/assets/8053664/6543275/f42d339e-c4e3-11e4-816f-e6b128a63906.png)


  1. Load the number 35 (00100011) onto the address busses
  2. Load 1 onto the Enable bus
  3. By setting the enable bus, it signals the RAM to go to memory address 00100011 (35) and output whatever 8-bit number is stored there onto the data busses. The CPU can then do whatever with that data it wants (we'll look at this in a little bit).

_Note: the blue busses would be turned on and off by the RAM depending on whatever value it finds at spot 35. The value at that spot doesn't matter so I didn't set any of them.._

(2) Write 134 to location 35 in RAM

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



|Memory Address (Hex) | Memory Address (Binary) | Content  |
| -----------------  | ----------------------- | -------- |
| 0x28 | 00101000 | Read 0x2f |
| 0x29 | 00101001 | Jump to 0x2b if equals 0 |
| 0x2a | 00101010 | Halt |
| 0x2b | 00101011 | Display next 2 characters to monitor |
| 0x2c | 00101100 | H |
| 0x2d | 00101101 | I |
| 0x2e | 00101101 | Halt |
| 0x2f | 00101110 | 0 |


Whoa... talk about hand-waving.. yes yes I know! You can't actually encode "Display next 2 characters to monitor" in 1 byte. Want to be more rigorous? OK, here is a more low-level version of this what address 0x2b should really say: "Here is the address of the port where you can find the monitor. The following 2 memory slots hold values you should decode using ASCII mapping. Once you decode the letters, send them to the monitor using whatever text-communication-protocol the monitor's driver has defined (found somewhere else in RAM)." 

So how could all that be encoded in 8 bits? Well it's not. 1) most RAMs have memory slots that exceed our 1-byte model. And 2) many processor follow RISC (Reduced Instruction Set Chip) principles, which basically means each instruction will be _extremely_ simple; but on the other hand, the design is such that each instruction also is carried out _really really_ quickly (think billions per second). So in fact, 0x2b should be replaced by 3 or 4 instructions. But for our purposes, we will wave our hands and move on :)



But in any case, let's run through what the CPU will do as it starts to read 0x28. 

1. Ask the RAM for the value at 0x2f and store it in some internal register
2. Sets the B input of the ALU to this value and sets the A input of the ALU to the value 0 and gives the ALU the instruction "compare" (obviously encoded as a binary code)
3. Read the flag the ALU outputs to see whether the two values were equal (the ALU can do one of two things: do some computation and output the result onto the bus, or send a flag directly to the control unit. In the case of a comparison, it sends the flag "equal", or "less than" directly to the control unit)
4. It then jumps to 2b since the condition was true
5. Sends the characters "H" and "I" in that order to the monitor through a predefined text-based communication protocal.

Makes sense?

## OK Let's Really Get Down and Dirty

How about a picture?

![cpu-detailed](https://cloud.githubusercontent.com/assets/8053664/6545071/12e140cc-c549-11e4-9c1d-6bee38b48814.png)

Dear lord help us... so many stuffs and things...
It's OK! You already know half of this stuff; it's just a lot to take in all at once, so let's break it down.

Notice the 8 busses going around and connecting everything. Other than the 4 flags going from the ALU to the controller, they are how these units talk to eachother. So if all these devices are talking through the same medium (the 8 busses) how do their messages not get overwritten by messages from other components? That's where all the E's and S's come into play. They are **set bits** and **enable bits**. The idea is that anytime the control unit wants a register to write on the bus, it sets their set bit to 1, and anytime it wants a register to record whatever value is on the bus, it sets their enable bit to 1. As you can see, certain registers (like Input B of the ALU) don't have an enable bit. This is because that register never has to write on the bus. OK, so let's go through the above 8 steps from the control unit's point of view.

0) Before we start, here are the initial conditions
  1. Memory address register: 00101000 (0x28)
  2. that instruction (Read 0x2f) has been loaded onto the Instruction register

1) Ask the RAM for the value at 0x2f and store it in some internal register
  1. 00101110 (0x2f) is loaded onto the Memory address register.
  2. The set bit for RAM is turned on.
  3. The RAM releases the value at that spot onto the busses. The set bit for register 0 is turned on and the value is recorded.

2) Sets the B input of the ALU to this value and sets the A input of the ALU to the value 0 and gives the ALU the instruction "compare" (obviously encoded as a binary code)
  1. Set Input B's set bit to 1, thereby saving that value in Input B.
  2. Load value 0 onto the busses (notice how this will directly go into Input A - no turning on a set bit needed).
  3. Load the code for "compare" onto the Operation register
  
3) Read the flag the ALU outputs to see whether the two values were equal (the ALU can do one of two things: do some computation and output the result onto the bus, or send a flag directly to the control unit. In the case of a comparison, it sends the flag "equal", or "less than" directly to the control unit)
  1. We don't check the result register (we know we are not interested in a "result" because of the type of operation: comparison). Check flag. See that they are equal. 
  2. Add 1 to instruction address. retrieve the next instruction (by sending it to RAM and retrieving the next memory slot's content). 
4) It then jumps to 2b since the condition was true
  1. Realize it is a "jump if equal" instruction. (realize means look up the operation code)
  2. Check flag previously outputted by ALU. Were they equal? Yes! Jump to specified location. We do this by loading that memory address onto Instruction address, turning on its set bit, and turning on the RAM's and memory address's set bits.
  3. Load the instruction at that memory spot onto the instruction register. 
  4. Realize it tells you to retrieve the next two memory slots' content and send them to the monitor.
5) Sends the characters "H" and "I" in that order to the monitor through a predefined text-based communication protocal.
  1. Load the address of the monitor port onto a register.
  2. Send H to that port
  3. Send I to that port
  4. retrieve the next instruction and realize it says Halt. Halt!



### Conclusion
You have already read enough. So I won't bore you with anymore words. Just going to say that I am super proud that you've stuck through my terrible writing. Hope you found this useful and (who knows) maybe even enjoable :)

Best,
Pedram

