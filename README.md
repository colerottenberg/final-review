# final-review
Final Review for Digital Design

This is the documentation for the final review of Digital Design. The final exam will consist of the following topics:
- Pipeline/Optimization
- Timing/Protocols
- Interconnects/Buses/Crossbars/Arbitration
- Memory
- Verification
- Miscellaneous
- [Pipeline/Optimization](#pipeline/optimization)


## Pipeline/Optimization

## Timing/Protocols

### Timing
**Always Valid Timing** is a timing schema that assumes data is always valid. This is a good assumption when the data is always valid, such as when a signal is always high or low. This is a good assumption for a clock signal, as it is always high or low. Examples include a temperature sensor that always outputs a valid signal. This is also often used in combinational logic, where the output is always valid. It can also be implemented in games, where the game is always running and the output is always valid.

**Periodically Valid Timing** is a timing schema that assumes data is valid at certain times. A data signal is valid at every ***N*** clock cycles. 

**Flow Control** is a timing schema that uses a control signal to indicate when data is valid. There are often two signals needed by a interface. Those being both the ***valid*** and ***ready*** signals. The ***valid*** signal indicates that the data is valid, while the ***ready*** signal indicates that the data is ready to be read. As soon as both signals are high, the data transfer is complete. Both signals are often used in a handshake protocol.

**Serialization** is a timing schema that converts parallel data into serial data. Think of this as splitting a data signal into multiple frames. In this method a pre-defined protocol is used to signify the size of the data in each frame. At the beginning of each frame, the frame signal is sent high, it is then sent low for the second frame, and so on. 

**Ser. with word granularity FC** is a timing schema that makes use of flow control with word granularity. Imagine an interface where the first word is a control word, and the second and third words are the address and all following words are data. The control word is a specific

### Protocols
**Handshake Protocol** is a protocol that uses a **req** and **ack** signal to indicate when data is valid. The **req** signal is used to request data, while the **ack** signal is used to acknowledge that the data is valid. This protocol is often used in a master-slave configuration.

**Strobbed Protocol** is a protocol that uses a **req** signal to request data, and the sender has a certain amount of time to respond with the data, $T_{req}$. This protocol is often used in a master-slave configuration.

**UART Protocol** is a protocol that uses a start bit, data bits, a parity bit, and a stop bit. The start bit is used to indicate the beginning of the data frame. The start bit involves pulling the signal low for a certain amount of time which sets the time for the clock. The data bits are the actual data being sent. There are often 8 data bits. The parity bit is used to check for errors in the data. The parity bit is often set to 0 if there are an even number of 1's in the data, and 1 if there are an odd number of 1's in the data. This type of parity is called ***even parity***. The stop bit is used to indicate the end of the data frame. The stop bit is often set to 1.
## Interconnects/Buses/Crossbars/Arbitration
### Interconnects
Used when many clients need to communicate. Ad-hoc point-to-point wiring or shared interconnects. Like a telephone exchange.

### Buses

**VHDL Bus**
```vhdl
-- Combinational Bus Interface
-- t (transmit) and r (receive) in signal names are from the
-- perspective of the bus
library ieee;
use ieee.std_logic_1164.all;
entity BusInt is
  generic( aw: integer := 2; -- address width
    dw: integer := 4 ); -- data width
  port( cr_valid, arb_grant, bt_valid: in std_logic;
    cr_ready, ct_valid, arb_req, br_valid: out std_logic;
    cr_addr, bt_addr, my_addr: in std_logic_vector(aw-1 downto 0);
    br_addr: out std_logic_vector(aw-1 downto 0);
    cr_data, bt_data: in std_logic_vector(dw-1 downto 0);
    br_data, ct_data: out std_logic_vector(dw-1 downto 0) );
end BusInt;

architecture impl of BusInt is
begin
  -- arbitration
  arb_req <= cr_valid;
  cr_ready <= arb_grant;
  -- bus drive
  br_valid <= arb_grant;
  br_addr <= cr_addr when arb_grant else (others => '0');
  br_data <= cr_data when arb_grant else (others => '0');
  -- bus drive
  br_valid <= arb_grant;
  br_addr <= cr_addr when arb_grant else (others => '0');
  br_data <= cr_data when arb_grant else (others => '0');
  -- bus receive
  ct_valid <= '1' when (bt_valid = '1') and (bt_addr =
  my_addr) else '0';
  ct_data <= bt_data ;
end impl;
```

#### [Multilevel Bus Architecture](https://github.com/colerottenberg/final-review/blob/main/EEL4712-Spring-2024-05-Integration.pdf#page=21) on page 21 of the EEL4712-Spring-2024-05-Integration.pdf
We wouldn't want a single bus to connect all the clients. Instead, we would want to use a multilevel bus architecture. This is where we have a bus connecting a few clients, and then another bus connecting the buses. This is often used in a hierarchical system.

#### What factors determine choice of interconnect?
1. **Latency** - How long does it take for a signal to travel from one client to another?
2. **Bandwidth** - How many bits can be sent at once?
3. **Capacity** - How many clients can be connected?
4. **Granularity** - How many bits can be sent at once?

### Crossbars
Crossbar Switches are used to connect multiple clients to multiple clients. This is often used in a network switch. The crossbar switch is a matrix of switches that connect the clients to each other. The crossbar switch is often used in a network switch.

## Memory
Memory is used to hold data for later recall. There are many types of memory, such as SRAM, DRAM, and Flash Memory. We cover SRAM and DRAM in this section. For more inormation on Memory, see the [Memory](https://github.com/colerottenberg/final-review/blob/main/EEL4712-Spring-2024-05-Integration.pdf#page=28) on page 28.

### RAM
In RAM there are two buses and control signals. The first bus is the address bus, or **ABUS**. This bus has a width of ***k*** bits. The second bus is the data bus, or **DBUS**. This bus has a width of ***n*** bits. The control signals are the **R/W** signal, the **OE** signal, and the **WE** signal. The **R/W** signal is used to indicate whether the data is being read or written. The **OE** signal is used to enable the output of the data. The **WE** signal is used to enable the writing of the data. The RAM module has up to $2^{k-1} \times n$

***Example of RAM***
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.all;
use IEEE.Numeric_Std.all;
entity sync_ram is
  port ( clock : in std_logic;
    we : in std_logic;
    address : in std_logic_vector;
    datain : in std_logic_vector;
    dataout : out std_logic_vector );
end entity sync_ram;
architecture RTL of sync_ram is
  type ram_type is array (0 to (2**address'length)-1) of std_logic_vector(datain'range);
  signal ram : ram_type; signal read_address : std_logic_vector(address'range);
begin RamProc: process(clock) is
  begin
    if rising_edge(clock) then
      if we = '1' then
        ram(to_integer(unsigned(address))) <= datain;
      end if;
      read_address <= address;
    end if;
end process RamProc;
  dataout <= ram(to_integer(unsigned(read_address)));
end architecture RTL;
```
This is an example of a synchronous RAM module. The RAM module has a clock signal, a write enable signal, an address signal, a data in signal, and a data out signal. The RAM module has a process that is sensitive to the clock signal. If the write enable signal is high, the data in signal is written to the address in the RAM module. The data out signal is set to the data at the address in the RAM module.

### [SRAM](https://github.com/colerottenberg/final-review/blob/main/EEL4712-Spring-2024-05-Integration.pdf#page=33) on page 31 of the EEL4712-Spring-2024-05-Integration.pdf
The Static Random Access Memory (SRAM) is a type of memory that uses flip-flops to store data. The SRAM is faster than the DRAM, but it is also more expensive. The SRAM is often used in cache memory. 
It is called static because the data is stored in flip-flops and does not need to be refreshed. The SRAM is often used in cache memory.

### [DRAM](https://github.com/colerottenberg/final-review/blob/main/EEL4712-Spring-2024-05-Integration.pdf#page=32) on page 32 of the EEL4712-Spring-2024-05-Integration.pdf
The DRAM primitivea are used to store data in a matrix of capacitors. The DRAM is slower than the SRAM, but it is also cheaper. The DRAM is often used in main memory. The DRAM is called dynamic because the data is stored in capacitors and needs to be refreshed. The DRAM is often used in main memory.

### What to do when we need more memory or bandwidth than one primitive?

#### Bit-Slicing Memory
Bit-Slicing Memory is a method of increasing the memory or bandwidth of a memory primitive. This is done by using multiple memory primitives in parallel. This is often used in a memory controller. Bit-slicing allows for memory to be accessed at a bit in multiple words.
This can lead to speed ups in parallel processing.
##### Example of Bit-Slicing
This can also allow us to expand the memory of a memory primitive. If we have a memory primitive with a capacity of 16k words and a width of 16 bits, we can use 4 memory primitives in parallel to increase the capacity to 16k words and the width to 64 bits.

#### Banking
Banking is a method of increasing the memory or bandwidth of a memory primitive. This is done by using multiple memory primitives in parallel. This is often used in a memory controller.
Banking involves using a decoder to select the memory primitive. This can allow for the expansion of total memory capacity. 
##### Example of Banking
Lets say we have a memory primitive with a capacity of 16k words and a width of 16 bits. This gives us a total address width of $k = log_2(n-1) = 14$. If we use 4 memory primitives in parallel, we can increase the capacity to 64k words, but now need an additional 2 bits to properly address the words. So we slice the first 14 bits to address the individual memory primitive and the last 2 bits are used in a decoder to select which primitive to enable.

#### Bit-Slicing and Banking
Bit-Slicing is a method to increase the word size of a memory module. Banking is a method to increase the capacity of a memory module. Bit-Slicing and Banking can be used together to increase the word size and capacity of a memory module. This is often used in a memory controller.
##### Example of Bit-Slicing and Banking
This just involves the usage of both those methods in tandem.

## Verification

## Miscellaneous
