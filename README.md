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

## Memory

## Verification

## Miscellaneous
