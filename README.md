# Assignment 3

## Deadline: November 6, 11:59PM

We defined a static analysis to summarize the different results
derivation of a regular expression produces in class. We also talked
about how to use it to convert a regular expression to a DFA. In this
assignment, you will implement:

 - Integrate the simplifications you implemented in previous
   assignment into the convenience methods we provide that normalize a
   regular expression while building it.
 - Implement the static analysis that we discussed in class, and use
   it to convert a regular expression into an almost minimal DFA.
 - Write tests for normalizations, the analysis, and the DFA.

## Project structure

The repository includes the following:

  - `src/main/scala/Derive.scala` - contains a recursive
    implementation of derivative parsing we developed in class. Use
    this as a reference to check the behavior of your VM
    implementation. *Do not change this file.*
  - `src/main/scala/RangeSet.scala` - implements dense sets based on
    intervals. *Do not change this file.*
  - `src/main/scala/Regex.scala` - contains the abstract syntax for
    regular expressions, as well as toString and pretty printing
    methods. *Do not change this file.*
  - `src/main/scala/Util.scala` - contains some useful utilities. Feel
    free to add things to this file if you need to.
  - `src/main/scala/RichRegex.scala` - contains the skeleton code for
    a rich regex builder library. *You should integrate your regex
    builders with simplifications into the builder methods we
    provide.*
  - `src/main/scala/DerivativeMachine.scala` - contains the skeleton
    code for VM-based derivative matching. *You should copy over your
    derivative machine implementation to this file.*
  - `src/main/scala/DerivativeAnalysis.scala` - contains the skeleton
    code for the static analysis and DFA construction you need to
    implement. *You should implement the methods in this class.*
  - `src/main/scala/Dfa.scala` - the DFA implementation that we
    implemented during the livecoding session in class. *You should
    not change this file.*

  `src/test/scala/*` - Contains skeleton code for unit tests. *You
  should implement your own tests in this directory.*

## Part 1: Integrating rich regex builder & simplifications

You should integrate the simplifications you implemented for last
assignment into the builder methods in `RichRegex.scala`. Your
simplifications should be integrated into `~`, `|`, `&` methods. You
can carry over your implementation of `*`, `+`, `unary_!`, `<=`, `>=`,
`<>` methods directly.

## Part 2: Static Analysis

You will implement `analyze` which will use the private details, set
up the initial call for `computeDfa` and compute the DFA that
corresponds to the given regular expression.

`computeDfa` will compute the transitions and set of reachable states
for all regexes in `todo` using `computeNext`, adding changed/new
states to the `todo` list and calling itself recursively until `todo`
is exhausted.

### Definition of `C` and `computeNext`

In class, we defined a function `C : Regex → Set[CharSet]`. `C(r)`
partitions the set of all characters (our alphabet, Σ) into a set of
partitions (which are character sets), and if two characters `a` and
`b` are in the same partition, derivative of `r` with respect to `a`
and `b` result in the same regular expression. Formally, if `a, b ∈ S
∈ C(r)` then `δ_a(r) = δ_b(r)`.

So, if `a` and `b` are in the same partition then the state
corresponding to `r` should transition to the same state when reading
either `a` or `b` in the DFA you generate.

The definition of `C` for different cases of regular expressions is as
follows:

```
    C(∅) = { Σ }
    C(ε) = { Σ }
C(S ⊆ Σ) = { S, Σ \ S } // S is a character set
   C(r*) = C(r)
   C(!r) = C(r)
C(r | s) = C(r) ∧ C(s)
C(r & s) = C(r) ∧ C(s)
C(r ~ s) = C(r) if !r.nullable, else C(r) ∧ C(s)
```

where `∧` binary operator on sets of character sets is defined as:

```
P ∧ Q = { S ∩ T | S ∈ P, T ∈ Q }
```

For example, {A, B} ∧ {C, D} = {A ∩ C, A ∩ D, B ∩ C, B ∩ D} where A,
B, C, D are character sets.

Based on this definition, you need to implement `computeNext` method
which needs to compute `C`, use it to compute the transitions, and the

## Part 3: Tests

You will need to write your own unit tests. Testing your own code *is
part of the grade*. We are looking for the following:
  - Non-trivial tests, i.e., a test that demonstrates that creating a
    charset creates a charset is not very useful.
  - Decent coverage - we don't expect you to go for a 100% test
    coverage, but we do expect you to cover edge cases.

Ultimately, quality of the tests is as important as the quantity. You
need to test:
 - the normalization code given to you and how it interacts with the
   simplifications you implement. The tests for this should go into
   `RegexSpec.scala`.
 - the public API of `DerivativeAnalysis` that you will implement. The
   tests for this should use the public API of the resulting DFA to
   test the behavior and these tests should go into the skeleton code
   we gave you in `DerivativeAnalysisSpec.scala`

## Rules

You must use only the purely functional subset of Scala. This means
that you are not allowed to use mutations; more explicitly you must
not use any of:
  - Mutable variables, i.e., those created using the `var` keyword,
  - Mutable collections, e.g. anything under
    `scala.collection.mutable`,
  - The `Array` data type.

If you use any mutation, you will automatically fail the assignment.

Your code must compile. Invoking `compile` and `test:compile` in the
SBT shell (as described in the first tutorial) must
succeed. Otherwise, you will automatically fail the assignment.

## Submission

We will use only the contents of the `src` directory for grading so
make sure that all your code is in the proper directories under it.
You will use `turnin` on CSIL to submit your assignment.  To submit
your assignment, on the root directory of the repository you cloned,

  1. Make sure that you run the unit tests on CSIL and get the result
       you expect.
  2. run `turnin assign3@cs162 src`.
  3. Read the instructions on screen and the list of files you are
       submitting carefully and submit the assignment only if you are
       sure that you are submitting all the files.

Good luck!
