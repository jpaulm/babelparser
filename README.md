# The BabelParser

The BabelParser is a low-level parsing concept that supports its own graphical notation. It was invented in the UK many 
years ago, and I have since ported it to just about every environment I've ever worked in.

It uses an input stream, and an output stream, each of which has its own pointer. As each character in the input is 
scanned off and recognized successfully, it is optionally copied to the output stream, and the input pointer (also optionally) steps to point at the next character in the input stream.  It is unusual to suppress stepping the input pointer, but this can be useful.

The most common operation is a "character comparator", although there are also "class comparators" (letters and/or numbers), 
and the "universal comparator", which is always true (except at end of file). The default action is to copy a recognized character to the output, but this can be overridden by "I-modification" (the input pointer is not stepped) or "O-modification" (where copying does not take place) or both. If both, O-modification is tested for before I-modification.  If the comparator fails, neither pointer is moved.

In Babel diagrams, a comparator is represented by a circle or a square, and a true result by a line exiting to the right, and 
a false result by a line exiting downwards. The universal comparator when unmodified is essentially a "copy", while with O-modification, it is a "skip" - I have used these names in the code.

Suppose I want to test for A, B or C, followed by 0 or more blanks (which are not be copied to the output stream), this can be 
represented as follows (I'll have to use squares for everything, and 'b' for blank):

```
            
                         *--------------*
                         |              | 
        *-----*          V   *-----*    |
  ----->|  A  |-----*------->|  b  |----*
        *-----*     A        *-----*
           |        |           |  O-mod
        *-----*     |           |            
        |  B  |-----*           *----------->  we have found the first non-blank
        *-----*     |
           |        |
        *-----*     |
        |  C  |-----*
        *-----*
           |
           X
           
```           

On completion of this scan, the output stream will contain A, B or C, and the output pointer will be positioned after 
this letter.  Any consecutive blanks will be skipped, up to the first non-blank.

If none of A, B, or C was found at the position marked by the input pointer, the logic will proceed to the code "below" the 
test for 'C' (marked with an X).

One more example: we want to test if the current character is a '<', and, if it is, copy all the following characters to 
the output stream, up to but not including the following '>'. I will use 'U' for the universal comparator.     

```
                         
        *-----*                   *-----*      
  ----->|  <  |-----------*------>|  >  |------ we have found the '>' and are 
        *-----*           |       *-----*               positioned just beyond it,
           |  O-mod       |          |  O-mod           and the intervening characters
           |              |          |                  are in the output stream
        not a '<'         |       *-----*
                          |       |  U  |-----*  (remember the universal comparator
                          |       *-----*     |       is always true - so without 
                          |                   |       O-mod, it is a 'copy') 
                          *-------------------*

```

An example of the use of BabelParser can be found in https://github.com/jpaulm/parsefbp .
