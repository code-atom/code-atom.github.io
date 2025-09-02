---
title: Difference between Base64 and Base64URL
date: 2025-09-02 23:42:00 +05:30
categories:
- jwt
- web
tags:
- encoding
- general
layout: post
---

Base64 and Base64url are both encoding schemes used to represent binary data in an ASCII string format, but they differ in how they handle certain characters to ensure compatibility with specific environments, particularly URLs.

## **Base64**

- **Purpose:**
    
    Standard Base64 is a general-purpose encoding scheme for converting binary data into a text format that can be safely transmitted over channels designed for text, such as email or XML documents.
    
- **Character Set:**
    
    It uses 64 characters for encoding: A-Z, a-z, 0-9, `+` (plus sign), and `/` (forward slash). It also uses `=` (equals sign) for padding at the end of the encoded string to ensure the length is a multiple of 4.
    
    ```
                      Table 1: The Base 64 Alphabet
    
     Value Encoding  Value Encoding  Value Encoding  Value Encoding
         0 A            17 R            34 i            51 z
         1 B            18 S            35 j            52 0
         2 C            19 T            36 k            53 1
         3 D            20 U            37 l            54 2
         4 E            21 V            38 m            55 3
         5 F            22 W            39 n            56 4
         6 G            23 X            40 o            57 5
         7 H            24 Y            41 p            58 6
         8 I            25 Z            42 q            59 7
         9 J            26 a            43 r            60 8
        10 K            27 b            44 s            61 9
        11 L            28 c            45 t            62 +
        12 M            29 d            46 u            63 /
        13 N            30 e            47 v
        14 O            31 f            48 w         (pad) =
        15 P            32 g            49 x
        16 Q            33 h            50 y
    ```
    
- **Limitations in URLs:**The `+`, `/`, and `=` characters have special meanings in URLs and can cause issues if not properly percent-encoded, which adds complexity and increases the length of the URL.

### **Base64URL**

- **Purpose:** Base64url is a variant of Base64 specifically designed to be "URL-safe," meaning it avoids characters that are problematic in URLs.
- **Character Set:**It modifies the standard Base64 character set:
    - The `+` character is replaced with  (hyphen/minus).
    - The `/` character is replaced with `_` (underscore).
    - Padding with `=` is often omitted or handled implicitly, as its presence can also cause issues in some URL contexts. The length of the encoded string can be used to determine if padding would be needed during decoding.
    
    ```
       Table 2: The "URL and Filename safe" Base 64 Alphabet
    
     Value Encoding  Value Encoding  Value Encoding  Value Encoding
         0 A            17 R            34 i            51 z
         1 B            18 S            35 j            52 0
         2 C            19 T            36 k            53 1
         3 D            20 U            37 l            54 2
         4 E            21 V            38 m            55 3
         5 F            22 W            39 n            56 4
         6 G            23 X            40 o            57 5
         7 H            24 Y            41 p            58 6
         8 I            25 Z            42 q            59 7
         9 J            26 a            43 r            60 8
        10 K            27 b            44 s            61 9
        11 L            28 c            45 t            62 - (minus)
        12 M            29 d            46 u            63 _
        13 N            30 e            47 v           (underline)
        14 O            31 f            48 w
        15 P            32 g            49 x
        16 Q            33 h            50 y         (pad) =
    ```
    
- **Advantages in URLs:**By using  and `_` instead of `+` and `/`, and by handling padding differently, Base64url eliminates the need for additional percent-encoding when embedding encoded data directly within URLs, making them cleaner and easier to handle.

### **Summary**

The core difference lies in the substitution of two characters (`+` and `/` with `-` and `_` respectively) and the handling of padding, all to make Base64url suitable for direct inclusion in URLs without requiring further encoding. While standard Base64 is widely used for general binary-to-text encoding, Base64url is preferred when the encoded data needs to be part of a URL, such as in JSON Web Tokens (JWTs) or other URL-based data structures.

Reference: 

- [https://stackoverflow.com/questions/55389211/string-based-data-encoding-base64-vs-base64url](https://stackoverflow.com/questions/55389211/string-based-data-encoding-base64-vs-base64url)