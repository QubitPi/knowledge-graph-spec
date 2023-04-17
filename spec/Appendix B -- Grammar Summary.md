# B. Appendix: Grammar Summary

## Source Text

SourceCharacter :: "Any Unicode scalar value"

## Ignored Tokens

Ignored ::

- UnicodeBOM
- WhiteSpace
- LineTerminator
- Comment
- Comma

UnicodeBOM :: "Byte Order Mark (U+FEFF)"

WhiteSpace ::

- "Horizontal Tab (U+0009)"
- "Space (U+0020)"

LineTerminator ::

- "New Line (U+000A)"
- "Carriage Return (U+000D)" [lookahead != "New Line (U+000A)"]
- "Carriage Return (U+000D)" "New Line (U+000A)"

Comment :: `#` CommentChar\* [lookahead != CommentChar]

CommentChar :: SourceCharacter but not LineTerminator

Comma :: ,

## Lexical Tokens

Token ::

- Punctuator
- Name
- IntValue
- FloatValue
- StringValue

Punctuator :: one of ! $ & ( ) ... : = @ [ ] { | }

Name ::

- NameStart NameContinue\* [lookahead != NameContinue]

NameStart ::

- Letter
- `_`

NameContinue ::

- Letter
- Digit
- `_`

Letter :: one of

- `A` `B` `C` `D` `E` `F` `G` `H` `I` `J` `K` `L` `M`
- `N` `O` `P` `Q` `R` `S` `T` `U` `V` `W` `X` `Y` `Z`
- `a` `b` `c` `d` `e` `f` `g` `h` `i` `j` `k` `l` `m`
- `n` `o` `p` `q` `r` `s` `t` `u` `v` `w` `x` `y` `z`

Digit :: one of

- `0` `1` `2` `3` `4` `5` `6` `7` `8` `9`

IntValue :: IntegerPart [lookahead != {Digit, `.`, NameStart}]

IntegerPart ::

- NegativeSign? 0
- NegativeSign? NonZeroDigit Digit\*

NegativeSign :: -

NonZeroDigit :: Digit but not `0`

FloatValue ::

- IntegerPart FractionalPart ExponentPart [lookahead != {Digit, `.`, NameStart}]
- IntegerPart FractionalPart [lookahead != {Digit, `.`, NameStart}]
- IntegerPart ExponentPart [lookahead != {Digit, `.`, NameStart}]

FractionalPart :: . Digit+

ExponentPart :: ExponentIndicator Sign? Digit+

ExponentIndicator :: one of `e` `E`

Sign :: one of + -

StringValue ::

- `""` [lookahead != `"`]
- `"` StringCharacter+ `"`
- `"""` BlockStringCharacter\* `"""`

StringCharacter ::

- SourceCharacter but not `"` or `\` or LineTerminator
- `\u` EscapedUnicode
- `\` EscapedCharacter

EscapedUnicode ::

- `{` HexDigit+ `}`
- HexDigit HexDigit HexDigit HexDigit

HexDigit :: one of

- `0` `1` `2` `3` `4` `5` `6` `7` `8` `9`
- `A` `B` `C` `D` `E` `F`
- `a` `b` `c` `d` `e` `f`

EscapedCharacter :: one of `"` `\` `/` `b` `f` `n` `r` `t`

BlockStringCharacter ::

- SourceCharacter but not `"""` or `\"""`
- `\"""`

Note: Block string values are interpreted to exclude blank initial and trailing
lines and uniform indentation with {BlockStringValue()}.
