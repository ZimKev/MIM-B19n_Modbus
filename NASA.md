# Samsung NASA Protocol

This document describes the structure of Samsung NASA messages as used in various Samsung HVAC systems.

## Physical layer

Messages are transmitted via a RS-485 link with 9600 baud, even parity.

## Packet structure

| Byte | Description | Value | Note |
| ---- | ----------- | ----- | ---- |
| 0    | Packet Start | 0x32 | |
| 1, 2 | Packet Size | 16bit | `int size = (int)data[1] << 8 \| (int)data[2];` `size + 2 == data.size();` |
| 3 | Source Adress Class | Address Class Enum | Outdoor = 0x10, HTU = 0x11, Indoor = 0x20, ERV = 0x30, Diffuser = 0x35, MCU = 0x38, RMC = 0x40, WiredRemote = 0x50, PIM = 0x58, SIM = 0x59, Peak = 0x5A, PowerDivider = 0x5B, OnOffController = 0x60, WiFiKit = 0x62, CentralController = 0x65, DMS = 0x6A, JIGTester = 0x80, BroadcastSelfLayer = 0xB0, BroadcastControlLayer = 0xB1, BroadcastSetLayer = 0xB2, BoradcastCS = 0xB3, BroadcastControlAndSetLayer = 0xB3, BroadcastModuleLayer = 0xB4, BoradcastCSM = 0xB7, BroadcastLocalLayer = 0xB8, BroadcastCSML = 0xBF, Undefiend = 0xFF |
| 4 | Source Channel | 8bit | |
| 5 | Source Address | 8bit | |
| 6 | Destination Address Class | Address Class Enum | See source Address class |
| 7 | Destination Channel | 8bit | |
| 8 | Destination Address | 8bit | |
| 9 | Packet Information | `packetInformation = ((int)data[index] & 128) >> 7 == 1` | |
| 9 | Protocol Version | `protocolVersion = (uint8_t)(((int)data[index] & 96) >> 5);` | |
| 9 | Retry Count | `retryCount = (uint8_t)(((int)data[index] & 24) >> 3);` | |
| 10 | Packet Type | `packetType = (PacketType)(((int)data[index + 1] & 240) >> 4);` |  StandBy = 0 Normal = 1, Gathering = 2, Install = 3, Download = 4 |
| 10 | Data Type | `dataType = (DataType)((int)data[index + 1] & 15);` | Undefined = 0, Read = 1, Write = 2, Request = 3, Notification = 4, Response = 5, Ack = 6, Nack = 7 |
| 11 | Packet Number | 8bit | Increasing packet number |
| 12 | Capacity (Number of Messages) | 8bit | |
| 13, 14 | Message Number | `messageNumber = (uint32_t)data[index] * 256U + (uint32_t)data[index + 1]; type = (MessageSetType)(((uint32_t)messageNumber & 1536) >> 9);` | `messageNumber` as seen in the S-NET NASA.ptc file; type: Enum = 0 (1 byte payload), Variable = 1 (2 bytes payload), LongVariable = 2 (4 bytes payload), Structure = 3 (all following bytes until the end of the packet, minus 3 end bytes; when a packet contains a structure, it does not contain any other messages. Therefore the capacity will be 1.) |
| 14 + 1, 14 + 2, ... | Message Payload | (size as derived from the Message Number) |
| ... | Iterate over Capacity to retrieve all messages | |
| -3, -2 | CRC 16 | 16bit | `uint16_t crc_actual = crc16(data, 3, size - 4); uint16_t crc_expected = (int)data[data.size() - 3] << 8 \| (int)data[data.size() - 2];` |
| -1 | Packet End | 0x34 | |

## Credits

Thank you to lanwin, who implemented [esphome_samsung_ac](https://github.com/lanwin/esphome_samsung_ac/tree/main) and was the first to understand the structure of the NASA Messages.
