Boolean Fault tree Logic (BFL)
==================
Boolean Fault tree Logic (BFL) is a logic designed by Nicoletti et al. to
formally define properties of fault trees [1]. A set of fault tree models and properties specified using BFL can be found in this repository.

# Syntax

For an example of a BFL file, see [examples/case-study-1.bfl](examples/case-study-1.bfl).

## General Information

A `bfl` file contains a fault tree and at least one BFL query.
The fault tree and BFL queries are separated by a line with `---`.
BFL queries must end with a semicolon.
Single line comments are supported and start with `//`.
BFL ignores whitespace as much as possible, i.e., `a && b` is the same as
`a&&b`.
In BFL, symbols/keywords/operators are case-insensitive, but event names are
not.

## Fault Tree Syntax

The syntax for the fault tree part of the file is based on the Galileo fault
tree format [2].
Each statement must end with a semicolon.
The first statement declares the toplevel:
```
toplevel <toplevel-name>;
```
After that, all events can be defined.

Intermediate events are defined by starting with their name, then their gate type,
and finally a list of their children:
```
<intermediate-event> <gate> <child1> <child2> ... <child-n>;
```
The list of children are whitespace separated.
For example, to define an intermediate event `a` that has an AND gate and
two children `b` and `c`, we can write `a AND b c;`.
All gate types are explained below.

Basic events can be defined implicitly, for example, if we have `a AND b c`,
`b` and `c` are assumed to be basic events unless there is also a definition
for those events that make them an intermediate event.
Alternatively, a basic event can also be defined explicitly, like so: `b;`

### Gate types

BFL supports three types of gates: AND, OR, and a voting gate.
The syntax for the gates is case-insensitive.

An AND gate can be written with `AND`, an OR gate can be written with `OR`.

A standard voting gate can be written exactly the same as in the Galileo
format: `<k>of<n>`, e.g., `2of3` if at least 2 out of 3 children must occur
for the fault to propagate.

A non-standard extended voting gate which uses any comparison of
`<`, `<=`, `==`, `>=`, `>` can also be employed. It can be used to specify that at **most** or
**exactly** `k` events must occur for the fault to propagate.
For example, with `k=2`, `VOT<=2` means at most 2 child events must occur, and
`VOT==2` means exactly 2 events must occur.

## BFL Syntax

### Logical connectives

All logical connectives can be expressed either with C-like or LaTeX-like
operators:

- Logical NEGATION: `!` or `\neg`
- Logical AND: `&&` or `\land`
- Logical OR: `||` or `\lor`
- Logical IMPLICATION: `=>` or `\implies`
- Logical EQUIVALENCE: `==` or `\equiv`
- Logical NONEQUIVALENCE: `!=` or `\not \equiv`

### Setting evidence

You can use the following syntax to set evidence for a single
event: `<formula>[e: 0]`.
Here, event `e` is set to `0`.
To set evidence for multiple events, use commas to separate the
events: `<formula>[a: 0, b: 1, c: 0]`.

### MCS and MPS operators

The MCS and MPS operators can be used with `\MCS` and `\MPS`, respectively.
The argument must be wrapped in parentheses, e.g., `\MCS(a)` is valid
while `\MCS a` is not.

### VOT operator

The VOT operator can be used with `\VOT`.
Use square brackets to enter the comparison (one of `<`, `<=`, `==`, `>=`, `>`)
and the number to compare against (`k`).
Then, wrap the input BFL formulas in parentheses and separate them with commas.
Example:

```
\VOT[>2](a && b, c || d, !e);
```

### IDP operator

The IDP operator can be used with `\IDP`.
The IDP operator takes two arguments, separated with a comma and surrounded by
parenthesis, e.g., `\IDP(a && b, a || c);`.

### SUP operator

The SUP operator can be used with `\SUP`.
It takes one input: an event in the fault tree.
Again, the input is wrapped in parentheses: `\SUP(a);`.

### Quantifiers

The existential quantifier can be used with `\exists`, and the universal
quantifier can be used with `\forall`.
It is possible to wrap the BFL formula in parentheses, but this is not
mandatory, i.e., `\exists(a);` and `\exists a;` are equivalent.

## BFL Queries

There are three types of BFL queries that can be executed.
All BFL queries are executed with respect to the fault tree at the start of
the file.

### Satisfaction relation query

With this query, you can check whether a status vector satisfies a BFL formula.
If so, the program will print `True`.
If not, the program will print a counterexample.
This is written using a comma-separated list of basic events that have a
status of 1, then a `|=` symbol, and then the BFL formula.
For example, `a, c |= a && (b || c);`.
Instead of `|=`, you can also write `\models`.

### Statisfaction set query

This query returns all satisfying status vectors for a BFL formula.
It can be written as `[[<formula>]];`, e.g., `[[\MCS(a)]];`

### Quantified query

A quantified query is a query that starts with one of the two quantifiers.
It returns either `True` or `False`.
`\exists <formula>;` returns `True` iff there exists a status vector that 
satisfies the formula.
`\forall <formula>;` returns `True` iff all possible status vectors satisfy 
the formula.

### IDP query

The IDP query is used like `\IDP(<formula1>, <formula2>);` and returns `True` 
iff `<formula1>` and `<formula2>` are independent.

### SUP query

The SUP query is used like `\SUP(<event-name>);` and returns `True` iff 
`event-name` is superfluous, i.e., it is independent of the top-level event.

# References

[1] S. M. Nicoletti, E. M. Hahn, and M. Stoelinga, “*BFL: a Logic to Reason about Fault Trees*”.
in 2022 52nd Annual IEEE/IFIP International Conference on Dependable Systems and Networks (DSN), Jun. 2022, pp. 441–452.
doi: [10.1109/DSN53405.2022.00051](https://doi.org/10.1109/DSN53405.2022.00051)

[2] K. Sullivan and J. B. Dugan, “*Galileo User’s Manual & Design Overview (Version 2.11-Alpha)*”.
url: https://www.cse.msu.edu/~cse870/Materials/FaultTolerant/manual-galileo.htm
