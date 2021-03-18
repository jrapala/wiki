# Digital Logic



**Binary System**: The communication a computer uses, also know as a base-2 numeral system. On = 1. Off = 0. Transistors allow electrical signals to pass through.

**Logic Gate**; Allow transistors to do more complex tasks, like decide where to send electrical signals depending on logical conditions.

**Bit**: A binary digit. We group 8 of these together (a **byte**) because 2^8 offered a large enough range of numbers to do the computing we needed. 

**Byte:** A group of 8 bits. Each byte can store one character. We have 256 possible values (2^8) for 255 decimal numbers..

**Character Encoding:** Used to assign binary values to characters, so humans can read them.

**ASCII**: The oldest character encoding. Only 127 values needed.

```
01101000 - h
01100101 - e
01101100 - l
01101100 - l
01101111 - o
```

**UTF-8**: The most prevalant character encoding used today. It is built off of the Unicode standard. It uses the ASCII tables and lets us a variable number of bytes, for things like emoji.

**RGB Model**: Three characters used to represent every color.



### Counting in Binary

The number `10`:

|  0   |  0   |  0   |  0   |  1   |  0   |  1   |  0   |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| 2^7  | 2^6  | 2^5  | 2^4  | 2^3  | 2^2  | 2^1  | 2^0  |
| 128  |  64  |  32  |  16  |  8   |  4   |  2   |  1   |



The letter `h` (ASCII) (`104` in decimal):

|  0   |  1   |  1   |  0   |  1   |  0   |  0   |  0   |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| 2^7  | 2^6  | 2^5  | 2^4  | 2^3  | 2^2  | 2^1  | 2^0  |
| 128  |  64  |  32  |  16  |  8   |  4   |  2   |  1   |



### Data Sizes

From smallest to largest:

- **Bit:** Holds one binary digit
- **Byte:** Holds 8 bits (e.g. a character)
- **Kilobyte (KB)**: Holds 1,024 bytes (2^10 - base 2 number system)
- **Megabyte (MB)**: Holds 1,024 KB
- **Gigabyte (GB)**: Holds 1,024 MB
- **Terabyte (TB)**: Holds 1,024 GB



