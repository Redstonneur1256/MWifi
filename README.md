# MWifi

### Memory cells:
* cell1: Control
* cell2: Sending
* cell3: Receiving

- cell1[0]: Self processor IP address: 32 bit number allocating around 2 billion addresses, if zero it will be set to the packed position of the processor, `((x & 0xFFFF) << 16) | (y & 0xFFFF)`
- cell1[1]: Action control (see actions)
- cell1[2]: Connection status (see status)

### Actions:
* 0: Idle
* 1: Connect to an address
  Parameters:
  - cell1[63] IP Address to attempt to connect
  - cell1[62] Flare bind timeout in milliseconds: (default 10000)
  - cell1[61] Locate timeout in milliseconds: (default 5000)

### Status:
* 0: Disconnected, no connection were attempted
* 1: Connected to the previous specified IP address
* 2: Failed to connect: Timed out

### Internal structure to open a connection:
A single flare is bind by the main router and then flagged with the command 0

- Main processor wait for the common flare command (bits 33-40) to be at 0
- Main processor flags the flare with the command `0x01` with the bits 0-32 being the IP of the target processor
- Other processors read the command, check the IP if its the same then flag the flare with command `0x02` and the bits 0-32 being the packed position of target memory cell
- Main processor unpack the position, move the flare to position to get the block and then flag the flare with command `0x00`

### Internal command structure
```
|           1          |2|     3   |                4               |
 0000000000000000000000 1 000000000 00000000000000000000000000000000
```
- 1: Bits 64-42: Unused
- 2: Bit     41: Common bit used to identify flare
- 3: Bits 32-40: Command ID
- 4: Bits  0-31: Command parameters
