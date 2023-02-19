# Welkin


## Syntax

The Welkin language is defined by the following EBNF notation:
```
  <expression> = (<statement> | <connection> )*

  <statement> = '#' <identifier> '\n'
  | 'use' <inclusion> 
  | 'alias' identifier 'for' identifier
  | <socket>* <visability>? <declaration> 
  <socket> = ('>' | '>>' | '<' | '<<') <socket_num>
  <socket_num> = num | '(' operation ')'
  <visibility> = ('public' ('file' | 'current_dir')) | 'protected'?
  <declaration> = 
  | <atomic_declaration>
  | 'unit' <unit> <derives>? <operation>  
  | tuple' <unit>? <tuple> 
  | "list" <unit>? <list>
  | "layer" <unit>? <derives>? <layer>
  | "extends" <unit> <derives>? <layer>
  <derives> = ':' (<term> ',')* <term>
  
  <term> = '(' <term> ')'| <atom> | <tuple> | <list> | <operation> | <field> | <inclusion> | <connector>
  | <connection> | <layer>

  <layer> = '{' (<statement> | <connection>)* '}'
  <connection> = <derives> | <term>? <connector> <term>?
  
  <connector> = <delimiter> <term> <delimiter>
  <inclusion> = <unit> ('::*' | '::' <unit> | '::{' (<unit> | <inclusion>), ',')* (<unit> | <inclusion>) '}')
  <field> = <unit> '.' ( <unit> | '(' operation ')')
  <operation> = '('? (<term> <tuple> | <term> <unit>+ | <term> <unit> <term>) ')'?
  <list> = '[' (<term>, ',')* <term>? ]') 
  <tuple> = '(' (<term>, ',')* <term>? ')'
  
  <atomic_declaration> = 'unit' <unit>
  | 'string' <unit>? <string> 
  | 'num' <number> | 'delimiters' <unit>? <delimiter> ',' <unit>? <delimiter> 
  | 'operator' <unit>? <operator>
  <atom> = <unit> | <string> | <number>
  <number> = (+ | -)? Numeral* ('.' Numeral* | <exponent> ('+' | '-')? )?
  <exponent> = 'e' | 'E'
  <int> = Numeral*
  <string> = '"' StringCharacter* '"' | ''' StringCharacter* '''
  <unit> = Character+
```
In the above grammar, 
* StringCharacter is any Unicode character;
* Numeral is any valid Unicode numeral;
* Character is any Unicode character that is not a space, escape sequence, or any of
```
[] {} () , . _ \ : ;
```
Implicitly, the first two characters may not be -, +, or a Numeral. This ensures that numbers can be properly parsed.
* Character/Numeral 
Foot Note: Technically, Character is defined as any alphanumeric, Posix graph, and Emoji characters in Unicode. If you would like support for additional characters, please post an issue.

The "extends" keyword can be used to extend a layer in a different file or within a layer. There are minimal restrictions to how this can be used: 
* Any layer with the "fixed" keyword cannot be extended.

Informally, comments are given by the following:
- single line comments start with '//';
- multiline comments are enclosed in '/* */'.

Except for strings and spaces between expressions, all expressions in Welkin are not whitespace sensitive.  In particular, any expression may be written on any line. This is similar to [Swift's](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics/) syntax, except operations may be put on multiple lines. However, there are guidelines to minimize the amount of white space in most documents; see the idioms below.

## Semantics

Each file defines its own layer based on its given name. In case two files share the same name, namespaces are generated based on the earliest file created. 

Automatically, the following symbols have a set interpretation

Due to these interpretations, these operators are reserved terms for their respective types. However, it is possible to work around this using units as wrappers (see [Units as General Wrappers]).

In case any declaration does not have a unit (as an identifier), the following procedure is used:
- 
- Delimiters must be imported from a layer or file (or are part of the stdlib)


## Semantics
(TODO: use abstract and JSON semantics for ease of use.)
Ultimately, these semantics are used to create a JSON file, which can be closely analyzed, stored, and easily shared with others. (In fact, JSON files can be parsed alongside a welkin file; see the 'Vault' section for more information). The next set of bullet points explain how each expression is stored.
For atoms:
- Every num is converted to a floating point number.
- Every string is stored as a string.
- Every unit, operator, and delimiter is stored as an object.
For terms:
- Every tuple is stored as a regular tuple in JSON
- Every list is stored as a regular list in JSON
- Every operation is an ordered pair: the operator followed by a list, containing either one or two operands
- Every layer is stored as an object. If , then the contents of this layer will be put into the pre-existing object instead.
For the remaining keywords:
- Through the 'use' keyword, any terms from a layer are copied and put into any layers using the term. Within the main algorithm, each  is put directly into the JSON file
- Each socket is an object and is put into the corresponding layer (or object). 

## Vault

A vault keeps track about all of the Welkin files in a directories, any JSON based dependencies, and . If the JSON file is not recognized by the program, an error will identify the main issue.


## Recommendations
The Welkin idioms are still in development (see ), and you may choose to use them as desired. Some pertinent ones are:
- Use unique names when defining Welkin files.
- 


layer Tortoise {

}

layer Hare {


}

layer Plot {
 
  unit lesson
  list characters [Tortoise, Hare]

  Tortoise -(beats)-> Hare

  layer lesson {
    
  }

}
