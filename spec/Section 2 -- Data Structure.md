# Data Structure

A Knowledge Graph document is defined as a syntactic grammar where terminal
symbols are tokens (indivisible lexical units). These tokens are defined in a
lexical grammar which matches patterns of source characters. In this document,
syntactic grammar productions are distinguished with a colon `:` while lexical
grammar productions are distinguished with a double-colon `::`.

The source text of a GraphQL document must be a sequence of {SourceCharacter}.
The character sequence must be described by a sequence of {Token} and {Ignored}
lexical grammars. The lexical token sequence, omitting {Ignored}, must be
described by a single {Document} syntactic grammar.

Note: See [Appendix A](#sec-Appendix-Notation-Conventions) for more information
about the lexical and syntactic grammar and other notational conventions used
throughout this document.

**Lexical Analysis & Syntactic Parse**

The source text of a GraphQL document is first converted into a sequence of
lexical tokens, {Token}, and ignored tokens, {Ignored}. The source text is
scanned from left to right, repeatedly taking the next possible sequence of
code-points allowed by the lexical grammar productions as the next token. This
sequence of lexical tokens are then scanned from left to right to produce an
abstract syntax tree (AST) according to the {Document} syntactical grammar.

Lexical grammar productions in this document use _lookahead restrictions_ to
remove ambiguity and ensure a single valid lexical analysis. A lexical token is
only valid if not followed by a character in its lookahead restriction.

For example, an {IntValue} has the restriction {[lookahead != Digit]}, so cannot
be followed by a {Digit}. Because of this, the sequence {`123`} cannot represent
the tokens ({`12`}, {`3`}) since {`12`} is followed by the {Digit} {`3`} and so
must only represent a single token. Use {WhiteSpace} or other {Ignored} between
characters to represent multiple tokens.

Note: This typically has the same behavior as a
"[maximal munch](https://en.wikipedia.org/wiki/Maximal_munch)" longest possible
match, however some lookahead restrictions include additional constraints.

## Source Text

SourceCharacter :: "Any Unicode scalar value"

GraphQL documents are interpreted from a source text, which is a sequence of
{SourceCharacter}, each {SourceCharacter} being a _Unicode scalar value_ which
may be any Unicode code point from U+0000 to U+D7FF or U+E000 to U+10FFFF
(informally referred to as _"characters"_ through most of this specification).

A GraphQL document may be expressed only in the ASCII range to be as widely
compatible with as many existing tools, languages, and serialization formats as
possible and avoid display issues in text editors and source control. Non-ASCII
Unicode scalar values may appear within {StringValue} and {Comment}.

Note: An implementation which uses _UTF-16_ to represent GraphQL documents in
memory (for example, JavaScript or Java) may encounter a _surrogate pair_. This
encodes one _supplementary code point_ and is a single valid source character,
however an unpaired _surrogate code point_ is not a valid source character.

### White Space

WhiteSpace ::

- "Horizontal Tab (U+0009)"
- "Space (U+0020)"

White space is used to improve legibility of source text and act as separation
between tokens, and any amount of white space may appear before or after any
token. White space between tokens is not significant to the semantic meaning of
a GraphQL Document, however white space characters may appear within a {String}
or {Comment} token.

Note: GraphQL intentionally does not consider Unicode "Zs" category characters
as white-space, avoiding misinterpretation by text editors and source control
tools.

### Line Terminators

LineTerminator ::

- "New Line (U+000A)"
- "Carriage Return (U+000D)" [lookahead != "New Line (U+000A)"]
- "Carriage Return (U+000D)" "New Line (U+000A)"

Like white space, line terminators are used to improve the legibility of source
text and separate lexical tokens, any amount may appear before or after any
other token and have no significance to the semantic meaning of a GraphQL
Document.

Note: Any error reporting which provides the line number in the source of the
offending syntax should use the preceding amount of {LineTerminator} to produce
the line number.

### Comments

Comment :: `#` CommentChar\* [lookahead != CommentChar]

CommentChar :: SourceCharacter but not LineTerminator

GraphQL source documents may contain single-line comments, starting with the
{`#`} marker.

A comment may contain any {SourceCharacter} except {LineTerminator} so a comment
always consists of all {SourceCharacter} starting with the {`#`} character up to
but not including the {LineTerminator} (or end of the source).

Comments are {Ignored} like white space and may appear after any token, or
before a {LineTerminator}, and have no significance to the semantic meaning of a
GraphQL Document.

### Insignificant Commas

Comma :: ,

Similar to white space and line terminators, commas ({`,`}) are used to improve
the legibility of source text and separate lexical tokens but are otherwise
syntactically and semantically insignificant within GraphQL Documents.

Non-significant comma characters ensure that the absence or presence of a comma
does not meaningfully alter the interpreted syntax of the document, as this can
be a common user-error in other languages. It also allows for the stylistic use
of either trailing commas or line terminators as list delimiters which are both
often desired for legibility and maintainability of source code.

### Lexical Tokens

Token ::

- Punctuator
- Name
- IntValue
- FloatValue
- StringValue

A GraphQL document is comprised of several kinds of indivisible lexical tokens
defined here in a lexical grammar by patterns of source Unicode characters.
Lexical tokens may be separated by {Ignored} tokens.

Tokens are later used as terminal symbols in GraphQL syntactic grammar rules.

### Ignored Tokens

Ignored ::

- UnicodeBOM
- WhiteSpace
- LineTerminator
- Comment
- Comma

{Ignored} tokens are used to improve readability and provide separation between
lexical tokens, but are otherwise insignificant and not referenced in
syntactical grammar productions.

Any amount of {Ignored} may appear before and after every lexical token. No
ignored regions of a source document are significant, however {SourceCharacter}
which appear in {Ignored} may also appear within a lexical {Token} in a
significant way, for example a {StringValue} may contain white space characters.
No {Ignored} may appear _within_ a {Token}, for example no white space
characters are permitted between the characters defining a {FloatValue}.

**Byte Order Mark**

UnicodeBOM :: "Byte Order Mark (U+FEFF)"

The _Byte Order Mark_ is a special Unicode code point which may appear at the
beginning of a file which programs may use to determine the fact that the text
stream is Unicode, and what specific encoding has been used. As files are often
concatenated, a _Byte Order Mark_ may appear before or after any lexical token
and is {Ignored}.

### Punctuators

Punctuator :: one of ! $ & ( ) ... : = @ [ ] { | }

GraphQL documents include punctuation in order to describe structure. GraphQL is
a data description language and not a programming language, therefore GraphQL
lacks the punctuation often used to describe mathematical expressions.

### Names

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

GraphQL Documents are full of named things: operations, fields, arguments,
types, directives, fragments, and variables. All names must follow the same
grammatical form.

Names in GraphQL are case-sensitive. That is to say `name`, `Name`, and `NAME`
all refer to different names. Underscores are significant, which means
`other_name` and `othername` are two different names.

A {Name} must not be followed by a {NameContinue}. In other words, a {Name}
token is always the longest possible valid sequence. The source characters
{`a1`} cannot be interpreted as two tokens since {`a`} is followed by the
{NameContinue} {`1`}.

Note: Names in GraphQL are limited to the Latin <acronym>ASCII</acronym> subset
of {SourceCharacter} in order to support interoperation with as many other
systems as possible.

**Reserved Names**

Any {Name} within a GraphQL type system must not start with two underscores
{"\_\_"} unless it is part of the [introspection system](#sec-Introspection) as
defined by this specification.

## Graph

Graph : JsonObject

JsonObject:

- Nodes
- Links

Nodes : Node+

Links : Link+

A Graph describes a complete set of data of a Knowledge Graph.

A Knowledge Graph data structure consists of a finite (and possibly mutable) set
of _vertices_ (also called _nodes_), together with a set of unordered pairs of
these vertices for an undirected graph or a set of ordered pairs for a directed
graph. These pairs are known as _edges_ (also called _links_), and for a
directed graph are also known as _edges_ but also sometimes _arrows_ or _arcs_.

A graph contains multiple definitions, either nodes or links

Graphs are only valid if

1. both nodes and links are present
2. only nodes are present

A Graph which contains links ONLY is not valid. Services which receive such a
Graph should return a descriptive error.

## Nodes

Nodes : Node+

Node : a JSON object

## Links

Links : Link+

Link : a JSON object

The JSON that defines a knowledge graph is the following:

```json
{
    "nodes": [
        {
            "id": "understanding-people",
            "fields":{
                "name": "Understanding People",
                "description": "",
                "url": "https://www.skillsyouneed.com/ips/understanding-others.html"
            }
        },
        {
            "id": "face-and-voice",
            "fields":{
                "name": "Face and Voice",
                "description": "",
                "url": "https://www.skillsyouneed.com/ips/nonverbal-face-voice.html"
            }
        }
        ...
    ],
    "links": [
        {
            "source": "understanding-people",
            "target": "face-and-voice"
        },
        ...
    ]
}
```

There is the rationale:

The basic structure is a combination of

1. https://github.com/vasturiano/react-force-graph#input-json-syntax
2. Neo4J JSON import format

We want our data structure to be general enough.

The "fields" comes from the GraphQL fields
