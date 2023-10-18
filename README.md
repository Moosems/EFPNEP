# Enhanced Floating Point Number Encoding Protocol

## Introduction
The Floating Point Number Encoding Protocol represents floating point numbers using a character-based system, with each character encoded into 5 bits. The character set includes the digits 0-9, a decimal point ('11111'), a repeating sign (`10101`), and a minus sign ('10000'). This protocol is designed to ensure precise encoding and decoding while optimizing bit usage.

## Writing and Padding:
To encode a floating point number, each digit and special character is mapped to its respective 5-bit code, as previously explained. When adding padding bits to ensure that the representation is a multiple of 8 bits, it is crucial to do so without altering the value. In cases where a character code contains '5' (e.g., '7'), the padding bits should be added after the last complete character. If necessary, adjust the encoding of the last character to maintain the correct value while achieving the desired bit alignment.

## Handling Padding Length:
After adding the padding bits, an additional 8-bit integer is used to specify the number of padding bits added. This 8-bit integer encodes the length of the padding bits, allowing precise identification and removal of both the padding bits and the extra encoding used to represent the padding length. This approach optimizes bit usage and ensures perfect precision in your floating point number encoding scheme while minimizing waste.

## Arithmetic Operations

### Addition
- Decimal points of both numbers are aligned.
- Addition is performed using the 5-bit codes, ensuring the carryover is handled correctly.
- Padding bits are considered, maintaining alignment.
- The 8-bit segment for padding length is adjusted as needed.

### Subtraction
- Decimal points are aligned.
- Subtraction is carried out as in regular decimal numbers.
- Borrowing is managed in the conventional way.
- Padding bits are accommodated without affecting the value.
- The 8-bit segment for padding length is adjusted as required.

### Multiplication
- Multiplication proceeds as in regular decimal numbers.
- Decimal point positions are tracked and added to the result.
- Padding bits and alignment are considered.
- The 8-bit segment for padding length is adjusted as necessary.

### Division
- The floating point numbers are converted into regular decimal numbers.
- Division is performed as with decimal numbers, tracking decimal point positions.
- The result is rounded to the desired precision, often 5 points.
- The 8-bit segment for padding length is adjusted to account for precision.
- The result can be encoded back into the protocol if needed.

## Precision
In cases of repeating decimals, precision is maintained up to a specified number of repeats, typically 2 followed by a repeating decimal sign indicating that the last two smallest repeats found go on inifinitely. This means that the protocol uses only 2 repetitions and one extra character to save bits but also never loses perfect precision.

## Examples:
Here are some examples of the system in practice:

1. Encoding the number `-3.14`:
    - `-3.14` contains all possible special characters, normal characters, padding bits, and the padding integer. The achieved number in practice would be `10000 00011 11111 00001 00101 0000000 00000111`.
    - In this example we have the base numbers (`10000 00011 11111 00001 00101`), seven padding bits (`0000000`), and an 8 bit integer indicating that the padding bits are seven in length (`00000111`).
2. Decoding `1000000011111110000100101000000000000111`:
    - Given the number `1000000011111110000100101000000000000111` the first task would be to retrieve the trailing integer and remove the padding bits. The last eight bits are `00000111` indicating seven bits. Remove the integer and padding bits one is now left with `1000000011111110000100101`. From here the number is split into groups of 5 bits such that the program has `10000 00011 11111 00001 00101`. Decoding each characters individually and appending it to a String-like type made for this protocol gives `-3.14`.
3. Addition:
    - Small numbers:
        ```
         .2
        +.1
        ---
         .3
        ```
    - Larger numbers:
        ```
         11.10
        + 2.22
        ------
         13.32
        ```
4. Subtraction:
    - Small numbers:
        ```
         .2
        -.1
        ---
         .1
        ```
    - Larger numbers:
        ``` 
         11.10 (borrow over everything)
        - 2.22
        ------
          8.88
        ```
5. Multiplication:
    - Small numbers:
        ```
         .2
        *.1
        ---
          |
          V
            .2
        / 10.0
        ---
           .02
        x 1.0
        ---
           .02
        ```
    - Larger numbers:
        ```
         11.10
        * 2.22
        ------
          |
          V
         1110
        * 222
        ------
          2220
        + 22200
        + 222000
        --------
          246420
        ```
6. Division:
    - Small numbers:
        ```
          .2
        ÷ .1
        ----
          |
          V
         2.0
        ÷ 1.0
        -----
         2.00
        ```
    - Larger numbers:
        ```
         10.0
        ÷ 3.0
        -----
          |
          V
             3.33
          .______
        3 | 10.00
           - 9 ||
           --- v|
             1 0|
            -  9|
             ---v
               10 (stop, 10 is repeating and decimal point is not changing)
        
        Binary return would be `00011 11111 00011 00011 101010 0000000 00000111`
        ```

## Benefits
This protocol has one major benefit:
1. The protocol is far more precise as it doesn't use true floating point numbers but rather handles the numbers similar to strings and performs operations as one would in grade school. Not only this, but when there are repeating decimals, the protocol is also able to handle them and maintain precision. 

## Drawbacks
This protocol has two major drawbacks:
1. The protocol is memory inneficient. The protocol uses 5 bits to represent each character and by treating numbers like a string, the protocol is not memory efficient. This is especially true when dealing with large numbers.
2. The protocol is slow. This is because it has to deal with the number like a string and has to perform operations on each character.

## Conclusion
The protocol ensures precise encoding and decoding while minimizing wasted bits by optimizing bit usage. This is achieved by aligning decimal points and adjusting padding bits as needed. The protocol can be used to perform basic arithmetic operations, adhering to the encoding rules and ensuring consistency and accuracy in mathematical operations.
