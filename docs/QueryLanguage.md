# QueryLanguage

The Subscription Query Language is a simple object type based language to define object
updates being interested in.

The syntax is defined to be easily understandable, parsable and writeable, and follows
the following basic rules:

```json
<identifier>: <predicate>, <anotheridentifier>: <anotherpredicate>
```

The term _identifier_ in this case is the actual property type of interest, e.g. _device_
or _customer_, followed by a colon and then the value definition.

Value definitions are one of the following types:

A selector of _*_ (asterisk) selects all possible values. It's the strongest wildcard,
and returns true for all matchers.

```cquery
device: *
```

The _*_ selector can also be used by itself as a simple "select all" selector, without
providing any specific identifiers. In this case, however, no further selection can be
provided.

A string selector is always enclosed in starting and terminating _"_ (double quotes).
Single quotes are not supported for strings, that said, double quotes inside the string
need to be escaped with a _\_ (reverse backslash).

A floating point value is a float64 representation, either positive or negative. Decimal
separator is always a dot (not comma) and leading zeros can be omitted.

An integer value is an int64 representation, either positive or negative. Leading zeros
are allowed.

A list is a simple container to collect a group of sub selectors to select multiple
elements at the same time, e.g. multiple device ids.

A simple example to select two devices based on their UUIDs:

```cquery
device: [ 066c9fbf-6799-47b8-90b9-fabc113c90a9, 47abe0cd-87ac-4a3c-9bb0-909babe9939a ]
```

In addition multiple object types can be chained into a single query to select elements
with more than one property. This can be achieved by separating the different query
parts by a comma.

The following example would select all devices of a single barn, represented by the
given UUID. 

```cquery
device: *, barn: 066c9fbf-6799-47b8-90b9-fabc113c90a9
```

UUIDs must be represented as an UUID literal, as defined by the
[RFC 4122 Section 3](https://tools.ietf.org/html/rfc4122#section-3 ':target=_blank')
with 32 hexadecimal digits (base-16) and 5 groups separated by hyphens, in the form of
8-4-4-4-12.

## Available Query Identifiers

To build a query, the following identifiers are defined:

| Identifier | Expected Datatype(s)| Required | Description |
| :--- | :--- | :--- | :---|
| device | UUID, LIST[UUID], * | true | Predicate to select a single, a list of, or all available devices. |
| customer | UUID, LIST[UUID], * | false | Predicate to select a single, a list of, or all available customers. |
| value-type | UUID, LIST[UUID], * | false | Predicate to select a single, a list of, or all available value types. |

The _device_ query identifier must be sent with all queries, building the base of the
selection algorithm. The basic query can be as simple as: _device: *_.

The only exception from this rule is the simple "select all" predicate, which consists of
the _*_-sign (asterisk) only, without any identifier.

## Query Language Grammar

The query language grammar is defined as the following pseudo grammar definition:

```antlr4
WHITESPACE: [ \r\n\t]+ -> skip ;

fragment Letter: [a-zA-Z]+ ;
fragment HexDigit: [0-9a-fA-F] ;
fragment HexOctet: HexDigit HexDigit ;
fragment OctalDigit: [0-7] ;
fragment ZeroToThree: [0-3] ;
fragment DigitNoZero: [1-9] ;
fragment Digit: '0' | DigitNoZero ;
fragment NaturalNumber: DigitNoZero Digit* ;
fragment Character: ~['"\\] | EscapeSequence ;
fragment EscapeSequence: '\\' [btnfr"'\\] | '\\' OctalDigit | '\\' OctalDigit OctalDigit | '\\' ZeroToThree OctalDigit OctalDigit ;
fragment DoubleHex: HexOctet HexOctet ;
fragment QuadHex: DoubleHex DoubleHex ;
fragment SextetHex: DoubleHex DoubleHex DoubleHex ;

MINUS: ('-') ;
GT: '>' ;
LT: '<' ;
GE: '>=' ;
LE: '<=' ;
ASSIGN: '=' ;
WILDCARD: '*' ;

IDENTIFIER: Letter (Letter | '-')* Letter ;
TERMINAL: ('"' Character* '"') | ('\'' Character* '\'') ;
FLOAT: ('0' | NaturalNumber)? '.' Digit Digit* ;
INTEGER: Digit+ ;
UUID: QuadHex '-' DoubleHex '-' DoubleHex '-' DoubleHex '-' SextetHex ;

query:
    '*'                                                 # selectAll
    | (elementRule (',' elementRule)* EOF)              # elements
;

floatRule:
    MINUS? FLOAT
;

integerRule:
    MINUS? INTEGER
;

elementRule:
    identifier=IDENTIFIER ':' predicate=predicateRule
;

uuidRule:
    uuid=UUID
;

wildcardRule:
    WILDCARD
;

stringLiteralRule:
    TERMINAL
;

predicateRule:
    listRule                                            #list
    | objectRule                                        #object
    | floatRule                                         #float
    | integerRule                                       #integer
    | uuidRule                                          #uuid
    | wildcardRule                                      #wildcard
    | stringLiteralRule                                 #string
    | comparisonRule                                    #comparison
;

listElementsRule:
    (floatRule (',' floatRule)*)                        #floats
    | (integerRule (',' integerRule)*)                  #integers
    | (uuidRule (',' uuidRule)*)                        #uuids
    | (stringLiteralRule (',' stringLiteralRule)*)      #strings
;

listRule:
    '[' listElementsRule? ']'
;

objectRule:
    '{' query '}'
;

comparisonRule:
    comp1=comparisonRule '&&' comp2=comparisonRule      #and
    | comp1=comparisonRule '||' comp2=comparisonRule    #or
    | operatorRule                                      #operator
;

operatorRule:
    op=(GT | LT | GE | LE) floatRule | integerRule
;
```
