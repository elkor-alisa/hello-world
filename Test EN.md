# Basic features of Unicode. Which Unicode Transformation Format to choose?

## Why do we need encoding

A computer program is a set of information that is stored in bytes, units of information processing. 1 byte consists of 8 bits.

People don’t read bytes so we can understand the code only if it is translated into a human language. This means we need to turn some set of bytes into a set of characters.

The process of converting bytes into characters is called **decoding**, and the reverse process is called **encoding** respectively. 

Компьютерные программы обычно пишутся не для одиночного использования, поэтому ими пользуются разные люди разных национальностей, которые говорят на разных языках. Из этого следует, что нам очень важно знать, как именно закодированы строки нашей программы, иначе мы не сможем правильно их интерпретировать или показать пользователю.

Поэтому нам нужны стандарты кодирования, чтобы сопоставлять символы алфавита байтам.

## First standardization attempts

**ASCII** was one of the first standards for the characters of the Latin alphabet. This is a table of 128 characters, each character corresponds to a binary code. For the Latin alphabet, numbers from 32 to 127 were used, symbols up to 32 were used for service marks.

The principle of work was the following: the software memory reserved 1 byte (or 8 bits) of information to match 1 character. However, table codes stored only 7 bits of information because 128 characters equal 2⁷. The remaining bit was used for control: it was easy to understand whether this separate byte was single or a part of a sequence.

Most computers used 8 bits registers, so, with ASCII, it was not only easy to store in memory any ASCII character, but even to save the extra bit.
If the Latin alphabet was the only one in the world, everything would have ended there. But people speak different languages and use different alphabets, so there was a need in the other coding standards. They did not match with ASCII, and if it was necessary to transfer strings of a program to another PC, a lot of decoding problems started to appear.

Finally, it was decided to make a standard that would unite all possible coding standards into one, so that no one ever had such problems. This is how **Unicode** appeared — a coding standard with an ambitious goal to cover all possible characters in the world.

## One standard to rule them all

Unicode is a huge set of graphic characters called a **Universal Character Set** (UCS), which provides positions from 0 to 1 114 111. The characters are organized in accordance with their usage frequency along **coding planes**. There are 17 planes, each may contain 65 536 characters. The first basic plane includes the world most commonly used characters. Several planes are considered additional and are mainly used for historical and rarely used hieroglyphs, as well as for control characters. There are also empty planes not yet filled, and the planes for private use.

In ASCII, the character was mapped to a set of bits that can be stored in memory. In Unicode, the symbol is mapped to an abstract **code point** of the U format + a number in hexadecimal. This is the main difference between ASCII and Unicode.

Unicode is updated regularly, new characters are added as they appear. Currently, version 12.1 uses 137 994 code points.

Since the code point does not specify bytes, it is just the position of some character in a large Unicode table, we need **Unicode Transformation Format** (UTF) to encode a sequence of code points into bytes according to its rules.

There are several UTF formats, the choice of a particular one depends on how you want to organize the representation of characters in memory. 

All formats can encode the same Unicode character set, but each character in a different encoding will represent a different sequence of bytes.


## Unicode Transformation Formats

Let’s look at the two well-known Unicode formats and sum up what their pros, cons and major differences are.

### UTF-16

When Unicode first appeared, the encoding with a fixed width of the machine word was used. It was called _UCS-2_ and it used 2 bytes for each character. Using UCS-2, 65 536 code points could be displayed, i.e. 2¹⁶, and that was enough for a basic plane.

Later, however, additional historical symbols were included in Unicode. They need more code points than UCS-2 could provide. By this time, a lot of software products with 16-bit string registers libraries appeared, for instance, Java and Windows NT. Therefore, the **UTF-16** was introduced in order to provide these platforms with an option to use additional characters without changing the UCS-2 approach.

By using the UTF-16, each code point is encoded with either one or two 16-bit code units. Thus, UTF-16 is the encoding with a variable length of a machine word. If a character to be encoded belongs to the plane of additional characters, UTF-16 uses so-called **surrogate pairs** — combinations of two bytes. Combinations use the numeric range of the basic plane but are guaranteed by the Unicode standard as invalid as basic ones. Therefore, a code point in UTF-16 can be represented in either 16 or 32 bits, depending on the type of a character to be encoded.

Since a UTF-16 character is one or two pairs of bytes, it is also important to understand in which sequence they follow each other. The correct character decoding depends on this.

To determine this, the **Byte Order Mark** is used. This is a U+FEFF tag — it does not encode any character in Unicode, but is used specifically for this purpose.

If when reading a string it turned out U+FEFF, it means that the byte order is direct and the high surrogate goes first, so the encoding is called UTF-16BE (big-endian). If U+FFFE was considered, then the order is reversed and the low surrogate goes first, so the encoding is called UTF-16LE (little-endian).


#### Pros

If you have to work mostly not with the Latin alphabet, this encoding is more profitable, since it uses 2 bytes per character more often. For example, in UTF-8, a non-standard character requires up from 3 bytes and more.

#### Cons

This is a variable-length encoding, so we can’t accurately calculate the size of the string.

You need to know the byte order for proper decoding.

### UTF-8

As we remember, the UTF-16 uses 16 bits per character. If you use the Latin alphabet, whose characters are situated in the plane of the most commonly used, then with this encoding you will have a lot of zeros in one word. They can not be removed as they stand for characters code points and at the same time these zeros take up extra space in memory.

Therefore, **UTF-8** encoding appeared. It is compatible with ASCII and for each code point with numbers from 0 to 127 only 1 byte is required. More bytes are required only for those code points positions that start at 128 and higher.

This encoding suited well those software and network protocols that used 8-bit string register. Thanks to UTF-8, they had the opportunity to support Unicode and not to waste memory with wide 16-bits characters.

A character in UTF-8 takes from 1 to 4 bytes depending on its code plane. For Latin characters only 1 byte is required, European characters take 2 bytes, and you need at least 2 bytes and at most 4 bytes for Asian characters.

#### Pros

Для работы с символами из ASCII использование UTF-8 выгоднее с точки зрения памяти. 

Не нужно знать порядок байтов.

#### Cons

In all other cases, UTF-8 may require more memory than UTF-16. If you work with large amounts of text, it can affect efficiency.

This is also a variable-length character encoding, which makes it difficult to manipulate the string.

### To Sum Up

#### UTF-8

- Does not require byte order knowledge
- Uses from 1 to 4 bytes per character
- Compatible with ASCII

#### UTF-16

- Requires byte order analysis 
- Uses 2 or 4 bytes per character
- Incompatible with ASCII

## What to choose?

The choice of UTF is often determined by your working environment.

For example, Windows uses UTF-16 internally, and UTF-8 is recommended by HTML5.
 
Most likely, if you do not plan to use complex Asian, historical or musical characters and are not limited to the requirements of a programming language or a platform, then there is no need for UTF-16.

Obviously, UTF-8 is more commonly spread. If there are no complex characters, UTF-16 can be assumed as more predictable so that the length of each character will be 16 bits. But in comparing with UTF-8, it will use more memory and still have those problems with a variable character length.
