             ...     ..            ..                              s
          .=*8888x <"?88h.   x .d88"                              :8
         X>  '8888H> '8888    5888R                 .u    .      .88
        '88h. `8888   8888    '888R        .u     .d88B :@8c    :888ooo
        '8888 '8888    "88>    888R     ud8888.  ="8888f8888r -*8888888
         `888 '8888.xH888x.    888R   :888'8888.   4888>'88"    8888
           X" :88*~  `*8888>   888R   d888 '88%"   4888> '      8888
         ~"   !"`      "888>   888R   8888.+"      4888>        8888
          .H8888h.      ?88    888R   8888L       .d888L .+    .8888Lu=
         :"^"88888h.    '!    .888B . '8888c. .+  ^"8888*"     ^%888*
         ^    "88888hx.+"     ^*888%   "88888%       "Y"         'Y"
                ^"**""          "%       "YP'

                  An Erlang external term encoder and decoder.

DESCRIPTION
============
This module is inspired by the BERT specification but does not adhere to it.
Instead it adheres to the "Erlang external term format". I named it similar to
BERT because I started off with BERT and the name is great!

I am using this to send and receive messages from an Erlang server over
WebSockets at [https://github.com/juster/piroxy].

TYPE CONVERSION
================

Remember that...

1. In Erlang a string is equivalent to a list with integer elements.
   Every element does not exceed the maximum value of a byte. Additionally the
   number of elements must fit in an unsigned 16-bit integer.
2. Erlang supports arbitrarily large integers by default. With JavaScript
   numbers are stored as floating point in memory and can be considered
   integers if Number.isInteger(x) == true. The BigInt class provides support
   for arbitrarily large integers but must be explicitly used.
3. Maps were added to Erlang but they are more like the Map class in JavaScript
   than like an Object. Objects can only have strings as keys whereas maps can
   have any value type as keys.
4. Arrays in JavaScript are not implemented like arrays in C. They do not
   guarantee O(1) access and so here Arrays are interchangable with Erlang
   lists.
5. ArrayBuffers are implemented like arrays in C. Uint8Arrays are
   interchangable with STRING_EXT because their values are byte-sized.

JavaScript                          Erlang
----------                          ------
Number.isInteger(x)                 INTEGER_EXT
typeof x == "number"                FLOAT_EXT
x instanceof BigInt                 LARGE_BIG_EXT
x instanceof Uint8Array             BINARY_EXT
typeof x == "string"                STRING_EXT
|| ({list:y}
    && y instanceof Uint8Array
    && y.length <= 65535)
{list:[]}                           NIL_EXT
{list:[...]}                        LIST_EXT
{tuple:[...]}                       SMALL_TUPLE_EXT or TUPLE_EXT
typeof x == "boolean"               ATOM_EXT (Erlang's true/false)
{atom:"true"}*                      "
{atom:"false"}*                     "
({atom:str}                         SMALL_ATOM_UTF8_EXT or ATOM_UTF8_EXT
 && typeof str === "string")
x instanceof Map                    MAP_EXT

* This breaks the one-to-one relationship between encoding and decoding!
  An error may be required when encoding...

TODO
=====
 * Convert to asm.js spec for increased performance.
 * Complicated symbol table sharing like a true Erlang node?

REFERENCES
===========
 * BERT: http://bert-rpc.org/
 * Erlang's external term format: http://erlang.org/doc/apps/erts/erl_ext_dist.html

LICENSE
========
The Erlang external term format is Copyright (c) 2020 Ericsson AB.
This software is licensed under the Mozilla Public License 2.0.

AUTHOR
=======
Justin "juster" Davis <jrcd83@gmail.com>
