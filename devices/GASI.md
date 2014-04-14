KaiComm Galactic Asynchronous Serial Interface
----

```
		 |     \ Kai 
		>|     \\ Communications
		>|     \\\
		>|     ///
		>|    //
		 |   /

```

|     Item       |   Value    |   Comment
| -------------: | ---------- | ----------------
|    Vendor code | 0xA87C900E | (KaiComm)
|    Device type | 0x02       | (Communications)
| Device Subtype | 0x02       | (Async serial)
|      Device ID | 0x17       | (GASI)
| DCPU Device ID | 0x8BC9D6C7 | (KaiComm GASI 7)
|        Version | 0x010A     |

The KaiComm GASI is a bi-directional multiuse data port using a 3 wire connection.
Transmit and receive are independent, but must operate at the same baud.
The size of the data primitive is selectable ranging from 1 to 4 octets, but each octet is framed seperately on the wire.
All data are sent LSB first.

Commands
----

 - **0x0000**: Query status
   Sets Register A with status, bits will be set/clear as follows:
   - Bit 1 - Receiving Active (1), or idle (0)
   - Bit 2 - Data available (1), or not (0)
   - Bit 3 - Transmitting Active (1), or idle (0)
   - Bit 4 - Transmit buffer is free (1), or full (0)
   - Bit 5-13 Reserved always set to 0
   - Bit 14 - Receive interrupts enabled: yes (1), no (0)
   - Bit 15 - Transmit done interrupts enabled: yes(1), no (0)
 - **0x0001**: Configure port
   Register A holds options:
   - bits 1-2 - octet size minus 1.
   - bit 3 - send/use parity - yes (1), no (0)
   - bit 4 - parity mode (defines bit 5 function) - assert (1), make (0)
   - bit 5 - (parity assert mode) - mark (1), space (0)
   - bit 5 - (parity make mode) - odd (1), even (0)
   - bit 6 - stop bit count - two (1), one (0)
   - bit 7-15 - ignored.
   Register B holds the baud selection (b) in bits 1-3, range of 0-7; bit 5 Divides the baud by 3 (d)
   The baud is calculated like so: baud = 115200 / (2 ^ b)), and then divided by 3 if bit 5 is set.
   Possible bauds range from 300 to 115,200 bits per second.
   Each octet is sent with 8 bits, 1 start bit, 1 or 2 stop bits, and optionally a "parity" bit. (10-14 bits per octet)
 - **0x0002**: Receive data
   Register A is set to the lower 16 bits, register B is set to the upper 16 bits.
   Register C is set with the error status, receive error status is cleared when read.
   when configured for less than 4 octets unused bits are set to 0.
   Receive Error status codes:
   - 0x0000 - No error
   - 0x0001 - Receive buffer overflow
   - 0x0002 - Transmission framing error
   - 0x0003 - No data available
   - 0x0004 - Break
   - 0x0005 - Parity fail
 - **0x0003**: Transmit data
   Register A contains the lower 16 bits, register B contains the upper 16 bits.
   when configured for less than 4 octets unused bits are ignored.
   Register C is set with error status.
   Transmit Error status codes:
   - 0x0000 - No error
   - 0x0001 - Transmit buffer overflow (these data will not be sent)
 - **0x0004**: Configure interrupts
   Register A defines which interrupts to enable.
   - Bit 1 - Interrupt on Receive enabled: yes (1), no (0)
   - Bit 2 - Interrupt on Transmit done enabled: yes(1), no (0)
   - Bit 3-15 Ignored.
   Register B contains message for Interrupt on Receive.
   Register C contains message for Interrupt on Transmit.
 - **0x0005**: Configure interrupt on break
   Register A enables or disables.
   - Bit 1 - Interrupt on break received: enabled - yes (1), no (0)
   - Bit 2-15 Ignored.
   Register B contains message for Interrupt on break.
 - **0x0006**: Assert break
   Transmits the break condition.
   Register C is set with error status.
   Transmit Error status codes:
   - 0x0000 - No error
   - 0x0001 - Transmitter is busy

Connector Pinout
----

Signal    | Port pin
--------- | :----:
Tx Data   |   1
Common    |   2
Rx Data   |   3

----

2014 Meisaka Yukara (CC-BY-SA 4.0)