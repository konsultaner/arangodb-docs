---
layout: default
description: AQL supports a number of operators that can be used in expressions
---
Operators
=========

AQL supports a number of operators that can be used in expressions.  There are
comparison, logical, arithmetic, and the ternary operator.

Comparison operators
--------------------

Comparison (or relational) operators compare two operands. They can be used with
any input data types, and return a boolean result value.

The following comparison operators are supported:

| Operator   | Description
|:-----------|:-----------
| `==`       | equality
| `!=`       | inequality
| `<`        | less than 
| `<=`       | less or equal
| `>`        | greater than
| `>=`       | greater or equal
| `IN`       | test if a value is contained in an array
| `NOT IN`   | test if a value is not contained in an array
| `LIKE`     | tests if a string value matches a pattern
| `NOT LIKE` | tests if a string value does not match a pattern
| `=~`       | tests if a string value matches a regular expression
| `!~`       | tests if a string value does not match a regular expression

Each of the comparison operators returns a boolean value if the comparison can
be evaluated and returns *true* if the comparison evaluates to true, and *false*
otherwise.

The comparison operators accept any data types for the first and second
operands. However, `IN` and `NOT IN` only return a meaningful result if
their right-hand operand is an array. `LIKE` and `NOT LIKE` only execute
if both operands are string values. All four operators do not perform
implicit type casts if the compared operands have different types, i.e.
they test for strict equality or inequality (`0` is different to `"0"`,
`[0]`, `false` and `null` for example).

```aql
     0  ==  null            // false
     1  >   0               // true
  true  !=  null            // true
    45  <=  "yikes!"        // true
    65  !=  "65"            // true
    65  ==  65              // true
  1.23  >   1.32            // false
   1.5  IN  [ 2, 3, 1.5 ]   // true
 "foo"  IN  null            // false
42  NOT IN  [ 17, 40, 50 ]  // true
 "abc"  ==  "abc"           // true
 "abc"  ==  "ABC"           // false
 "foo"  LIKE  "f%"          // true
 "foo"  NOT LIKE  "f%"      // false
 "foo"  =~  "^f[o].$"       // true
 "foo"  !~  "[a-z]+bar$"    // true
```

The `LIKE` operator checks whether its left operand matches the pattern specified
in its right operand. The pattern can consist of regular characters and wildcards.
The supported wildcards are `_` to match a single arbitrary character, and `%` to
match any number of arbitrary characters. Literal `%` and `_` need to be escaped
with a backslash. Backslashes need to be escaped themselves, which effectively
means that two reverse solidus characters need to precede a literal percent sign
or underscore. In arangosh, additional escaping is required, making it four
backslashes in total preceding the to-be-escaped character.

```aql
    "abc" LIKE "a%"          // true
    "abc" LIKE "_bc"         // true
"a_b_foo" LIKE "a\\_b\\_foo" // true
```

The pattern matching performed by the `LIKE` operator is case-sensitive.

The `NOT LIKE` operator has the same characteristics as the `LIKE` operator
but with the result negated. It is thus identical to `NOT (… LIKE …)`. Note
the parentheses, which are necessary for certain expressions:

```aql
FOR doc IN coll
  RETURN NOT doc.attr LIKE "…"
```

The return expression gets transformed into `LIKE(!doc.attr, "…")`, leading
to unexpected results. `NOT(doc.attr LIKE "…")` gets transformed into the
more reasonable `! LIKE(doc.attr, "…")`.

