[[developers-guide]]
# 3. Developer’s Guide
:page-layout: dev
:backend: xhtml
:sectnums:
:sectanchors:
:toc:
:page-standards-status: normative

This chapter complements the Author’s Guide by providing more in-depth discussion of language elements, semantics, more complex query scenarios, and more advanced topics such as typing and function definition. Readers are expected to be familiar with the content of the [Author’s Guide](02-authorsguide.html) in the discussions that follow.

## Lexical Elements

CQL is intended to be an authoring language. As such, the syntax is designed to be intuitive and clear, both when writing and reading the language. Care has been taken to ensure that the language contains a simple and clear core set of language elements, and that they all interact in a consistent and predictable manner.

As with any traditional computer language, CQL uses typical lexical elements such as whitespace, keywords, symbols, comments, and so on.

CQL defines the following basic lexical elements:

[[table-3-a]]
[cols=",",options="header",]
|================================================================================
|Element |Description
|*Whitespace* |Whitespace defines the separation between all tokens in the language
|*Comment* |Comments are ignored by the language, allowing for descriptive text
|*Literal* |Literals allow basic values to be represented within the language
|*Symbol* |Symbols such as [.sym]#+#, [.sym]#-#, [.sym]#*#, and [.sym]#/#
|*Keyword* |Grammar-recognized keywords such as define and where
|*Identifier* |User-defined identifiers
|================================================================================

Table 3‑A - The basic lexical elements defined in CQL

Every valid CQL document can be broken down into a set of tokens, each of which is one of these basic lexical elements. The following sections describe each of these elements in more detail.

### Whitespace

CQL defines _tab_, _space_, and _return_ as _whitespace_, meaning they are only used to separate other tokens within the language. Any number of whitespace characters can appear, and the language does not use whitespace for anything other than delimiting tokens.

### Comments

CQL defines two styles of comments, _single-line_, and _multi-line_. A single-line comment consists of two forward slashes, followed by any text up to the end of the line:

[source,cql]
```
define "Foo": 1 + 1 // This is a single-line comment
```

To begin a multi-line comment, the typical forward slash-asterisk token is used. The comment is closed with an asterisk-forward slash, and everything enclosed is ignored:

[source,cql]
```
/*
This is a multi-line comment
Any text enclosed within is ignored
*/
```

Note that nested multi-line comments are not supported.

#### Tags

[.note-info]
____
Comment tags are a new feature of CQL 1.5, and are trial-use
____

Within multi-line comments, CQL supports the ability to define _tags_ that will be associated with the declaration on which they appear. Tags are defined in comments immediately preceding the declaration to which they apply using the `@` symbol, followed by a valid, unquoted identifier, followed by a colon (`:`). For example:

[source,cql]
```
/*
@author: Frederic Chopin
@description: Defines whether the patient is included in the initial population
*/
define "InInitialPopulation":
  AgeInYearsAt(start of MeasurementPeriod) >= 16
    and AgeInYearsAt(start of MeasurementPeriod) < 24
    and Patient.gender in "Female Administrative Sex"
```

The contents of the resulting tag will be whatever comes after the tag definition until the next tag or the end of the comment-block, whatever comes first.

[source,cql]
```
/*
@author: Ludwig van Beethoven
@description: Determines the cumulative duration of a list of intervals
@comment: This function collapses the input intervals prior to determining the cumulative duration
to ensure overlapping intervals do not contribute multiple times to the result
 */
define function "CumulativeDuration"(Intervals List<Interval<DateTime>>):
  Sum((collapse Intervals) X return all duration in days of X)
```

### Literals

Literals provide for the representation of basic values within CQL. The following types of literals are supported:

[[table-3-b]]
[cols=",",options="header",]
|============================================================================================================
|Literal |Description
|*Null* |The null literal ([.kw]#null#)
|*Boolean* |The boolean literals ([.kw]#true# and [.kw]#false#)
|*Integer* |Sequences of digits in the range 0..2^31^-1
|*Long* |Sequences of digits in the range 0..2^63^-1
|*Decimal* |Sequences of digits with a decimal point, in the range 0.0.. (10^28^-1)/10^8^
|*String* |Strings of any character enclosed within single-ticks ([.lit]#'#)
|*Date* |The at-symbol ([.sym]#@#) followed by an ISO-8601 compliant representation of a date
|*DateTime* |The at-symbol ([.sym]#@#) followed by an ISO-8601 compliant representation of a datetime
|*Time* |The at-symbol ([.sym]#@#) followed by an ISO-8601 compliant representation of a time
|*Quantity* |An integer or decimal literal followed by a datetime precision specifier, or a UCUM unit specifier
|*Ratio* |A ratio of two quantities, separated by a colon ([.sym]#:#)
|============================================================================================================

Table 3‑B - The types of literals supported in CQL

A syntax diagram of the types of literals supported can be seen [here](19-l-cqlsyntaxdiagrams.html#literal).

CQL uses standard escape sequences for string literals:

[[table-3-c]]
[cols=",",options="header",]
|========================================================================================
|Escape |Character
|\' |Single-quote
|\" |Double-quote
|\` |Backtick
|\r |Carriage Return
|\n |Line Feed
|\t |Tab
|\f |Form Feed
|\\ |Backslash
|\uXXXX |Unicode character, where XXXX is the hexadecimal representation of the character
|========================================================================================

Table 3‑C - The escape sequences for string literals in CQL

A syntax diagram of the standard escape sequences for string literals supported can be seen [here](19-l-cqlsyntaxdiagrams.html#ESC).

### Symbols

Symbols provide structure to the grammar and allow symbolic invocation of common operators such as addition. CQL defines the following symbols:

[[table-3-d]]
[cols=",",options="header",]
|===============================================================================================
|Symbol |Description
|*:* |Definition operator, typically read as “defined as”. Also used to separate the numerator from denominator in Ratio literals
|*()* |Parentheses for delimiting groups, as well as specifying and passing function parameters
|*[]* |Brackets for indexing into lists and strings, as well as delimiting the retrieve expression
|*{}* |Braces for delimiting lists and tuples
|*<>* |Angle-brackets for delimiting generic types within type specifiers
|*.* |Period for qualifiers and accessors
|*,* |Comma for delimiting items in a syntactic list
|*= != \<= < > >=* |Comparison operators for comparing values
|*+ - * / ^* |Arithmetic operators for performing calculations
|===============================================================================================

Table 3‑D - The symbols supported by CQL to provide structure to the grammar and allow symbolic invocation of common operators such as addition

### Keywords

Keywords are words that are recognized by the parser and used to build the various language constructs. CQL defines the following keywords:

[source,cql]
```
after
aggregate
all
and
as
asc
ascending
before
between
by
called
case
cast
code
Code
codesystem
codesystems
collapse
concept
Concept
contains
context
convert
date
day
days
default
define
desc
descending
difference
display
distinct
div
duration
during
else
end
ends
except
exists
expand
false
flatten
fluent
from
function
hour
hours
if
implies
in
include
includes
included in
intersect
Interval
is
let
library
List
maximum
meets
millisecond
milliseconds
minimum
minute
minutes
mod
month
months
not
null
occurs
of
on
or
or after
or before
or less
or more
or on
overlaps
parameter
per
point
predecessor
private
properly
public
return
same
singleton
second
seconds
start
starting
starts
sort
successor
such that
then
time
timezoneoffset
to
true
Tuple
union
using
valueset
version
week
weeks
where
when
width
with
within
without
xor
year
years
```

A syntax diagram of the keywords supported can be seen [here](19-l-cqlsyntaxdiagrams.html#keyword).

### Reserved Words

When there is no possibility for ambiguity, keywords may also be used as identifiers. However, many keywords are considered _reserved_ words, meaning that it is illegal to use them as identifiers. If necessary, identifiers that clash with a reserved word can be double-quoted or surrounded by backticks (`` ` ``).

The following keywords are defined as reserved words:

[source,cql]
```
aggregate
all
and
as
after
before
between
case
cast
Code
collapse
Concept
convert
day
days
difference
distinct
duration
during
else
exists
expand
false
flatten
from
if
in
included in
is
hour
hours
Interval
let
List
maximum
millisecond
milliseconds
minimum
minute
minutes
month
months
not
null
occurs
of
on or
or
or on
per
properly
return
same
second
seconds
singleton
sort
such that
then
to
true
Tuple
week
weeks
when
with
within
without
year
years
```

Note that most reserved words may still be used as identifiers if the usage is unambiguous. For example, [.kw]#exists# is a reserved word, but because the use of parentheses is required for function invocation, it can still be distinguished as a function identifier.

In addition, even though many keywords are allowed to appear as identifiers, this feature of the language is about avoiding naming clashes with data models used in CQL, and several places in the grammar do not allow keywords or reserved words to be used as identifiers. For example, named expressions, terminology declarations, aliases, and let clauses cannot use keywords or reserved words as identifiers.

A syntax diagram of the _reserved_ words supported can be seen [here](19-l-cqlsyntaxdiagrams.html#reservedWord).

### Identifiers

Identifiers are used to name various elements within the language. There are three types of identifiers in CQL, simple, delimited, and quoted.

A syntax diagram of the types of _identifiers_ in CQL can be seen [here](19-l-cqlsyntaxdiagrams.html#identifier).

A simple identifier is any alphabetical character or an underscore, followed by any number of alpha-numeric characters or underscores. For example, the following are all valid simple identifiers:

[source,cql]
```
Foo
Foo1
_Foo
foo
FOO
```

Note also that these are all unique identifiers. By convention, simple identifiers in CQL should not begin with underscores, and should be Pascal-cased (meaning the first letter of every word within the identifier is capitalized), rather than using underscores.

In particular, the use of identifiers that differ only in case should be avoided.

A delimited identifier is any sequence of characters enclosed in backticks ([.sym]#`#):

[source,cql]
```
`Encounter, Performed`
`Diagnosis`
```

A quoted identifier is any sequence of characters enclosed in double-quotes ([.sym]#"#):

[source,cql]
```
"Encounter, Performed"
"Diagnosis"
```

The use of double-quotes and backticks allows identifiers to contain spaces, commas, and other characters that would not be allowed within simple identifiers. This allows identifiers within CQL to be much more descriptive and readable.

A syntax diagram of quoted identifier can be seen [here](19-l-cqlsyntaxdiagrams.html#QUOTEDIDENTIFIER).

To specify a quoted or delimited identifier that includes a double-quote ([.sym]#"#) or backtick ([.sym]#`#), use a backslash to escape the delimiter:

[source,cql]
```
"Encounter \"Inpatient\""
```

Note that double-quoted and delimited identifiers are still case-sensitive, and as with simple identifiers, the use of identifiers that differ only in case should be avoided. The enclosing delimiter marks are not included in the defined identifier.

CQL escape sequences for strings also work for identifiers:

[[table-3-e]]
[cols=",",options="header",]
|========================================================================================
|Escape |Character
|*\'* |Single-quote
|*\"* |Double-quote
|*\`* |Backtick
|*\r* |Carriage Return
|*\n* |Line Feed
|*\t* |Tab
|*\f* |Form Feed
|*\\* |Backslash
|*\uXXXX* |Unicode character, where XXXX is the hexadecimal representation of the character
|========================================================================================

Table 3‑E - The escape sequences for identifiers in CQL

#### Qualified Identifiers

Identifiers can be combined using the _qualifier_ operator ([.sym]#.#), resulting in a _qualified identifier_. For example [.id]#Common.ConditionsIndicatingSexualActivity#. An identifier with no qualifiers is an _unqualified identifier_.

A syntax diagram of a _qualified identifier_ can be seen [here](19-l-cqlsyntaxdiagrams.html#qualifiedIdentifier).

### Operator Precedence

CQL uses standard in-fix operator notation for expressing computational logic. As a result, CQL also adopts the expected operator precedence to ensure consistent and predictable behavior of expressions written using CQL. The following table lists the order of operator precedence in CQL from highest to lowest:

[[table-3-f]]
[cols=",",options="header",]
|==============================================================
|Category |Operators
|*Primary* |[.sym]#.# [.sym]#[]# [.sym]#()#
|*Conversion Phrase* |[.kw]#convert#..[.kw]#to#
|*Unary Arithmetic* |unary [.sym]#+/-#
|*Extractor* |[.kw]#start# [.kw]#end# [.kw]#difference# [.kw]#duration# [.kw]#width# [.kw]#successor# [.kw]#predecessor of# +
_component_ [.kw]#singleton from#
|*Exponentiation* |[.sym]#^#
|*Multiplicative* |[.sym]#*# [.sym]#/# [.kw]#div mod#
|*Additive* |[.sym]#+# [.sym]#-# [.sym]#&#
|*Conditional* |[.kw]#if#..[.kw]#then#..[.kw]#else# +
[.kw]#case#..[.kw]#else#..[.kw]#end#
|*Unary List* |[.kw]#distinct# [.kw]#collapse# [.kw]#flatten# [.kw]#expand#
|*Unary Test* |[.kw]#is null# [.kw]#true# [.kw]#false#
|*Type Operators* |[.kw]#is as cast#..[.kw]#as#
|*Unary Logical* |[.kw]#not exists#
|*Between* |[.kw]#between# +
_precision_ [.kw]#between# +
[.kw]#duration in# _precision_ [.kw]#between# +
[.kw]#difference in# _precision_ [.kw]#between#
|*Comparison* |[.sym]#\<=# [.sym]#<# [.sym]#># [.sym]#>=#
|*Timing Phrase* |[.kw]#same as# +
[.kw]#includes# +
[.kw]#during# +
[.kw]#before/after# +
[.kw]#within#
|*Interval Operators* |[.kw]#meets overlaps starts ends#
|*Equality* |[.sym]#=# [.sym]#!=# [.sym]#~# [.sym]#!~#
|*Membership* |[.kw]#in contains#
|*Conjunction* |[.kw]#and#
|*Disjunction* |[.kw]#or xor#
|*Implication* |[.kw]#implies#
|*Binary List* |[.kw]#union intersect except#
|==============================================================

Table 3‑F - The order of operator precedence in CQL

As with most expression languages, parentheses can always be used to force order-of-operations if the defined operator precedence does not produce the intended evaluation of a given expression.

When multiple operators appear in a single category, precedence is determined by the order of appearance in the expression, left to right.

### Case-Sensitivity

To encourage consistency and reduce potential confusion, CQL is a case-sensitive language. This means that case is considered when matching keywords and identifiers in the language. For example, the following CQL is invalid:

[source,cql]
```
Define "Foo": 1 + 1
```

The declaration is illegal because the parser will not recognize [.kw]#Define# as a keyword.

[[libraries-1]]
## Libraries

Libraries provide the basic unit of code organization for CQL. Each CQL file contains a single library, and may include any number of libraries by reference, subject to the following constraints:

* The local <<identifiers,identifier>> for a referenced library must be <<qualified-identifiers,unqualified>> and unique within the artifact.
* Circular library references are not allowed.
* Library references are not transitive.

A syntax diagram of a library construct can be seen [here](19-l-cqlsyntaxdiagrams.html#library).

Library identifiers may be qualified to any degree to allow libraries to be organized and shared. In addition, the ELM for a <<04-logicalspecification.adoc#library,library>> contains an identifier element with a namespace which provides a globally unique, stable identifier scope for the library. All the library identifiers within a given namespace must be unique, and the namespace is used by the implementation environment to resolve library identifiers to their actual library source. See the <<examples.adoc#mother-infant-measure,Mother Infant Measure>> for an example of how namespaces are specified in ELM.

When including a library, use the fully qualified identifier for the library. If the [.kw]#called# clause is omitted from the include declaration, the unqualified library identifier will be used as the local identifier for the library.

Because the library identifier and its qualifiers are CQL identifiers, they may be either a simple identifier, or a delimited-identifier, which may actually be a uniform resource identifier (URI), an object identifier (OID), or any other identifier system. It is up to the implementation and environment what interpretation, if any, is given to the identifier of a library. For example, assume a library identified as [.id]#Global.Common#:

[source,cql]
```
library Global.Common

define function "Foo"(A Integer, B Integer):
  A + B
```

When including this library, the [.kw]#called# clause may be omitted:

[source,cql]
```
library UsingCommon

include Global.Common

define function "Bar"(A Integer, B Integer):
  Common.Foo(A, B)
```

Libraries may also be declared with a specific version. When referencing a library, the reference may include a version specifier. If the reference includes a version specifier, the library with that version specifier must be used. If the reference does not include a version specifier, it is up to the implementation environment to provide the most appropriate version of the referenced library.

It is an error to reference a specific version of a library if the library does not have a version specifier, or if there is no library with the referenced version.

Note that the library declaration is optional in a CQL document, but if it is omitted, it is not possible to reference the library from any other CQL library.

Libraries may reference other libraries to any degree of nesting, so long as no circular library references are introduced, and all references to the same library use the same version. For example, given:

[source,cql]
```
library A version '1'
library A version '2'

library B includes library A version '1'
library C includes library A version '2'
```

A library D may not reference both B and C, because it would result in two different versions of library A being referenced.

In addition, library references are not transitive, meaning that in order to reference the components declared within a particular library, the library must be explicitly included. In other words, referencing a library does not automatically include libraries referenced by that library.

### Access Modifiers

Each component of a library may have an access modifier applied, either [.kw]#public# or [.kw]#private#. If no access modifier is applied, the component is considered public. Only public components of a library may be accessed by referencing libraries. Private components can only be accessed within the library itself.

A syntax diagram of the access modifiers can be seen [here](19-l-cqlsyntaxdiagrams.html#accessModifier).

### Identifier Resolution

For identifiers, if a library name is not provided, the identifier must refer to a locally or system defined component. If a library name is provided, it must be the local identifier for the library, and that library must contain the identifier being referenced.

For named expressions, CQL supports forward declarations, so long as the resolution does not result in a circular definition.

### Function Resolution

For functions, if a library name is not provided, the invocation must refer to a locally defined function, or a CQL system function. Function resolution proceeds by attempting to match the _signature_ of the invocation, i.e. the number and type of each argument, to a defined signature for the function. Because the CQL type system supports subtyping, generics, and implicit conversion and casting, it is possible for an invocation signature to match multiple defined signatures. In these cases, the _least converting_ signature is chosen, meaning the signature with the fewest required conversions. If multiple signatures have the same number of required conversions, an ambiguous resolution error is thrown, and the author must provide an explicit cast or conversion to resolve the ambiguity.

If a library name is provided, only that library will be searched for a resolution.

As with expressions, CQL supports forward declarations for functions, so long as the reference does not result in a cycle.

[[data-models-1]]
## Data Models

CQL allows any number of data models to be included in a given library, subject to the following constraints:

* The data model identifier must be unique, both among data models, as well as libraries.
* Data model references are not included from referenced libraries. To reference the data types in a data model, an appropriate local using declaration must be specified.

As with library references, data model references may include a version specifier. If a version is specified, then the environment must ensure that the version specifier matches the version of the data model supplied. If no data model matching the requested version is present, an error is thrown.

### Alternate Data Models

Although the examples in this specification generally use the QUICK model (part of the Clinical Quality Framework), CQL itself does not require or depend on a specific data model. For example, the following sample is taken from the CMS146v2_using_QDM.cql file in the Examples section of the specification:

[source,cql]
```
["Encounter, Performed": "Ambulatory/ED Visit"] E
  with ["Diagnosis": "Acute Pharyngitis"] P such that
    interval[P."start datetime", P."stop datetime")
      overlaps after interval[E."start datetime", E."stop datetime")
```

In this example, QDM is used as the data model. Note the use of quoted attribute identifiers to allow for the spaces in the names of QDM attributes.

### Multiple Data Models

Because CQL allows multiple [.kw]#using# declarations, the possibility exists for clashes within retrieve expressions. For example, a library that used both QUICK and vMR may clash on the name [.id]#Encounter#. In general, the resolution process for class names within CQL proceeds as follows:

* If the class name has no qualifier, then each model used in the current library is searched for an exact match.
** If an exact match is found in more than one model, the reference is considered ambiguous and an error is thrown that the class reference is ambiguous among the matches found.
** If an exact match is found in only one model, that model and type is used.
** If no match is found in any model, an error is thrown that the referenced name cannot be resolved.
* If the class name has a qualifier, then the qualifier specifies the model to be searched, and only that model is used to attempt a resolution.
** If the qualifier specifies the name of a model that cannot be found in the current library, an error is thrown that the referenced model cannot be found.
** If an exact match is found in the referenced model, that class is used.
** If no exact match is found, an error is thrown that the qualified class name cannot be resolved.

## Types

CQL is a statically typed language, meaning that it is possible to infer the type of any given expression, and for any given operator invocation, the type of the arguments must match the types of the operands. To provide complete support for the type system, CQL supports several constructs for dealing with types including _type specifiers_, as well as _conversion_, _casting_, and _type-testing_ operators.

CQL uses a single-inheritance type system, meaning that each type is derived from at most one type. Given a type T and a type T' derived from type T, the following statements are true:

* The type T is a _supertype_ of type T'.
* The type T' is a _subtype_ of type T.
* A value of type T' may appear anywhere a value of type T is expected.

### System-Defined Types

CQL defines several base types that provide the elements for constructing other types, as well as for defining the operations available within the language.

The maximal supertype is System.Any. All other types derive from System.Any, meaning that any value is of some type, and also ultimately of type System.Any.

All the system-defined types derive directly from System.Any. The primitive types and their ranges are summarized here:

[[table-3-g]]
[cols=",,",options="header",]
|=========================================================================
|Type |Range |Step Size
|*Boolean* |false..true |N/A
|*Integer* |-2^31^..2^31^–1 |1
|*Long* |-2^63^..2^63^-1 |1
|*Date* |@0001-01-01..@9999-12-31 |1 day
|*DateTime* |@0001-01-01T00:00:00.0..@9999-12-31T23:59:59.999 |1 millisecond
|*Decimal* |(-10^28^+1)/10^8^..(10^28^-1)/10^8^ |10^-8^
|*String* |All strings of length 2^31^-1 or less. |N/A
|*Time* |@T00:00:00.0..@T23:59:59.999 |1 millisecond
|=========================================================================

Table 3‑G - The primitive types and their ranges supported in CQL

Note that CQL supports three-valued logic, see the section on <<02-authorsguide.adoc#missing-information,Missing Information>> in the Author's Guide, as well as the section on <<Missing Information>> in the Developer's guide for more.

In addition, CQL defines several structured types to facilitate representation and manipulation of clinical information:

[[table-3-h]]
[cols=",",options="header",]
|==========================================================================================================
|Type |Description
|*Code* |Represents a clinical terminology code, including the code identifier, system, version, and display.
|*Concept* |Represents a single concept as a list of equivalent Codes.
|*Quantity* |Represents a quantity with a dimension, specified in UCUM units.
|*Ratio* |Represents a ratio between two quantities
|==========================================================================================================

Table 3‑H - The structured types to facilitate representation and manipulation of clinical information

For more information about these types, refer to the [CQL Reference](09-b-cqlreference.html) section on <<09-b-cqlreference.adoc#types-2,Types>>.

### Specifying Types

In various constructs, the type of a value must be specified. For example, when defining the type of a parameter, or when testing a value to determine whether it is of a specific type. CQL provides the _type specifier_ for this purpose. There are five categories of type-specifiers, corresponding to the four categories of values supported by CQL, plus a choice type category that allows for more flexible models and expressions:

* Named Types
* Tuple Types
* Interval Types
* List Types
* Choice Types

A syntax diagram of the _type specifiers_ in CQL can be seen [here](19-l-cqlsyntaxdiagrams.html#typeSpecifier).

The _named type specifier_ is simply the name of the type. For example:

[source,cql]
```
parameter Threshold Integer
```

This example declares a parameter named [.id]#Threshold# of type [.id]#Integer#.

A syntax diagram of a _named type specifier_ construct can be seen [here](19-l-cqlsyntaxdiagrams.html#namedTypeSpecifier).

The _tuple type specifier_ allows the names and types of the elements of the type to be specified. For example:

[source,cql]
```
parameter Demographics Tuple { address String, city String, zip String }
```

A syntax diagram of a _tuple type specifier_ construct can be seen [here](19-l-cqlsyntaxdiagrams.html#tupleTypeSpecifier).

The _interval type specifier_ allows the point-type of the interval to be specified:

[source,cql]
```
parameter Range Interval<Integer>
```

A syntax diagram of a _interval type specifier_ construct can be seen [here](19-l-cqlsyntaxdiagrams.html#intervalTypeSpecifier).

The _list type specifier_ allows the element-type of a list to be specified:

[source,cql]
```
parameter Points List<Integer>
```

A syntax diagram of a _list type specifier_ construct can be seen [here](19-l-cqlsyntaxdiagrams.html#listTypeSpecifier).

And finally, the _choice type specifier_ allows a choice type to be specified:

[source,cql]
```
parameter ChoiceValue Choice<Integer, String>
```

A syntax diagram of a _choice type specifier_ construct can be seen [here](19-l-cqlsyntaxdiagrams.html#choiceTypeSpecifier).

### Type Testing

CQL supports the ability to test whether or not a value is of a given type. For example:

[source,cql]
```
5 is Integer
```

returns [.kw]#true# because [.lit]#5# is an [.id]#Integer#.

In general, the _is_ relationship determines whether or not a given type is derived from another type. Given a type T and a type T' derived from type T, the following definitions hold:

* Identity – T is T
* Subtype – T' is T

Note that because of the _identity_ relationship above, the term _subtype_ applies to all derived types, as well as the type itself. In the discussions that follow, if a definition must explicitly refer to only derived types, the term _proper subtype_ will be used.

For interval types, given a point type P, and a point type P' derived from type P, interval type Interval<P'> is a subtype of interval type Interval<P>.

For list types, given an element type E, and an element type E' derived from type E, list type List<E'> is a subtype of list type List<E>.

For tuple types, given a tuple type T with elements E~1~, E~2~, ...E~n~, names N~1~, N~2~, ...N~n~­, and types T~1~, T~2~, ...T~n~, respectively, a tuple type T' with elements E'~1~, E'~2~, ...E'~n~, names N'~1~, N'~2~, ...N'~n~, and types T'~1~, T'~2~, ...T'~n~, type T' is a subtype of type T if and only if:

* The number of elements in each type is the same: |E| = |E'|
* For each element in T, there is one element in T' with the same name, and the type of the matching element in T' is a subtype of the type of the element in T.

For structured types, the supertype is specified as part of the definition of the type. Subtypes inherit all the elements of the supertype and may define additional elements that are only present on the derived type.

### Choice Types

CQL also supports the notion of a _choice type_, a type that is defined by a list of component types. For example, an element of a tuple type may be a choice of [.id]#Integer# or [.id]#String#, meaning that the element may contain a value that is either an [.id]#Integer#, or a [.id]#String#.

In addition, choice types can be used to indicate the type of a list of mixed elements, such as the result of a [.kw]#union#:

[source,cql]
```
[Procedure] union [Encounter]
```

This example results in a list that contains both Procedures and Encounters, and the resulting type is [.id]#Choice<Procedure#, [.id]#Encounter>#.

An expression of a choice type can be used anywhere that a value of any of its component types is expected, and an implicit cast will be used to restrict the choice type to the correct component type.

For example, given an [.id]#Observation# type with an element [.id]#value# of type [.id]#Choice<String#, [.id]#Code#, [.id]#Integer#, [.id]#Decimal#, [.id]#Quantity>#, the following expressions are all valid:

[source,cql]
```
Observation.value + 12
Observation.value & ' (observed)' +
Observation.value in "Valid Values" +
Observation.value < 5 'mg'
```

These expressions will result in an implicit cast being applied as follows:

[source,cql]
```
(Observation.value as Integer) + 12 +
(Observation.value as String) & ' (observed)' +
(Observation.value as Code) in "Valid Values" +
(Observation.value as Quantity) < 5 'mg'
```

The semantics for casting will result in a [.kw]#null# if the run-time value of the element is not of the appropriate type.

When accessing an element of a choice type with structured types as components, any element can be accessed. Note, however, that if the element being accessed is present in multiple components, the resulting expression may be a choice type if the elements have different types.

In addition, the choice type enables the set operations, [.kw]#union#, [.kw]#intersect#, and [.kw]#except# to be generalized to work on lists of different types.

For [.kw]#union#, this means that the inputs can be lists of different types of elements, and the type of the result is now a choice type with components of each of the input types. If the input types are the same, the result is a choice with a single component which degenerates to the component type.

For [.kw]#intersect#, this means the inputs can be lists of different types of elements, and the type of the result is a choice with only the types that are common between the input types. Again, if this results in a choice with a single component, it degenerates to the component type.

For [.kw]#except#, this means that the inputs can contain lists of different types of elements, but because the except may not exclude all the values of a given type, the result will be the same type as the left input.

### Type Inference

Type inference is the process of determining the type of an expression based on the types of the values and operations involved in the expression. CQL is a strongly typed language, meaning that it is always possible to infer the type of an expression at compile-time (i.e. by static analysis).

The type inference rules for the various categories of language constructs are given in the following sections.

#### Literals and Selectors

The type of a literal is trivial for the primitive types and selectors: [.id]#Boolean#, [.id]#String#, [.id]#Integer#, [.id]#Long#, [.id]#Decimal#, [.id]#Date#, [.id]#DateTime#, [.id]#Time#, [.id]#Quantity#, and [.id]#Ratio#.

The type of the null selector is Any.

For a list selector, the type may be specified as part of the selector:

[source,cql]
```
List<System.Integer> { 1, 2, 3 }
```

Or it may be inferred based on the types of the elements:

[source,cql]
```
{ 1, 2, 3 }
```

For an empty list, with no specifier, the type is List<Any>.

If the type of a list is specified, the elements in the list are required to be of the declared element type of the list.

If the type of the list is inferred, the type of the first element is used initially, and subsequent elements in the list are required to be of the inferred type of the first element, with the exception that if a subsequent element is a supertype of the initial element, or if the initial element is convertible to the type of a subsequent element, the type of the subsequent element will become the new inferred element type for the list.

For a tuple selector, the type is constructed from the elements in the tuple selector.

For an instance selector, the type is determined by the name of the type of the instance being constructed.

#### Operators and Functions

In general, the result type of an operator or function is determined by the declared return type of the function. For example, the (Integer, Integer) overload of the Add operator returns an Integer value, so the type of an Add invocation is Integer:

[source,cql]
```
3 + 4
```

The CQL Reference appendix gives the signatures and declared return types for all system operators.

In addition to special cases for operators such as conditionals and Coalesce, CQL defines implicit conversion, casting, and promotion and demotion to provide more flexible type checking rules. These special cases are described in subsequent sections.

[[queries-1]]
#### Queries

For queries, the type inference rules are based on the clauses used, beginning with single-source queries:

1.  For a single-source query, the initial type of the query is the type of expression defining the single source. If the expression is singular (i.e. non-list-valued) the query ranges over only that element. If the expression is plural, the query ranges over all the elements in the list.
2.  For a multi-source query, the initial type of the query is defined by a tuple where each tuple has an element for each source in the query, named the alias name of the source, and of the type of the expression defining the source. If all sources are singular the initial type of the query is the singular tuple type. If any source is plural, the initial type of the query is a list of the tuple type.
3.  Let clauses only introduce content that can be referenced within the scope of the query, they do not impact the type of the result unless referenced within a return clause.
4.  With and without clauses only limit the set of results returned by a query, they do not impact the type of the result.
5.  A where clause only limits the set of results returned by the query, it does not impact the type of the result.
6.  The return clause determines the overall shape of the query result. If there is no return clause, the result type of the query is the same as the initial type of the query as determined based on the sources. If a return clause is used, the result type of the query is inferred based on the return expression. If the query is singular, the result type is the type of the return clause expression. If the query is plural, the result type is a list whose element types are the type of the return expression.
7.  Similar to the return clause, an aggregate clause, if present, determines the overall result of the query. If an aggregate clause is used, the result type of the query is the result type of the aggregate expression, regardless of whether the query is singular or plural.

### Conversion

Conversion is the operation of turning a value from one type into another. For example, converting a number to a string, or vice-versa. CQL supports explicit conversion operators, as well as implicit conversion for some specific types.

#### Explicit Conversion

The explicit [.kw]#convert# can be used to convert a value from one type to another. For example, to convert the string representation of a datetime to a [.id]#DateTime# value:

[source,cql]
```
convert '2014-01-01T12:00:00.0-06:00' to DateTime
```

If the conversion cannot be performed, the result is [.kw]#null#. For example:

[source,cql]
```
convert 'Foo' to Integer
```

will result in [.kw]#null#. The convert syntax is equivalent to invoking one of the defined conversion operators:

[[table-3-i]]
[cols=",",options="header",]
|==============================================================================================================================================================================================================
|Operator |Description
|*ToBoolean(Decimal)* |Converts the decimal representation of a boolean value (`0.0` or `1.0`) to a Boolean value
|*ToBoolean(Integer)* |Converts the integer representation of a boolean value (`0` or `1`) to a Boolean value
|*ToBoolean(Long)* |Converts the long representation of a boolean value (`0L` or `1L`) to a Boolean value
|*ToBoolean(String)* |Converts the string representation of a boolean value to a Boolean value
|*ToInteger(Boolean)* |Converts a boolean to an integer value, true results in `1`, false results in `0`
|*ToInteger(Long)* |Converts a Long value to an equivalent Integer value
|*ToInteger(String)* |Converts the string representation of an integer value to an Integer value using the format ([.sym]#+\|-#)d*
|*ToLong(Boolean)* |Converts a boolean value to an equivalent Long value, true results in `1L`, false results in `0L`
|*ToLong(Integer)* |Converts an Integer value to an equivalent Long value
|*ToLong(String)* |Converts the string representation of a long value to a Long value using the format ([.sym]#+\|-#)d*
|*ToDecimal(Boolean)* |Converts a Boolean value to an equivalent Decimal value, true results in `1.0`, false results in `0.0`
|*ToDecimal(Integer)* |Converts an Integer value to an equivalent Decimal value
|*ToDecimal(Long)* |Converts a Long value to an equivalent Decimal value
|*ToDecimal(String)* |Converts the string representation of a decimal value to a Decimal value using the format ([.sym]#+\|-#)d*.d*
|*ToQuantity(Decimal)* |Converts a Decimal value to a Quantity with a default unit ('1')
|*ToQuantity(Integer)* |Converts an Integer value to a Quantity with a default unit ('1')
|*ToQuantity(String)* |Converts the string representation of a quantity value to a Quantity value using the format ([.sym]#+\|-#)d*.d*'units'
|*ToRatio(String)* |Converts the string representation of a ratio value to a Ratio value using the format <quantity>:<quantity>
|*ToDate(String)* |Converts the string representation of a date value to a Date value using ISO-8601 format: YYYY-MM-DD
|*ToDate(DateTime)* |Converts a DateTime to a Date. This is equivalent to invoking [.kw]#date from# on the DateTime value
|*ToDateTime(Date)* |Converts a Date value to a DateTime with all time components unspecified and the timezone offset of the request
|*ToDateTime(String)* |Converts the string representation of a datetime value to a DateTime value using ISO-8601 format: YYYY-MM-DDThh:mm:ss.fff(+\|-)hh:mm
|*ToTime(String)* |Converts the string representation of a time value to a Time value using ISO-8601 format: hh:mm:ss.fff
|*ToString(Boolean)* |Converts a Boolean value to its string representation (true\|false)
|*ToString(Integer)* |Converts an Integer value to its string representation
|*ToString(Long)* |Converts a Long value to its string representation
|*ToString(Decimal)* |Converts a Decimal value to its string representation
|*ToString(Quantity)* |Converts a Quantity value to its string representation
|*ToString(Ratio)* |Converts a Ratio value to its string representation
|*ToString(Date)* |Converts a Date value to its string representation
|*ToString(DateTime)* |Converts a DateTime value to its string representation
|*ToString(Time)* |Converts a Time value to its string representation
|*ToConcept(Code)* |Converts a Code value to a Concept with the given Code as its primary and only Code. If the Code has a display value, the Concept will have the same display value.
|*ToConcept(List<Code>)* |Converts a list of Code values to a Concept with the first Code in the list as the primary Code. If the primary Code has a display value, the Concept will have the same display value.
|==============================================================================================================================================================================================================

Table 3‑I - The defined type conversion operators in CQL

For a complete description of these conversion operators, refer to the <<09-b-cqlreference.adoc#type-operators-1,Type Operators>> section in the [CQL Reference](09-b-cqlreference.html).

#### Quantity Conversions

In addition to type conversions, CQL supports _quantity conversion_, converting a quantity from one unit to another unit using the same [.kw]#convert# keyword:

[source,cql]
```
convert 5000 'g' to 'kg'
convert 28 days to weeks
```

If the unit conversion is valid, the expression results in a quantity with the target units. If the unit conversion is invalid, the result is [.kw]#null#.

[.note-warning]
____

Note that implementations are not required to support quantity conversion. Implementations that do support unit conversion shall do so according to the conversion specified by UCUM. Implementations that do not support unit conversion shall throw an error if an unsupported unit conversion is requested with this operation.

____

#### Implicit Conversions

In addition to the explicit conversion operators discussed above, CQL supports implicit conversions for specific types to enable expressions to be built more easily. The following table lists the explicit and implicit conversions supported in CQL:

[[table-3-j]]
[cols=",,,,,,,,,,,,,",options="header",]
|===============================================================================================
|From\To   |Boolean |Integer |Long |Decimal |Quantity |Ratio |String |Date |DateTime |Time |Code |Concept |List<Code> |ValueSet
|*Boolean* |N/A |Explicit |Explicit |Explicit |- |- |Explicit |- |- |- |- |- |- |-
|*Integer* |Explicit |N/A |Implicit |Implicit |Implicit |- |Explicit |- |- |- |- |- |- |-
|*Long* |Explicit |Explicit |N/A |Implicit |- |- |Explicit |- |- |- |- |- |- |-
|*Decimal* |Explicit |- |- |N/A |Implicit |- |Explicit |- |- |- |- |- |- |-
|*Quantity* |- |- |- |- |N/A |- |Explicit |- |- |- |- |- |- |-
|*Ratio* |- |- |- |- |- |N/A |Explicit |- |- |- |- |- |- |-
|*String* |Explicit |Explicit |Explicit |Explicit |Explicit |Explicit |N/A |Explicit |Explicit |Explicit |- |- |- |-
|*Date* |- |- |- |- |- |- |Explicit |N/A |Implicit |- |- |- |- |-
|*DateTime* |- |- |- |- |- |- |Explicit |Explicit |N/A |- |- |- |- |-
|*Time* |- |- |- |- |- |- |Explicit |- |- |N/A |- |- |- |-
|*Code* |- |- |- |- |- |- |- |- |- |- |N/A |Implicit |- |-
|*Concept* |- |- |- |- |- |- |- |- |- |- |- |N/A |Explicit |-
|*List<Code>* |- |- |- |- |- |- |- |- |- |- |- |Explicit |N/A |-
|*ValueSet* |- |- |- |- |- |- |- |- |- |- |- |- |Implicit |N/A
|===============================================================================================

Table 3‑J - The explicit and implicit conversions supported in CQL

In addition to these conversions, note that specific data models may introduce conversions, including implicit conversions.

Although implicit conversions can be performed using the explicit convert, the language will also automatically apply implicit conversions when appropriate to produce a correctly typed expression. For example, consider the following multiplication:

[source,cql]
```
define "MixedMultiply": 1 * 1.0
```

The type of the literal [.lit]#1# is [.id]#Integer#, and the type of the literal [.lit]#1.0# is [.id]#Decimal#. To infer the type of the expression correctly, the language will implicitly convert the type of the [.lit]#1# to [.id]#Decimal# by inserting a [.id]#ToDecimal# invocation. The multiplication is then performed on two [.id]#Decimals#, and the result type is [.id]#Decimal#.

In addition, CQL defines implicit conversion of a named structured type to its equivalent tuple type. For example, given the type [.id]#Person# with elements [.id]#Name# of type [.id]#String# and [.id]#DOB# of type [.id]#DateTime#, the following comparison is valid:

[source,cql]
```
define "TupleComparison": Person { Name: 'Joe', DOB: @1970-01-01 } = Tuple { Name: 'Joe', DOB: @1970-01-01 }
```

In this case, the structured value will be implicitly converted to the equivalent tuple type, and the comparison will evaluate to true.

Note that the opposite implicit conversion, from a tuple to a named structured type, does not occur because a named structured type has additional information (namely the type hierarchy) that cannot be inferred from the definition of a tuple type. In such cases, an explicit conversion can be used:

[source,cql]
```
define "TupleExpression": Tuple { Name: 'Joe', DOB: @1970-01-01 }
define "TupleConvert": convert TupleExpression to Person
```

The conversion from a tuple to a structured type requires that the set of elements in the tuple type be the same set or a subset of the elements in the structured type.

### Casting

Casting is the operation of treating a value of some base type as a more specific type at run-time. The [.kw]#as# operator provides this functionality. For example, given a model that defines an [.id]#ImagingProcedure# as a specialization of a [.id]#Procedure#, in the following example:

[source,cql]
```
define "AllProcedures": [Procedure]
define "ImagingProcedures":
  AllProcedures P
    where P is ImagingProcedure
    return P as ImagingProcedure
```

the [.id]#ImagingProcedures# expression returns all procedures that are instances of [.id]#ImagingProcedure# as instances of [.id]#ImagingProcedure#. This means that attributes that are specific to [.id]#ImagingProcedure# can be accessed.

If the run-time type of the value is not of the type specified in the [.kw]#as# operator, the result is [.kw]#null#.

In addition, CQL supports a _strict_ cast, which has the same semantics as casting, except that if the run-time type of the value is not of the type specified, a run-time error is thrown. The keyword [.kw]#cast# is used to indicate a strict cast:

[source,cql]
```
define "StrictCast": cast First(Procedures) as ImagingProcedure
```

#### Implicit Casting

CQL also supports the notion of _implicit casting_ to prevent the need to cast a [.kw]#null# literal to a specific type. For example, consider the following expression:

[source,cql]
```
define "ImplicitCast": 5 * null
```

The type of the first argument to the multiplication is [.id]#Integer#, and the type of the second argument is [.id]#Any#, an untyped [.kw]#null# literal. But multipication of [.id]#Integer# and [.id]#Any# is not defined and [.id]#Any# is a supertype of [.id]#Integer#, not a subtype. This means that with strict typing, this expression would not compile without the addition of an explicit cast:

[source,cql]
```
define "ImplicitCast": 5 * (null as Integer)
```

To avoid the need for this explicit cast, CQL implicitly casts the [.id]#Any# to [.id]#Integer#.

### Promotion and Demotion

To simplify the expression of logic involving lists and intervals, CQL defines _promotion_ and _demotion_, which are a special class of implicit conversions.

Promotion is used to implicitly convert a value to a list of values of that type. Whenever an operation that expects a list-valued argument is passed a single value, the single value may be promoted to a list of the same type containing the single value as its only element.

Demotion is the opposite, used to implicitly extract a single value from a list of values. Whenever an operation that expects a singleton is passed a list, the list may be demoted to a singleton using [.kw]#singleton from#.

For intervals, promotion is performed by creating an interval with the single value as the start and end of the interval, and demotion is performed using [.kw]#point from#.

Note that the use of demotion has the potential to result in a run-time error if [.kw]#singleton from# is invoked on a list with multiple elements, or if [.kw]#point from# is invoked on an interval wider than a single point. In addition, promotion and demotion can sometimes result in unexpected behavior. As such, environments may choose whether or not to support these features of the language.

Whether or not promotion and demotion should be used depends on the type-safety expectations for each use case. As such, promotion and demotion should only be used in environments where the consequences are well understood. By default, list promotion and demotion are appropriate to support the use of FHIRPath, interval promotion is used only to enable mixed-type signatures for the [.kw]#same or after# and [.kw]#same or before# operators, and interval demotion is not used.

### Conversion Precedence

Because of the possibility that a given invocation signature may be resolved to multiple overloads of an operator through the application of different conversions, CQL specifies a conversion precedence for resolving the ambiguity. When matching the invocation type of an argument to the declared type of the corresponding argument of an operator, the following precedence is applied:

1.  Exact match – If the invocation type is an exact match to the declared type of the argument
2.  Subtype – If the invocation type is a subtype of the declared type of the argument
3.  Compatible – If the invocation type is compatible with the declared type of the argument (e.g., the invocation type is Any)
4.  Cast - If the invocation type can be cast to the declare type (e.g., the invocation type is a choice that includes the declared type)
5.  Implicit Conversion To Simple Type – An implicit conversion is defined from the invocation type of the argument to the declared type of the argument, and the declared type is a simple type
6.  Implicit Conversion To Class Type - An implicit conversion is defined from the invocation type of the argument to the declared type of the argument, and the declared type is a class type
7.  Interval Promotion - The declared type is an interval and the invocation type can be promoted to an interval of that type
8.  List Demotion – The invocation type of the argument is a list and can be demoted to the declared type
9.  Interval Demotion - The invocation type of the argument is an interval and can be demoted to the declared type
10.  List Promotion – The declared type is a list and the invocation type can be promoted to a list of that type

These conversion precedences can be viewed as ordered from _least converting_ to _most converting_. When determining a conversion path from an invocation signature to a declared signature, the _least converting_ overall conversion path is used.

## Conditional Expressions

To simplify the expression of complex logic, CQL provides two flavors of conditional expressions, the [.kw]#if# expression, and the [.kw]#case# expression.

The if expression allows a single condition to select between two expressions:

[source,cql]
```
if Count(X) > 0 then X[1] else 0
```

This expression checks the count of X and returns the first element if it is greater than [.lit]#0#; otherwise, the expression returns [.lit]#0#. Note that if the condition evaluates to [.kw]#null#, it is interpreted as [.kw]#false#.

The [.kw]#case# expression allows multiple conditions to be tested, and comes in two flavors: standard case, and selected case.

A standard case allows any number of conditions, each with a corresponding expression that will be the result of the [.kw]#case# if the associated condition evaluates to [.kw]#true#. Note that as with the if expression, if the condition evaluates to [.kw]#null#, it is interpreted as [.kw]#false#. If none of the conditions evaluate to [.kw]#true#, the [.kw]#else# expression is the result:

[source,cql]
```
case
  when X > Y then X
  when Y > X then Y
  else 0
end
```

A selected case specifies a comparand, and each case item specifies a possible value for the comparand. If the comparand is equal to a case item, the corresponding expression is the result of the selected case. If the comparand does not equal any of the case items, the else expression is the result:

[source,cql]
```
case X
  when 1 then 12
  when 2 then 14
  else 15
end
```

Note that if the source expression in a selected case is [.kw]#null#, no condition will compare equal and the result will be the else expression. If any case item is [.kw]#null#, it will not compare equal to the comparand.

Both of these conditional expression constructs require run-time conditional evaluation. This is sometimes referred to as short-circuit evaluation for conditional expressions. For implementations, this means delaying evaluation of the arguments. Revisiting the first example in this section:

[source,cql]
```
if Count(X) > 0 then X[1] else 0
```

Short-circuit evaluation means the expression `X[1]` will only be evaluated if `Count(X) > 0` evaluates to [.kw]#true#. This is in contrast to the logical operators [.kw]#and# and [.kw]#or#, where short-circuit evaluation is not required.

## Nullological Operators

To provide complete support for missing information, CQL supports several operators for testing for and dealing with null results.

To provide a null result, use the [.kw]#null# keyword:

[source,cql]
```
null
```

To test whether an expression is [.kw]#null#, use the _null test_:

[source,cql]
```
X is null
X is not null
```

To replace a null with the result of an expression, use a simple [.kw]#if# expression:

[source,cql]
```
if X is null then Y else X
```

To return the first non-null expression among two or more expressions, use the [.id]#Coalesce# operator:

[source,cql]
```
Coalesce(X, Y, Z)
```

which is equivalent to:

[source,cql]
```
case
  when X is not null then X
  when Y is not null then Y
  else Z
end
```

In addition, CQL supports the boolean-test operators [.kw]#is [not] true# and [.kw]#is [not] false#. These operators, like the null-test operator, only return [.kw]#true# and [.kw]#false#, they will not propagate a [.kw]#null# result.

[source,cql]
```
X is true
X is not false
```

The first example will return [.kw]#true# if X evaluates to [.kw]#true#, [.kw]#false# if X evaluates to [.kw]#false# or [.kw]#null#. The second example will return [.kw]#true# if X evaluates to [.kw]#true# or [.kw]#null#, [.kw]#false# if X evaluates to [.kw]#false#. Note in particular that these operators are _not_ equivalent to comparison of [.id]#Boolean# results using equality or inequality.

## String Operators

Although less common in typical clinical logic, some use cases require string manipulation. As such, CQL supports a core set of string operators.

Like lists, strings are 0-based in CQL. To index into a string, use the _indexer_ operator:

[source,cql]
```
X[0]
```

To determine the length of string, use the [.id]#Length# operator:

[source,cql]
```
Length(X)
```

To determine the position of a given pattern within a string, use the [.id]#PositionOf# operator:

[source,cql]
```
PositionOf('cde', 'abcdefg')
```

The [.id]#PositionOf()# operator returns the index of the starting character of the first argument in the second argument, if the first argument can be located in the second argument. Otherwise, [.id]#PositionOf()# returns [.lit]#-1# to indicate the pattern was not found in the string. To find the last appearance of a given pattern, use [.id]#LastPositionOf()#, and to find patterns at the beginning and end of a string, use [.id]#StartsWith()# and [.id]#EndsWith()#. Regular expression matching can be performed with the [.id]#Matches()# and [.id]#ReplaceMatches()# operators.

To return a substring from a given string, use the [.id]#Substring# operator:

[source,cql]
```
Substring('abcdefg', 0, 3)
```

This example returns the string [.lit]#'abc'#. The second argument is the starting index of the substring to be returned, and the third argument is the length of the substring to be returned. If the length is greater than the number of characters present in the string from the starting index on, the result includes only the remaining characters. If the starting index is less than 0, or greater than the length of the string, the result is [.kw]#null#. The third argument is optional; if it is not provided, the substring is taken from the starting index to the end of the string.

To concatenate strings, use the [.sym]#+# operator:

[source,cql]
```
'abc' + 'defg'
```

Note that when using [.sym]#+# with string values, if either argument is [.kw]#null#, the result will be [.kw]#null#. To treat [.kw]#null# as the empty string ([.sym]#''#), use the [.sym]#&# operator:

[source,cql]
```
'abc' & 'defg'
```

To combine a list of strings, use the [.id]#Combine# operator:

[source,cql]
```
Combine({ 'ab', 'cd', 'ef' })
```

The result of this expression is:

[source,cql]
```
'abcdef'
```

To combine a list with a separator, provide the separator argument to the [.id]#Combine# operator:

[source,cql]
```
Combine({ 'completed', 'refused', 'pending' }, ';')
```

The result of this expression is:

[source,cql]
```
'completed;refused;pending'
```

To split a string into a list of strings based on a specific separator, use the [.id]#Split# operator:

[source,cql]
```
Split('completed;refused;pending', ';')
```

The result of this expression is:

[source,cql]
```
{ 'completed', 'refused', 'pending' }
```

Use the [.id]#Upper# and [.id]#Lower# operators to return strings with upper or lowercase letters for all characters in the argument.

[[introducing-context-in-queries]]
## Introducing Scoped Definitions in Queries

The CQL query construct provides for the ability to introduce named expressions that only exist within the scope of a single query. The _let clause_ of queries allows any number of definitions to be provided. Each definition has access to all the available components of the query scope. This feature is extremely useful for simplifying query logic by allowing complex expressions to be defined and then reused within the scope of a single query. For example:

[source,cql]
```
"Medications" M
  let ingredients: GetIngredients(M.rxNormCode)
  return
    ingredients I
      let
        adjustedDoseQuantity: EnsureMicrogramQuantity(M.doseQuantity),
        dailyDose:
          GetDailyDose(
            I.ingredientCode,
            I.strength,
            I.doseFormCode,
            adjustedDoseQuantity,
            M.dosesPerDay
          ),
        factor: GetConversionFactor(I.ingredientCode, dailyDose, I.doseFormCode)
      return {
        rxNormCode: M.rxNormCode,
        doseFormCode: I.doseFormCode,
        doseQuantity: adjustedDoseQuantity,
        dosesPerDay: M.dosesPerDay,
        ingredientCode: I.ingredientCode,
        ingredientName: I.ingredientName,
        strength: I.strength,
        dailyDose: dailyDose,
        mme: Quantity { value: dailyDose.value * factor, unit: dailyDose.unit + '/d' }
      }
```

In this query, the same logic defined by the [.id]#dailyDose# expression can be reused multiple times in the where clause, avoiding the need to repeat the calculation and making the intended meaning of the logic much more clear.

Note also the ability to reference a previously defined let in the same scope, as in the use of [.id]#adjustedDoseQuantity# in the definition of [.id]#dailyDose#.

## Multi-Source Queries

In addition to the single-source queries discussed in the Author’s Guide, CQL provides multi-source queries to allow for the simple expression of complex relationships between sets of data. Consider the following excerpt from the numerator of a measure for appropriate warfarin and parenteral anticoagulation overlap therapy:

* *Numerator =*
** Patients who received warfarin and parenteral anticoagulation:
*** Five or more days, with an INR greater than or equal to 2 prior to discontinuation of parenteral therapy
*** OR: Five or more days, with an INR less than 2 and discharged on overlap therapy
*** OR: Less than five days and discharged on overlap therapy

We begin by breaking this down into the source components, Encounters, Warfarin Therapy, and Parenteral Therapy:

[source,cql]
```
define "Encounters": [Encounter: "Inpatient"] E
  where E.period during "Measurement Period"
define "Warfarin Therapy": [MedicationAdministration: "Warfarin"]
define "Parenteral Therapy": [MedicationAdministration: "Parenteral Anticoagulation"]
```

First, we establish that the encounter had both warfarin and parenteral anticoagulation therapies. This is easy enough to accomplish using [.kw]#with# clauses:

[source,cql]
```
define "Encounters with Warfarin and Parenteral Therapies":
  "Encounters" E
    with "Warfarin Therapy" W such that W.effectiveTime starts during E.period
    with "Parenteral Therapy" P such that P.effectiveTime starts during E.period
```

However, the next step involves calculating the duration of overlap between the warfarin and parenteral therapies, and a with clause only filters by a relationship, it does not introduce any data from the related source. To allow queries like this to be easily expressed, CQL allows a [.kw]#from# clause to be used to start a query:

[source,cql]
```
define "Encounters with Warfarin and Parenteral Therapies":
  from "Encounters" E,
    "Warfarin Therapy" W,
    "Parenteral Therapy" P
  where W.effectiveTime starts during E.period
    and P.effectiveTime starts during E.period
```

We now have both the encounter and the warfarin and parenteral therapies in scope and can perform calculations involving all three:

[source,cql]
```
define "Encounters with overlapping Warfarin and Parenteral Therapies":
  from "Encounters" E,
    "Warfarin Therapy" W,
    "Parenteral Therapy" P
  where W.effectiveTime starts during E.period
    and P.effectiveTime starts during E.period
    and duration in days of (W.effectiveTime intersect P.effectiveTime) >= 5
    and Last([Observation: "INR Value"] I
      where I.applies during P.effectiveTime sort by applies).value >= 2
```

This gives us the first condition, namely that a patient was on overlapping warfarin and parenteral therapies for at least 5 days, and the ending INR result associated with the parenteral therapy is greater than or equal to 2.

Next, we need to build criteria for the other cases, but these cases involve the same calculations, just compared against different values, or in different ways. Rather than having to restate the calculations multiple times, CQL allows a [.kw]#let# clause to be used to introduce an intermediate computational result within a query:

[source,cql]
```
define "Encounters with overlapping Warfarin and Parenteral Therapies":
  from "Encounters" E,
    "Warfarin Therapy" W,
    "Parenteral Therapy" P
  let
    overlapDuration: duration in days of (W.effectiveTime intersect P.effectiveTime),
    endingINR:
      Last([Observation: "INR Value"] I
        where I.applies during P.effectiveTime sort by applies
      ).value
  where W.effectiveTime starts during E.period
    and P.effectiveTime starts during E.period
    and (
      (overlapDuration >= 5 and endingINR >= 2)
      or (overlapDuration >= 5 and endingINR < 2
        and P.effectiveTime overlaps after E.period)
      or (overlapDuration < 5
        and P.effectiveTime overlaps after E.period)
    )
return E
```

Because the return clause in a query is optional, the type of the result of multi-source queries with no return clause is defined as a list of tuples with an element for each source named the alias for the source within the query and of the type of the elements of the source. For example:

[source,cql]
```
from [Encounter] E, [MedicationStatement] M
```

The result type of this query is:

[source,cql]
```
List<Tuple { E Encounter, M MedicationStatement }>
```

The result will be a list of tuples containing the cartesian product of all Encounters and Medication Statements.

In addition, the default for return clauses is [.kw]#distinct#, as opposed to [.kw]#all#, so if no return clause is specified, duplicates will be eliminated from the result.

### Query Syntax Options

Note that the grammar for CQL queries allows for the [.kw]#from# keyword to be used for single- and multi-source queries. For example, the following is valid CQL:

[source,cql]
```
from [Encounter] E
  where E.effectiveTime starts after Today() - 1 year
```

Moreover, parsing the grammar can be simplified by requiring that all queries start with the [.kw]#from# keyword. To support a change to the language to enable this simplification, environments may require that all queries begin with the [.kw]#from# keyword.

## Non-Retrieve Queries

In addition to the query examples already discussed, it is possible to use any arbitrary expression as the source for a query. For example:

[source,cql]
```
({ 1, 2, 3, 4, 5 }) L return L * 2
```

This query results in [.sym]#{# [.lit]#2#, [.lit]#4#, [.lit]#6#, [.lit]#8#, [.lit]#10# [.sym]#}#. Note that the parentheses are required for arbitrary expressions. A query source is either a retrieve, a qualified identifier, or a parenthesized expression.

The above example also illustrates that queries need not be based on lists of tuples. In fact, they need not be based on lists at all. The following example illustrates the use of a query to redefine a single tuple:

[source,cql]
```
define "FirstInpatientEncounter":
  First([Encounter] E where E.class = 'inpatient' sort by period.start desc)

define "RedefinedEncounter":
  FirstInpatientEncounter E
    return Tuple {
      type: E.type,
      admissionDate: E.period.start,
      dischargeDate: E.period.end
    }
```

In addition, even if a given query is based on a list of tuples, the results are not required to be tuples. For example, if only the length of stay is required, the following example could be used to return a list of integers representing the length of stay in days for each encounter:

[source,cql]
```
[Encounter: "Inpatient"] E
  return duration in days of E.period
```

## Related Context Retrieves

[.note-info]
____
Support for specifying search paths, include and reverseInclude elements in the Retrieve is a new feature of CQL 1.5, and is trial-use.
____

To allow queries to cross contexts, CQL supports the notion of a _related context retrieve_. For example, consider a neonatal measure where the infant is the subject of the measure. In order to calculate gestational age, the measure may need to retrieve information from the mother's record. Without the ability to cross contexts, this would not be possible. The following example illustrates this usage:

[source,cql]
```
context Patient

define "Mother": singleton from ([RelatedPerson: "Mother Relationship"])

define "Estimated Due Date":
  Last(
    ["Mother" -> "Observation": "Estimated Due Date Exam"] Exam
      sort by effective
  )

define "Gestational Age in Days at Birth":
  (280 - (duration in days between "Estimated Due Date" and "Birth Date")) div 7
```

Note the use of the [.id]#"Mother"# expression within the retrieve: [.id]#["Mother" \-> "Observation": "Estimated Due Date Exam"]#. This syntax ([.sym]#\->#) indicates that the retrieve should be performed in the context returned by the [.id]#"Mother"# expression. The [.id]#"Mother"# expression in this case will be evaluated in the [.id]#"Patient"# context, and result in a [.id]#RelatedPerson# from the infant's record with the relationship type of [.id]#"Mother Relationship"#. The [.id]#RelatedPerson# will then be used as the context for the retrieve.

If the expression being defined (such as "Mother" in the previous example) is [.kw]#null#, the related context retrieve would be evaluated with [.id]#id# [.sym]#= null#, which would result in unknown and an empty list would be the result. If the expression returns a class instance (as in this case, an instance of a [.id]#RelatedPerson#), the model information is used to determine the attribute of the class that contains the value for the context id ([.id]#linkedPatientId# in this case). And finally, the expression is not allowed to return a list, it must evaluate to a single class or primitive value.

[.note-danger]
____
As with all healthcare-related data, there are privacy and security concerns associated with this feature. Implementations must ensure that use of this functionality does not violate any access, authorization, or use protocols in the systems being accessed with this feature.

See the <<examples.adoc#mother-infant-measure,Mother Infant Measure>> example for a detailed illustration of this functionality.
____

## Aggregate Queries

[.note-info]
____
The aggregate clause is a new feature of CQL 1.5, and is trial-use.
____

CQL provides support for a limited class of recursive problems using the _aggregate clause_ of the query construct. This clause is similar in function to the JavaScript `.reduce()` function, in that it allows an expression to be repeatedly evaluated for each element of a list, and that expression can access the _current_ value of the aggregation. For example, the following query illustrates a simple usage of this construct to calculate the factorial of 5:

[source,cql]
```
define FactorialOfFive:
  ({ 1, 2, 3, 4, 5 }) Num
    aggregate Result starting 1: Result * Num
```

In this example, the list of integers from `1` to `5` is introduced as the primary source in a query with the alias `Num`, and then the `aggregate` clause is used to calculate the factorial. The result is named `Result` and given the starting value of `1`. This result is then repeatedly multiplied by each integer in the list to produce the final result.

More formally, the `aggregate` clause has the following syntax:

``` ebnf
<aggregate clause> ::=
  aggregate [(all | distinct)] <result alias> [<starting clause>] : <expression>

<starting clause> ::=
  starting (<simple literal> | <quantity | "("<expression>")")
```

The `aggregate` clause may be used in any query instead of a `return` clause, and causes the query to return the result of the last iteration of the aggregate expression. The `all` and `distinct` keywords can be used and apply to the elements prior to the iteration. The `result alias` is an identifier that can be used within the aggregation expression to refer to the current result value, enabling a limited form of recursion. The `starting clause` can be used to provide an initial value for the aggregation. If no starting clause is specified, the aggregation result is initially `null`.

As in the simple example above, the result of the query can be a single value, rather than a list of values, but note that since the aggregate expression may return a list, the result of an `aggregate` query may still be a list:

[source,cql]
```
define "RolledOutIntervals":
  MedicationRequestIntervals M
    aggregate R starting (null as List<Interval<DateTime>>): R union ({
      M X
        let S: Max({ end of Last(R) + 1 day, start of X }),
          E: S + duration in days of X
        return Interval[S, E]
    })
```

In this example, the `aggregate` expression is returning the union of the current result with an interval constructed from the greater of the day after the end of the last interval and the start of the current interval, to the duration in days of the current interval later. The result is a list of non-overlapping intervals where any overlaps in the input list have pushed out subsequent intervals.

A syntax diagram of an `aggregate` clause construct can be seen [here](19-l-cqlsyntaxdiagrams.html#aggregateClause).

Note that in general, since the type of the aggregate expression is not known until the expression can be semantically analyzed, it may be necessary to provide a typed starting expression as illustrated in this example. The starting clause can be omitted if the type of the result can be inferred from the aggregate expression:

[source,cql]
```
define FactorialOfFive:
  ({ 1, 2, 3, 4, 5 }) Num
    aggregate Result: Coalesce(Result, 1) * Num
```

In this example, since the starting clause is omitted, Result is initially [.kw]#null#, and Coalesce must be used to provide the default value of 1, and the type of Integer will be inferred through the Coalesce. Note that although this example only computes the factorial of five, the expand operator could be used to generate a sequence of integers and used to construct a general factorial function.

## Defining Functions

CQL provides for the definition of functions. A function in CQL is a named expression that is allowed to take any number of arguments, each of which has a name and a declared type.

A syntax diagram of a function defintion can be seen [here](19-l-cqlsyntaxdiagrams.html#functionDefinition).

For example:

[source,cql]
```
define function "CumulativeDuration"(Intervals List<Interval<DateTime>>):
  Sum((collapse Intervals) X return all duration in days of X)
```

This statement defines a function named [.id]#CumulativeDuration# that takes a single argument named [.id]#Intervals# of type [.kw]#List<Interval<DateTime>>#. The function returns the sum of duration in days of the collapsed intervals given. This function can then be used just as any other system-defined function:

[source,cql]
```
define "Encounters": [Encounter: "Inpatient Visit"]
define "CD": CumulativeDuration(Encounters E return E.period)
```

These statements establish an expression named CD that computes the cumulative duration of inpatient encounters for a patient.

Within the library in which it is defined, a function can be invoked directly by name. When a function is defined in a referenced library, the local library alias must be used to invoke the function. For example, assuming a library with the above function definition and referenced with the local alias [.id]#Core#:

[source,cql]
```
define "Encounters": [Encounter: "Inpatient Visit"]
define "CD": Core.CumulativeDuration(Encounters E return E.period)
```

In this example, the [.id]#CumulativeDuration# function must be invoked using the local library alias [.id]#Core#.

A syntax diagram of defining a function can be seen [here](19-l-cqlsyntaxdiagrams.html#function).

Functions can be defined that reference other functions anywhere within any library and to any degree of nesting, so long as the reference does not result in a circular reference.

### Operator Functions

Operator functions are system functions defined to support the behavior of operators defined in the language. For example, the addition operator ([.sym]#+#) is implemented by the [.id]#Add# function. Each operator defined in the language has a corresponding system-defined function that surfaces directly in the ELM. For a complete listing of these operators and their ELM function names, refer to the <<06-translationsemantics.adoc#functions,Functions>> topic in the Translation Semantics chapter.

A consequence of having these system function definitions is that operators can also be invoked directly as functions. For example:

[source,cql]
```
exists X
X = Y
```

The above expressions could also be written as:

[source,cql]
```
Exists(X)
Equal(X, Y)
```

### Fluent Functions

[.note-info]
____

Fluent functions are a new feature of CQL 1.5, and are trial-use.
____

Functions can be defined as _fluent_ by including the [.kw]#fluent# keyword as part of the function definition:

[source,cql]
```
define fluent function "confirmed"(Conditions List<Condition>):
  Conditions C where C.verificationStatus ~ "Condition Confirmed"

define fluent function "active"(Conditions List<Condition>):
  Conditions C where C.clinicalStatus ~ "Condition Active"
    and C.abatement is null

define fluent function "activeOrRecurring"(Conditions List<Condition>):
  Conditions C
    where C.clinicalStatus ~ "Condition Active"
      or C.clinicalStatus ~ "Condition Recurrence"
      or C.clinicalStatus ~ "Condition Relapse"
```

A _fluent_ function means that it can be invoked using dot-notation (`.`), and the first argument to the function will be provided by the value of the left-side of the dot-invocation at that point. For example:

[source,cql]
```
define "Diabetes Conditions":
  [Condition: "Diabetes Mellitus"]

define "Confirmed and Active or Recurring Diabetes Conditions":
  Conditions.confirmed().activeOrRecurring()
```

The result of this example is the same as if the functions had been invoked normally:

[source,cql]
```
define "Confirmed and Active or Recurring Diabetes Conditions":
  activeOrRecurring(confirmed(Conditions))
```

In other words, all the Condition elements returned from the [.id]#Diabetes Conditions# expression, where those conditions have a [.id]#verificationStatus# of [.id]#Condition Confirmed#, and a [.id]#clinicalStatus# of [.id]#Condition Active#, [.id]#Condition Recurrence#, or [.id]#Condition Relpase#.

A _fluent_ function may also take multiple arguments where the first argument to the function will be provided by the value of the left-side of the dot-invocation at that point and the second argument can still be passed in. For example:

[source,cql]
```
define fluent function byClinicalStatus(Conditions List<Condition>, Concept status):
  Conditions C where C.clinicalStatus ~ status

// usage
define "Active Diabetes Conditions":
  "Diabetes Conditions".byClinicalStatus("Condition Active")
```

Again, the result of this example is the same as if the function had been invoked normally:

[source,cql]
```
define "Active Diabetes Conditions":
  byClinicalStatus("Diabetes Conditions", "Condition Active")
```

In other words, all the Condition elements returned from the [.id]#Diabetes Conditions# expression, where those conditions have a [.id]#clinicalStatus# of [.id]#Condition Active#.

[.note-info]
____
Note that the examples in this section are adapted from the [CDS Connect FHIR Commons](https://github.com/AHRQ-CDS/AHRQ-CDS-Connect-PAIN-MANAGEMENT-SUMMARY/blob/v0.3.2/src/cql/r4/CDS_Connect_Commons_for_FHIRv400.cql#L178) library.
____

### External Functions

Functions can also be defined as _external_ to support the ability to import functionality defined in external libraries. If a function is defined external, the return type must be provided:

[source,cql]
```
define function "IsSubsumedBy"(code Code, subsumingCode Code) returns Boolean : external
```

CQL does not prescribe the details for how external functions are resolved or implemented, only that an implementation must accept the arguments as specified by the signature, and is expected to return a value of the declared return type.

Take heed that although there may be use cases for which external functions are the best option, they are not without drawbacks. Chief among the drawbacks that arise when using external functions are the challenges associated with interoperability. Since external functions are implementation specific, CQL libraries that are authored relying on external functions are also implementation specific. Therefore, the use of external functions is discouraged because they hinder one of the foundational benefits of CQL, which is data exchange.

## Using FHIRPath

FHIRPath is a general-purpose graph traversal language designed as a simple way to define paths on a hierarchical data model such as FHIR. The language is used within the FHIR specification to provide precise semantics for various items in the specification such as invariants and search parameter paths. Because of the general-purpose nature of FHIRPath, CQL uses the basic expression definition capabilities defined by FHIRPath for its core expression terms. In fact, the ANTLR grammar for CQL imports the FHIRPath grammar and relies on the semantics defined there to define the base expression functionality of CQL, in much the same way that XQuery utilizes XPath to define its expression capabilities. In other words, CQL is a superset of FHIRPath, meaning that any valid FHIRPath expression is also a valid CQL expression.

However, FHIRPath has various implicit conversions defined to simplify expression of common path traversal scenarios. Because CQL is a type-safe language, some of this functionality can optionally be restricted within CQL through the use of several language options, as described in the following sections.

### Path Traversal

Paths in FHIRPath are constructed by concatenating labels using a dot qualifier:

Patient.name.given

In this case, the path begins at the [.id]#Patient# expression and accesses the [.id]#name# property, followed by the [.id]#given# property of each [.id]#name#. Because the [.id]#given# path invocation is targeting the list of names, the property access is invoked for each name in the list, resulting in a list of all the given elements for every name in the Patient.

However, because property access on a list may actually be the result of mistakenly expecting the property to be singular, this behavior can be disabled with the _disable-list-traversal_ option.

### List Promotion and Demotion

In FHIRPath, all operations are defined to return collections, and operations that expect singleton values are defined to throw an error when they are invoked with collections containing multiple elements. In CQL, this behavior is implemented using list promotion and demotion.

Wherever an operator is defined to take a non-list-valued type as a parameter, list demotion allows the arguments to be list-valued and are implicitly converted to a singleton value using the [.kw]#singleton from# operator:

[source,cql]
```
Patient.name.given + ' ' + Patient.name.family
```

The _disable-demotion_ option controls whether or not this expression is valid. With the option enabled, the expression can be compiled, and will evaluate, so long as the run-time values of [.id]#given# and [.id]#family# contain only a single element. With the option disabled, this expression will no longer compile, and the list-valued arguments must be converted to a single value:

[source,cql]
```
Patient.name.given.single() + ' ' + Patient.name.family.single()
```

This allows the compiler to help the author determine whether a singular value is expected and appropriate, or if the author mistakenly assumed the attribute was singular, when in fact the data model allows multiple values.

See the <<Promotion and Demotion>> topic for more discussion on how CQL supports list promotion and demotion.

### Missing Information

FHIRPath traversal operations are defined such that only values that are present are returned. In other words, it does not define a _null_ indicator to represent missing information. Instead, it uses the empty collection ([.sym]#\{ }#) and propagates empty collections in expressions. In general, if the input to an operator or function is an empty collection, the result is an empty collection. This corresponds to the null propogation semantics of CQL, particularly with respect to the three-valued logic semantics of the logical operators.

### Type Resolution

The FHIRPath specification does not require strongly-typed interpretation. In particular, the resolution of property names can be deferred completely to run-time, allowing for flexible use of expressions such as [.id]#.children()# and [.id]#.descendents()#. However, because CQL is a strongly-typed language, these types of expressions are required to be resolved at compile-time.

For example, consider the following FHIRPath:

[source,cql]
```
Patient.children().name
```

This expression returns a list of the name elements of all the children of the Patient instance. To accomplish this in CQL, the result of [.id]#.children()# is a list of elements of choice types, where the types in the choice are the distinct set of types of child elements.

This approach enables the flexibility of FHIRPath expressions but still maintains compile-time type resolution.

### Method Invocation

The FHIRPath syntax is designed as a fluent API, meaning that operations are invoked using a dot-invocation syntax. This functionality is supported in CQL using a syntactic method construct, similar to a lambda function, that allows the invocation to be rewritten as an equivalent function call. The method definition is allowed to declare special variables such as [.id]#$this# that can be addressed in the body of the method.

This mechanism is then used to implement the FHIRPath operators, which are rewritten via the lambda replacement as direct invocations of CQL. The detailed equivalents for all FHIRPath operations are defined in the [FHIRPath Function Translation Appendix](16-i-fhirpathtranslation.html).

The _disable-method-invocation_ option controls whether or not method-style invocation is allowed in the translator.