# Review 1
----------- Overall evaluation -----------
SCORE: 1 (weak accept)

----- TEXT:

## Content
The paper presents an algorithm that checks whether two given GKAT expressions
are equivalent. The algorithm operates on a GKAT-coalgebra that can be computed
on the fly. The algorithm uses two tricks to speed up computation: 1. pruning
the search space when dead states are encountered. 2. using sat solvers to
group transitions (though not explicitly mentioned in Algo 1/2).
The algorithm performs a lot better than the pervious algorithm SymKAT[21]. The
improvement in speed is by a factor of 20 to 100, with SK sometimes hitting the
time limit (and the improvement in memory usage is even bigger).

## Evaluation

(+) It can not be overstated how impressive the benchmark results are.
    (I've reproduced the row e3000b30p200eq)
(+) The idea of theorem 27 is smart: the search space of the algorithm is
    limited by the homomorphic image of a finite coalgebra! The link between
    the the construction (Table 1) and the rule-based coalgebra (Fig 1) is
    interesting on it's own.
(+) The paper achieves a nice span from mathematical theory to a running tool
(-) the introductory chapter on coalgebra II.A isn't carefully written,
    and some claims are simply wrong because they are stated in too general
    terms (see 'concern 2').
    It does not seem to harm correctness of later sections, but still it must
    be corrected! (Also, 'final coalgebra' is introduced but never needed)
(-) the style of writing is ok. The paper should re-state from time to time
    what the main goal is (I assume: checking equivalence of two GKAT
    expressions?) and what remains to be done to reach it. (E.g.: "apply Algo 2
    to two states of the derivate coalgebra D")

1 (weak accept)

## Questions 1: Benchmark Source

Where are the benchmarks from? Did you generate them? (If yes, how? If no, please cite)

## Concern 2: Unnecessary but harmful generalization

Section II.A  recalls universal coalgebra for a Set-functor F, but is not
careful enough when it comes to subtleties, which leads to some wrong
statements in the text. The issues can be solved by assuming that F is finitary
and preserves weak-pullbacks.

* Lemma 1 is only true if F is assumed to preserve weak pullbacks. See
  the asterisk and Convention 4.4 in the cited [23].
* p.1 "principle sub-coalgebra ⟨s⟩_S always exists [23, Theorem 6.4]". This requires
  that F preserves arbitrary (possible infinite) intersections. See also
  footnote 4 in Convention 4.4 in [23].

All issues are addressed by requiring that F is finitary (which is the case
here because T is finite) and weak-pullback-preserving:
weak-pullback-preserving implies that F preserves finite intersections.
Together with finitariness, F preserves all intersections. For details on
intersections and reachability see [B].

## Minor Comments
(Only react here if something is wrong or if there is something important to add)
p.1: why is it relevant to check whether two GKAT expressions are equivalent?
p.2. Lemma 1: Typo 'given' -> 'Given'
p.2. "theorem 6.4" -> "Theorem 6.4"
p.3 "that contains s will" -> "that contain s will"
p.3: why do you call it 'principle sub-coalgebra' instead of 'reachable subcoalgebra'?
p.3: it is unclear, whether you assume the existence of the final F-coalgebra or not. If F is finitary, then the final coalgebra exists.
p.3: placement of Corollary 3 is odd and should rather be placed directly after "there exists a unique homomorphism": By definition, the homomorphisms beh_S and beh_U·h are identical. Thus beh_S(s) = beh_U(h(s)) (it's not even worth a numbered corollary)
p.3: "if statement" -> "if-statement"
p.3: "We use notation like" -> "notations"
p.3: After "A GKAT expression can .. " I first wondered whether I had missed the definition of Kat-expressions. Thus, say that "KAT allows unrestricted +, so we don't give a full definition here"
p.3: usually \sim is used for bisimilarity and not just some bisimulation relation
p.3: is T always finite? you don't say it explicitly, but line 3 in column 2 on p.3 suggests it. Also finiteness of T is required for G being a simple polynominal functor.
p.3: Def At_T: why don't you define At_T as the set of maps T->2?
p.3: Why S×K and not K×S?
p.3: here, you suddenly switch from sigma to delta as the coalgebra structure. Also delta is uncurried whereas the coalgebra structure must be a curried function
p.3: Theorem 4: the plural of coalgebra is inconsistent (throughout the paper, not just here!): In Theorem 4 you both mention "coalgebras" and "coalgebra". (I recommend using "coalgebras")
p.4: Specify whether n >= 0 or n >= 1.
p.4: What is the precise definition von G_w and G?
p.4: line -2: please specify the location within [27] for the finite trace model.
p.5: second paragraph: G isn't really a final coalgebra because strictly speaking, the normal GKAT coalgebras aren't coalgebras (at least not for a set functor). Also, it does not help to use [[-]]_S: norm(S) -> G if you do not define what it actually does. My impression rather is that you do not need final coalgebras at all for the present paper.
p.5: include Definitions in the correct numbering scheme (currently, Definition 2 is between Corollary 14 and Lemma 15)
p.6, Corollary 17: this isn't 'either ... or', because both can be the case, right? If two states s and u are both rejecting everything, then (s,u) are bisimilar and they are both dead.
p.7: it's unclear to me what section III is for if you have section IV
p.8, Algo 1: what is EQ()? is it equivalence of the union-find-structure? 
p.8 "the boolean expressions that s accepts epsilon(s)..." sounds wrong. Do you mean that epsilon(s) is the set of expressions that accept s? If so, say "the set epsion(s) of expressions that s accepts"
p.8, "We name \hat epsilon the accepting...": This criterion looks broken. Is there a comment missing between a and b? Is it \alpha instead of a? Are both really element of \hat epsilon(s)?
p.8, equation (1): The second condition does not type-check at all. I assume the correct definition is "(b,s',p) \in \hat\delta(s)", right?
p.8: what is "lowering"?
p.8: Remark 3: "there exists two" -> "there exist two"
p.8: Remark 3: Also, every GKAT coalgebra is a symbolic GKAT-coalgebra, right?
p.8: what is a symbolic GKAT-coalgebra? I assume it's a coalgebra for the functor Pow(BExp) × Pow(BExp × S × K), right?
p.8, Remark 3: It is probably not worth the focus that symbolic formalisms aren't canonical. (Isn't this the whole point of equivalence checking that differently defined things can behave the same?)
p.9: "the converse is not true": I assume you mean that the functor "is not full"
p.9, line 1 of "Section V": please recall the puzzle here
p.9, Def <B|... is unnecessarily specific to epsilon and delta and thus can not be chained. Strictly speaking, the last line of delta in Table 1 is not defined because <{b}|<epsilon|delta is neither of form <B|epsilon(s) nor of the form <B|delta(s). => Solution: Define <B|E for E ∈ Pow(BExp) and <B|D for D ∈ Pow(BExp×S×K)
p.10: Algorithm 10: please explain in the text or in the algorithm where (and how) sat checking is used.
p.10, Table 1: Many typos here:
  * case +_b definition of delta: shouldn't it be <{b}|delta1 ... + <{\bar b}|delta2 here inead of {b} in the second summand?
  * case +_b definition of epsilon: same as above; {\bar b} instead of {b}
  * case ; definition of delta: \hat\delta_2 instead of \hat\delta2
  * case ^(b) definition of delta: \hat\epsilon_2 instead of \hat\epsilon2
p.10/p.21: proof of Theorem 27: Please, justify the iffs (which definition or rule); otherwise, it is very hard to read/verify
p.11: 'to reduces' -> 'to reduce'
p.12: so what was the timeout? Also, did SK timeout on all tests of the benchmark or only on some?
p.12: so which kernel is now what? are DV/TC the kernels? then why not call the command line flag -k dv and -k tc?
p.12: 'checking also need to' -> 'checking also needs to'
p.12: regarding 'normalization': I guess that greedy bisimulation /
  normalization is a kind of coalgebraic simulation for a suitable relation
  lifting of the GKAT functor, see [A]:
src/syntax/gkat_{sat,bdd}.rs: Typo in HConsign, HashConsign? I assume they should be called HConsing and HashConsing?

## Crash in debug mode
In debug mode, the bdd-backend crashes for me without any helpful message: When I run 'cargo
build' and start your tool, I obtain a backtrace. (sat-backend works fine in
debug; bdd/sat work fine in release mode)

RUST_BACKTRACE=1 ./target/debug/rust-gkat -k k2 -s bdd benchmark/e250b5p10eq/exp42.txt                                     1s

thread 'main' panicked at ~/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/hashconsing-1.6.0/src/lib.rs:559:9:
assertion failed: match prev { None => true, Some(prev) => prev.to_hconsed().is_none(), }
stack backtrace:
   0: rust_begin_unwind
   1: core::panicking::panic_fmt
   2: core::panicking::panic
   3: hashconsing::HConsign<T,S>::insert
             at ~/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/hashconsing-1.6.0/src/lib.rs:559:9
   4: <&mut hashconsing::HConsign<T,S> as hashconsing::HashConsign<T>>::mk_is_new
             at ~/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/hashconsing-1.6.0/src/lib.rs:638:9
   5: hashconsing::HashConsign::mk
             at ~/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/hashconsing-1.6.0/src/lib.rs:605:9
   6: <rust_gkat::syntax::gkat_bdd::BDDGkat as rust_gkat::syntax::gkat::Gkat<rust_gkat::syntax::gkat_bdd::BDDBExp>>::hashcons
             at ./src/syntax/gkat_bdd.rs:175:9
   7: rust_gkat::syntax::gkat::Gkat::mk_test
             at ./src/syntax/gkat.rs:77:9
   8: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:131:17
   9: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:120:26
  10: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:119:26
  11: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:119:26
  12: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:120:26
  13: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:119:26
  14: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:120:26
  15: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:119:26
  16: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:120:26
  17: rust_gkat::syntax::gkat::Gkat::from_exp
             at ./src/syntax/gkat.rs:125:26
  18: rust_gkat::main
             at ./src/main.rs:62:28
  19: core::ops::function::FnOnce::call_once
             at /usr/src/debug/rust/rustc-1.85.0-src/library/core/src/ops/function.rs:250:5
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.

## References
[A] Hughes, Jacobs: Simulations in Coalgebra (2004)
https://doi.org/10.1016/j.tcs.2004.07.022

[B] Wissmann, Milius, Katsumata, Dubut: A Coalgebraic View on Reachability (2019)
https://doi.org/10.14712/1213-7243.2019.026


# Review 2
----------------------- REVIEW 2 ---------------------
SUBMISSION: 233
TITLE: On-the-fly Algorithms for GKAT Equivalences
AUTHORS: Cheng Zhang, Qiancheng Fu, Hang Ji, Ines Santacruz and Marco Gaboardi

----------- Overall evaluation -----------
SCORE: 0 (borderline paper)
----- TEXT:
This is a paper that presents a method for checking equivalence of guarded Kleene algebra with tests. 

The paper combines a number of methods. They fit together very well and apart from a few small issues I found the development easy to read. What I am not sure about is whether the methods are at all surprising on their own or in combination. 

A "GKAT coalgebra" is really just a special kind of automaton. If I understand correctly it is a sort-of deterministic transducer (where the input alphabet is called At_T and the output alphabet is called K) with accept/reject. The actual GKAT syntax is not needed or used, the paper is just about the machines. So, I don't know whether the methods here are already known or unsurprising in the automata community. 

A second concern is that I don't know whether there are people who want GKAT equivalence, apart from the authors and their coauthors. GKAT itself seems slightly artificial to me, because I would have thought that in many situations there will be a more intricate relationship between the actions and the tests, which will need to factor very seriously into the equivalences. For example, if the actions write to memory and the tests read the memory. It is a bit hard for me to imagine situations where there isn't a relationship between actions and tests. 

Some detailed issues. 

NORM is used throughout but it is a bit peculiar. If I understand correctly, there can still be dead states in a normalized coalgebra, it's just that they must immediately reject everything, and they will not be reachable. 

In Definition 2, "least GKAT coalgebra" doesn't make sense. It should be least relation containing ~ and closed under the transition relation. 

There is a mistake in equation (1) on page 8. The delta on the right hand side should be \hat delta, and its arguments and results should have different types. 

In the experiments, can you say a bit more about SymKAT and why it might perform worse? 

At the top of page 12 you briefly mention deterministic symbolic transducers, can you compare more with this and explain why the differences are non-trivial? 

Overall, I didn't quite understand, do you need to use bisimulations, or could you have start states and just compare traces directly? Bisimulations are not so interesting on deterministic automata. Perhaps the bisimulations come in handy when there are powersets in the symbolic version? I could do with a bit more explanation about this. 

For on-the-fly generally, do you want to cite some earlier work, e.g. Fernandez and Mounier, CAV 1991?
----------- Questions for rebuttal -----------
Can you compare more with prior work on symbolic transducers, and explain whether the differences are non-trivial or surprising?


# Review 3
----------------------- REVIEW 3 ---------------------
SUBMISSION: 233
TITLE: On-the-fly Algorithms for GKAT Equivalences
AUTHORS: Cheng Zhang, Qiancheng Fu, Hang Ji, Ines Santacruz and Marco Gaboardi

----------- Overall evaluation -----------
SCORE: -2 (reject)
----- TEXT:
## Overview
  
The authors present an algorithm for deciding GKAT equivalence.

GKAT [27] is a deterministic fragment of KAT (Kleene algebra with
tests).  For a fixed set of tests, the complexity of the problem is
almost linear in GKAT, where it is PSpace-complete for KAT
(essentially, because GKAT expressions denote deterministic automata).

A specificity of GKAT is that when checking equivalence using standard
bisimulation algorithms, one needs to discard dead states in order to
avoid false negatives. This can be done beforehand in linear-time
[27]; here the authors propose to compute and discard dead states
lazily.

They use a coalgebraic-based presentation to justify that the
resulting algorithm is correct.

A remaining difficulty with KAT/GKAT is that the alphabet is
exponential in the number of primitive tests. To deal with this, the
authors show how to turn their algorithm into a symbolic one, where
off-the-shelf SAT solvers can be used to represent and manipulate sets
of letters efficiently. In particular, they show how to lift
Antimirov' derivatives and Thompson construction to this symbolic
setting.

The paper comes with an implementation in Rust, making it possible to
use the above two constructions, either with cudd of minisat as
backend.  The authors give benchmarks where the minisat version is
significantly faster than the cudd version, and where both are orders
of magnitude faster than symkat [27], a symbolic solver for KAT (the
larger PSpace-problem).


## Evaluation

The paper reads well and is correct as far as I can tell.
I could play with the provided implementation.

The announced key contribution (being able to discard dead states
lazily) is not suprising, nor difficult. The authors prove its
correctess by coalgebraic means, but the intuitive argument is fairly
trivial, and the given proof is rather convoluted in the end (I found
it clear and correct, but I was not enthusiastic about the long list
of easy coalgebra theorems recalled from page 2 to 5: Lemma 1, Theorem
2, Corollary 3, Theorem 4, Theorem 5, Lemma 6, Theorem 7, Corollary 8,
Corollary 9, and then slightly more GKAT specific 10,11,12,13,14).

From an algorithmic point of view, there is not much to gain by trying
to delay an up-front linear cost which has to be paid on positive
instances.  I agree it could accelerate the algorithm in negative
instances, but it could slow it down in positive ones (consider
infinite trace equivalent dead expressions: it should be slightly
faster to detect that they are dead than to compute a bisimulation).
In any case, the authors do not give any evidence that this idea
really yields improvements in practice: they do not compare their
algorithm with and without the optimisation, and when playing with the
benchmarks I discovered that the instances they use do not permit to
see any difference.  (The benchmarks and their presentation also
suffer from important problems which I mention below.)

Instead, I think the real contribution of the paper is the proposal of
a symbolic version of the algorithm in [27], in a way that, like in
[30,31] and unlike in [20,21], makes it possible to use
state-of-the-art sat-solvers.

Such an idea is really in the line of [30,31], its adaptation for GKAT
is nice and well-presented, but I cannot consider it as a significant
break-through given the litterature on symbolic automata.

Still, the benchmarks show a real gap between their implementation and
symkat [21]. It is not clear to me whether this comes from this
ability to use cudd/minisat, or from the fact that symkat was designed
for the larger problem KAT.

All in all, I think this paper contains valuable pieces of works, but
not enough for LiCS, and should in any case be largely reworked to
focus on the main contribution and/or properly demonstrate that the
'delayed dead state detection' is useful.
(My grade is severe because I am confident it should be rejected at
LiCS in the current form, but let me repeat that the work is
nevertheless interesting.)


## Problems with the benchmark

The algorithms are run on 250 positive instances, 250 negative
instances (ignoring the 5 "degenerate" ones), but we do not know how
these instances were generated, which is already problematic.

- When looking closely at the positive ones, it appears that 190 of
  them are actually trivial (i.e., they test an expression against
  itself):

  e250b5p10eq    31 trivial 19 non trivial
  e500b5p50eq    30 trivial 20 non trivial
  e1000b10p100eq 41 trivial  9 non trivial
  e2000b20p200eq 44 trivial  6 non trivial
  e3000b30p200eq 44 trivial  6 non trivial
  
  Also, 81 of these 250 instances are actually dead (and thus all
  equivalent)

  Thus I don't think this part of the benchmark is so relevant. At the
  very least it should be presented differently.

  
- The negative instances do not suffer from this problem (by
  definition they cannot be trivial, and in these instances, only 13
  expressions are dead).

  Still, I instrumented the code to compute the length of the
  encountered counter-examples. It appears that it is always 0! In
  other words, the counter-example is always found immediately,
  because the expressions are always different from the very
  beginning, the algorithm doesn't need to explore the automata
  to find it. (In fact, the line with [stack.push] is called only 10
  times in total on the 250 negative instances)

  Thus this side of the benchmark is also non-representative.

The remaining 60 non-trivial positive instances show a clear
improvement over symkat [21], but still, this benchmark should be
entirely reworked.



## Imprecisions

Besides a few typos, there are places where the text is imprecise,
slightly unclear, or misleading.

For instance: the way union-find is used makes the algorithm almost
linear (as explained in [27]), but the complexity analysis on p7c2
describes it as quadratic, and then it is concluded that you
get a similar complexity as in [27].

Or: Theorem 23 is true, but Theorem 23 is not the proper tool to
justify the correctness of the algorithm as mentioned on p7c2 (how
would you use it?)


p1c2 "in contrast to the symbolic KAT derivative [21], the
deterministic nature of GKAT coalgebra allows its symbolic variant to
transition via boolean expressions instead of primitive tests"
-> unclear, and [21] does not "transition via primitive tests"

p2c1 "In particular, we do not define the semantics of GKAT either
algebraically or coalgebraically, as we can avoid directly
interacting with the semantics by leveraging the coalgebraic theory of
GKAT."
-> in the end, you do use its coalgebraic theory


Corollary 3 is not a corollary of what is above (bisimulations), it is
an immediate consequence of finality.

Not clear why we need to bother with Theorem 4

Theorem 5 and Lemma 6 are really trivial

Rather than those standard/easy things, it would be better to spend
more time to properly define the expected semantics of GKAT and
explain the subtelties with finite/infinite traces: this is done too
quickly at the beginning of Section I.D, and this is hard to follow
without reading [27]


p5c2 "This approach offers several benefits: first, the
liveness detection is only called when necessary, thus
unlikely to iterate through the entire coalgebra; sec-
ond, the algorithm can short-circuit on a counter-
example, finally [...]"
-> The first two are the same benefit.
-> Also announce that you can easily cache these computations,
   so that they won't be repeated.

p7c2 "In the extreme case when the two input states are infinite-
trace equivalent, the on-the-fly algorithm can even
skip liveness checking entirely."
-> yes, but in that case the liveness analysis would be
   faster (linear instead of almost linear)

p8 symbolic derivatives
-> say somewhere that these are more Antimorov' partial derivatives
   than Brzozowski's derivatives

it would also make sense to directly define \hat epsilon as a function
from S to Bexp (rather than P(Bexp)): this is more intuitive, and this
is what you do in the implementation

p10, please properly explain what are those "start" dynamics (or don't
talk about them...)


## Minor

double check 's's (singular/plural + verbs)

p2c2 "we can restrict the homomorphism map into an inclusion"
-> rephrase

p3c2 "which are conjunctions of all the primitive tests either in its positive or negative form:"
-> rephrase

Lemma 15 eager -> greedy

p7c1: say that listing 1 is in appendix

p7c2: "the correctness of this optimisation is justified by." 

Cor 29 "at most linear *in* the size"


p12c2 focus*es*, need*s*, stem*s*


------------------------------------------------------