The regular expression operators `=~` and `!~` expect their left-hand operands to
be strings, and their right-hand operands to be strings containing valid regular
expressions as specified in the documentation for the AQL function
[REGEX_TEST()](functions-string.html#regex_test).

Array comparison operators
--------------------------

Most comparison operators also exist as an *array variant*. In the array variant,
a `==`, `!=`, `>`, `>=`, `<`, `<=`, `IN`, or `NOT IN` operator is prefixed with
an `ALL`, `ANY`, or `NONE` keyword. This changes the operator's behavior to
compare the individual array elements of the left-hand argument to the right-hand
argument. Depending on the quantifying keyword, all, any, or none of these
comparisons need to be satisfied to evaluate to `true` overall.

You can also combine one of the supported comparison operators with the special
`AT LEAST (<expression>)` operator to require an arbitrary number of elements
to satisfy the condition to evaluate to `true`. You can use a static number or
calculate it dynamically using an expression.

```aql
[ 1, 2, 3 ]  ALL IN  [ 2, 3, 4 ]  // false
[ 1, 2, 3 ]  ALL IN  [ 1, 2, 3 ]  // true
[ 1, 2, 3 ]  NONE IN  [ 3 ]       // false
[ 1, 2, 3 ]  NONE IN  [ 23, 42 ]  // true
[ 1, 2, 3 ]  ANY IN  [ 4, 5, 6 ]  // false
[ 1, 2, 3 ]  ANY IN  [ 1, 42 ]    // true
[ 1, 2, 3 ]  ANY ==  2            // true
[ 1, 2, 3 ]  ANY ==  4            // false
[ 1, 2, 3 ]  ANY >  0             // true
[ 1, 2, 3 ]  ANY <=  1            // true
[ 1, 2, 3 ]  NONE <  99           // false
[ 1, 2, 3 ]  NONE >  10           // true
[ 1, 2, 3 ]  ALL >  2             // false
[ 1, 2, 3 ]  ALL >  0             // true
[ 1, 2, 3 ]  ALL >=  3            // false
["foo", "bar"]  ALL !=  "moo"     // true
["foo", "bar"]  NONE ==  "bar"    // false
["foo", "bar"]  ANY ==  "foo"     // true

[ 1, 2, 3 ]  AT LEAST (2) IN  [ 2, 3, 4 ]  // true
["foo", "bar"]  AT LEAST (1+1) ==  "foo"   // false
```

Note that these operators do not utilize indexes in regular queries.
The operators are also supported in [SEARCH expressions](operations-search.html),
where ArangoSearch's indexes can be utilized. The semantics differ however, see
[AQL `SEARCH` operation](operations-search.html#array-comparison-operators).

Logical operators
-----------------

The following logical operators are supported in AQL:

- `&&` logical and operator
- `||` logical or operator
- `!` logical not/negation operator

AQL also supports the following alternative forms for the logical operators:

- `AND` logical and operator
- `OR` logical or operator
- `NOT` logical not/negation operator

The alternative forms are aliases and functionally equivalent to the regular 
operators.

The two-operand logical operators in AQL are executed with short-circuit 
evaluation (except if one of the operands is or includes a subquery. In this
case the subquery is pulled out an evaluated before the logical operator).

The result of the logical operators in AQL is defined as follows:

- `lhs && rhs` returns `lhs` if it is `false` or would be `false` when converted
  to a boolean. If `lhs` is `true` or would be `true` when converted to a boolean,
  `rhs` is returned.
- `lhs || rhs` returns `lhs` if it is `true` or would be `true` when converted
  to a boolean. If `lhs` is `false` or would be `false` when converted to a boolean,
  `rhs` is returned.
- `! value` returns the negated value of `value` converted to a boolean

```aql
u.age > 15 && u.address.city != ""
true || false
NOT u.isInvalid
1 || ! 0
```

Passing non-boolean values to a logical operator is allowed. Any non-boolean operands 
are casted to boolean implicitly by the operator, without making the query abort.

The *conversion to a boolean value* works as follows:
- `null` is converted to `false`
- boolean values remain unchanged
- all numbers unequal to zero are `true`, zero is `false`
- an empty string is `false`, all other strings are `true`
- arrays (`[ ]`) and objects / documents (`{ }`) are `true`, regardless of their contents

The result of *logical and* and *logical or* operations can now have any data 
type and is not necessarily a boolean value.

For example, the following logical operations return boolean values:

```aql
25 > 1  &&  42 != 7                        // true
22 IN [ 23, 42 ]  ||  23 NOT IN [ 22, 7 ]  // true
25 != 25                                   // false
```

… whereas the following logical operations do not return boolean values:

```aql
   1 || 7                                  // 1
null || "foo"                              // "foo"
null && true                               // null
true && 23                                 // 23
```
   
Arithmetic operators
--------------------

Arithmetic operators perform an arithmetic operation on two numeric
operands. The result of an arithmetic operation is again a numeric value.

AQL supports the following arithmetic operators:

- `+` addition
- `-` subtraction
- `*` multiplication
- `/` division
- `%` modulus

Unary plus and unary minus are supported as well:

```aql
LET x = -5
LET y = 1
RETURN [-x, +y]
// [5, 1]
```

For exponentiation, there is a [numeric function](functions-numeric.html#pow) *POW()*.
The syntax `base ** exp` is not supported.

For string concatenation, you must use the [`CONCAT()` string function](functions-string.html#concat).
Combining two strings with a plus operator (`"foo" + "bar"`) does not work!
Also see [Common Errors](common-errors.html).

```aql
1 + 1
33 - 99
12.4 * 4.5
13.0 / 0.1
23 % 7
-15
+9.99
```

The arithmetic operators accept operands of any type. Passing non-numeric values to an 
arithmetic operator casts the operands to numbers using the type casting rules 
applied by the [TO_NUMBER()](functions-type-cast.html#to_number) function:

- `null` is converted to `0`
- `false` is converted to `0`, `true` is converted to `1`
- a valid numeric value remains unchanged, but NaN and Infinity are converted to `0`
- string values are converted to a number if they contain a valid string representation
  of a number. Any whitespace at the start or the end of the string is ignored. Strings
  with any other contents are converted to the number `0`
- an empty array is converted to `0`, an array with one member is converted to the numeric
  representation of its sole member. Arrays with more members are converted to the number 
  `0`.
- objects / documents are converted to the number `0`.

An arithmetic operation that produces an invalid value, such as `1 / 0`
(division by zero), produces a result value of `null`. The query is not
aborted, but you may see a warning.

```aql
   1 + "a"       // 1
   1 + "99"      // 100
   1 + null      // 1
null + 1         // 1
   3 + [ ]       // 3
  24 + [ 2 ]     // 26
  24 + [ 2, 4 ]  // 24
  25 - null      // 25
  17 - true      // 16
  23 * { }       // 0
   5 * [ 7 ]     // 35
  24 / "12"      // 2
   1 / 0         // null (with a 'division by zero' warning)
```

Ternary operator
----------------

AQL also supports a ternary operator that can be used for conditional
evaluation. The ternary operator expects a boolean condition as its first
operand, and it returns the result of the second operand if the condition
evaluates to true, and the third operand otherwise.

In the following example, the expression returns `u.userId` if `u.age` is
greater than 15 or if `u.active` is `true`. Otherwise it returns `null`:

```aql
u.age > 15 || u.active == true ? u.userId : null
```

There is also a shortcut variant of the ternary operator with just two
operands. This variant can be used if the expression for the boolean
condition and the return value should be the same.

In the following example, the expression evaluates to `u.value` if `u.value` is
truthy. Otherwise, a fixed string is given back:

```aql
u.value ? : 'value is null, 0 or not present'
```

The condition (here just `u.value`) is only evaluated once if the second
operand between `?` and `:` is omitted, whereas it would be evaluated twice
in case of `u.value ? u.value : 'value is null'`.

{% hint 'info' %}
Subqueries that are used inside expressions are pulled out of these
expressions and executed beforehand. That means that subqueries do not
participate in lazy evaluation of operands, for example, in the
ternary operator. Also see
[evaluation of subqueries](fundamentals-subqueries.html#evaluation-of-subqueries).
{% endhint %}

Range operator
--------------

AQL supports expressing simple numeric ranges with the `..` operator.
This operator can be used to easily iterate over a sequence of numeric
values.

The `..` operator produces an array of the integer values in the 
defined range, with both bounding values included.

```aql
2010..2013
```

The above example produces the following result:

```json
[ 2010, 2011, 2012, 2013 ]
```

Using the range operator is equivalent to writing an array with the integer
values in the range specified by the bounds of the range. If the bounds of
the range operator are non-integers, they are converted to integer values first.

There is also a [`RANGE()` function](functions-numeric.html#range).

Array operators
---------------

AQL provides different array operators:

- `[*]` for [array variable expansion](advanced-array-operators.html#array-expansion)
- `[**]` for [array contraction](advanced-array-operators.html#array-contraction)
- `[? ...]` known as the [question mark operator](advanced-array-operators.html#question-mark-operator)
  for nested search

Operator precedence
-------------------

The operator precedence in AQL is similar as in other familiar languages
(highest precedence first):

| Operator(s)          | Description
|:---------------------|:-----------
| `::`                 | scope (user-defined AQL functions)
| `[*]`                | array expansion
| `[]`                 | indexed value access (of arrays)
| `.`                  | member access (of objects)
| `()`                 | function call
| `!`, `NOT`, `+`, `-` | unary not (logical negation), unary plus, unary minus
| `*`, `/`, `%`        | multiplication, division, modulus
| `+`, `-`             | addition, subtraction
| `..`                 | range operator
| `<`, `<=`, `>=`, `>` | less than, less equal, greater equal, greater than
| `IN`, `NOT IN`       | in operator, not in operator
| `==`, `!=`, `LIKE`, `NOT LIKE`, `=~`, `!~`  | equality, inequality, wildcard match, wildcard non-match, regex match, regex non-match
| `AT LEAST`           | at least modifier (array comparison operator, question mark operator)
| `OUTBOUND`, `INBOUND`, `ANY`, `ALL`, `NONE` | graph traversal directions, array comparison operators, question mark operator
| `&&`, `AND`          | logical and
| `||`, `OR`           | logical or
| `INTO`               | into operator (INSERT / UPDATE / REPLACE / REMOVE / COLLECT operations)
| `WITH`               | with operator (WITH / UPDATE / REPLACE / COLLECT operations)
| `=`                  | variable assignment (LET / COLLECT operations, AGGREGATE / PRUNE clauses)
| `?`, `:`             | ternary operator, object literals
| `DISTINCT`           | distinct modifier (RETURN operations)
| `,`                  | comma separator

The parentheses `(` and `)` can be used to enforce a different operator
evaluation order.
