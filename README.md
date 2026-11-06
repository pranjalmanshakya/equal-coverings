# Equal Coverings of Finite Groups

**Theory, Lean Formalization, and Computational Evidence**

---

This repository contains all materials for a Third Year Project in B.Sc. Computational Mathematics at the Department of Mathematics, Kathmandu University, Dhulikhel, Nepal.

**Authors:** Bishesh Bohora, Ashwot Acharya, Pranjal Man Shakya, Bhoomika Thapa

**Supervisor:** Dr. Gokul KC, Associate Professor, Department of Mathematics

**GitHub:** https://github.com/bishesh4562r/EqCovering

---

## Table of Contents

- [What This Project Is About](#what-this-project-is-about)
- [Key Definitions](#key-definitions)
- [The Central Conjecture](#the-central-conjecture)
- [Repository Structure](#repository-structure)
- [Theoretical Results](#theoretical-results)
- [Lean 4 Formalization](#lean-4-formalization)
- [GAP Computational Investigation](#gap-computational-investigation)
- [How to Use the GAP Code](#how-to-use-the-gap-code)
- [How to Build the Lean Project](#how-to-build-the-lean-project)
- [How to Compile the LaTeX Document](#how-to-compile-the-latex-document)
- [Results Summary](#results-summary)
- [Future Directions](#future-directions)
- [References](#references)

---

## What This Project Is About

A **covering** of a finite group G is a collection of proper subgroups whose union equals G. An **equal covering** adds the constraint that every subgroup in the covering has the same order. This is a classical area of group theory originating with Scorza in 1926, revived by Neumann, Cohn, Haber, Rosenfeld, and many others.

This project extends the work of Velasquez-Berroteran (2022), which developed the theory of equal coverings and computationally classified groups up to order 60. We pursue three parallel strands:

1. **Theoretical** -- We provide complete, rigorous proofs of foundational results on group coverings (including the Haber-Rosenfeld theorem and Scorza's theorem) that the original paper invokes but does not prove in full. We also investigate a new direction toward the simple group conjecture for alternating groups.

2. **Formal** -- We formalize six key theorems in Lean 4 using a self-contained algebraic core, with two additional theorems stated as axioms pending full proofs.

3. **Computational** -- We extend the GAP-based classification of groups with equal coverings from order 60 to order 120, producing a complete table of results with supporting theoretical justifications.

---

## Key Definitions

**Covering.** A collection of proper subgroups of G whose set-theoretic union is G. The covering number sigma(G) is the minimum number of subgroups in any covering. We set sigma(G) = infinity if G is cyclic.

**Equal Covering.** A covering where all subgroups have the same order. The equal covering number epsilon(G) is the minimum cardinality of any equal covering. We write epsilon(G) = infinity if no equal covering exists.

**Irredundant Covering.** A covering where every subgroup contains at least one element not in any other subgroup of the covering.

**Exponent.** The exponent of G, written exp(G), is the smallest positive integer n such that g^n = 1 for all g in G. For finite G, exp(G) equals the least common multiple of all element orders.

**Semidirect Product.** G is the semidirect product of H and K (written G = H semidirect K) if H is normal in G, HK = G, and H intersect K = {1}.

**Partition.** A covering where any two distinct members intersect only at the identity. The partition number rho(G) is the minimum number of subgroups in any partition. Every equal partition is an equal covering, so epsilon(G) <= rho(G) whenever both are finite.

---

## The Central Conjecture

**Conjecture (Velasquez-Berroteran, 2022).** If G is a finite simple group, then G has no equal covering.

This conjecture is computationally verified for all finite simple groups of order less than 100,000. No counterexample has been found. Our project adds further computational evidence by testing all groups of orders 61 through 120, and we outline a theoretical approach toward proving the conjecture for alternating groups A_p where p is prime and p >= 5.

---

## Repository Structure

```
EqCovering/
|
|-- documentation.tex          Main LaTeX document (full project report)
|-- documentation.pdf          Compiled PDF of the report
|-- references.bib             BibTeX bibliography file
|-- logo.png                   University logo
|-- .gitignore                 Ignores LaTeX build artifacts
|
|-- FormalizedTheorems/
|   |-- Lean/                  Lean 4 project directory
|       |-- EqualCovering.lean         Top-level import file
|       |-- lakefile.toml              Lean build configuration
|       |-- lean-toolchain             Lean version (4.27.0)
|       |-- README.md                  Lean project README
|       |-- EqualCovering/
|           |-- Group.lean             Group, Subgroup, IsSubgroup definitions
|           |-- Covering.lean          Covering and EqualCovering structures
|           |-- Finite.lean            Finite group axioms (order, exponent, Lagrange)
|           |-- Products.lean          Direct product of groups and product subgroups
|           |-- Homomorphisms.lean     Group homomorphisms, surjectivity, preimage subgroups
|           |-- Theorems.lean          All formalized theorems (1, 2, 13, 15, 16, 17, 19)
|       |-- Test/
|           |-- firstlean.lean         Test/scratch file
|
|-- GAPfiles/
|   |-- Original Code/
|   |   |-- author.g                  Original algorithm by Velasquez-Berroteran
|   |
|   |-- Normal Range Code/
|   |   |-- s.g                       Improved version with bucketing and bitset coverage
|   |
|   |-- Optimizations/
|   |   |-- 1a_prune.g               Candidate order pruning (with known bug)
|   |   |-- 1b_theorem_fallback.g    Theorem-based yes/no shortcuts
|   |   |-- 2a_avoid_allsubgroups.g  Subgroup bucketing to avoid repeated AllSubgroups
|   |   |-- 2b_hash_map.g            Hash map for element-to-index lookup
|   |   |-- 2c_early_exit.g          Early exit once all elements are covered
|   |   |-- 2d_cache_result          Caching results by group ID
|   |
|   |-- Formulized Optimized code/
|   |   |-- t.g                      Final fully optimized code (all 7 optimizations)
|   |
|   |-- Results ( 120 Order )/       Output/results files from scanning orders 61-120
|
|-- presentation/
    |-- presentation.tex             Beamer presentation (Deep Reef color theme)
```

---

## Theoretical Results

We proved the following foundational results in full, filling gaps left by Velasquez-Berroteran (2022):

**Theorem 1.** A group G has a covering if and only if G is non-cyclic.

**Theorem 2.** No group is the union of exactly two proper subgroups. That is, sigma(G) is never equal to 2.

**Scorza's Theorem (1926).** sigma(G) = 3 if and only if there exists a normal subgroup N of G such that G/N is isomorphic to the Klein four-group C_2 x C_2. The proof constructs a bijection between the cosets of N and the elements of the Klein four-group, verifying the multiplication table is preserved.

**Haber-Rosenfeld Theorem.** Let p be the smallest prime divisor of |G|. Then G cannot be the union of p or fewer proper subgroups. Furthermore, if a covering uses exactly p+1 subgroups, then at least one subgroup has index p. If that subgroup is normal, then every subgroup in the covering has index p and p^2 divides |G|.

**Covering Sum Lemma.** If G = union of H_i for i = 1 to n, then the sum over i of 1/[G:H_i] is at least 1.

**Quotient Monotonicity.** If N is normal in G, then sigma(G) <= sigma(G/N).

**Product Formula.** sigma(H x K) <= min{sigma(H), sigma(K)}. When gcd(|H|, |K|) = 1, equality holds.

**Theorem 13.** If Pi = {H_i} is an equal covering of G, then exp(G) divides |H_i| for all i. This is the master constraint that drives both the GAP search space and the alternating group argument.

**Corollary 1.** If exp(G) does not divide |K| for every maximal subgroup K of G, then G has no equal covering.

**Theorem 14.** Dihedral group D_{2n} has an equal covering if and only if n is even. When n is even, the covering is {langle r, langle r^2 s, langle r^2 rs}.

**Theorem 15.** Every non-cyclic finite p-group has an equal covering.

**Theorem 16.** If G has an equal covering, then G x H has an equal covering for any group H.

**Theorem 17.** Every non-cyclic finite nilpotent group has an equal covering.

**Theorem 18.** If |G| = p_1 p_2 ... p_k with p_i distinct primes (squarefree order), then G has no equal covering.

**Theorem 19.** If N is normal in G and G/N has an equal covering, then G has an equal covering. If G = H semidirect K and K has an equal covering, then G has an equal covering.

**New Conjecture (this project).** For any prime p >= 5, the alternating group A_p has no equal covering. The proof strategy uses Theorem 13 to show that any equal covering member must contain a Sylow p-subgroup, then targets either a contradiction with simplicity or an incompatibility with the maximal subgroup lattice.

---

## Lean 4 Formalization

The formalization lives in the `FormalizedTheorems/Lean/` directory. It is a self-contained Lean 4 project using toolchain version 4.27.0. Rather than importing Mathlib's Subgroup API directly, we built a small algebraic core so that the correspondence to Mathlib is explicit.

### What Is Formalized

**Fully proven (with tactic proofs):**

| Theorem | Statement | File |
|---------|-----------|------|
| Theorem 1 (forward) | G has a covering implies G is non-cyclic | Theorems.lean |
| Theorem 1 (reverse) | G non-cyclic implies G has a covering (with explicit construction) | Theorems.lean |
| Theorem 2 | No group is the union of two proper subgroups | Theorems.lean |
| Theorem 13 | Equal covering implies exp(G) divides order of every member | Theorems.lean |
| Corollary 1 | Exponent obstruction to equal coverings | Theorems.lean |
| Theorem 16 | Equal covering of G implies equal covering of G x H | Theorems.lean |
| Theorem 19 | Equal covering pulls back along surjective homomorphisms | Theorems.lean |

**Stated as axioms (correct type signatures, pending full proofs):**

| Theorem | Statement |
|---------|-----------|
| Theorem 15 | Every non-cyclic finite p-group has an equal covering |
| Theorem 17 | Every non-cyclic finite nilpotent group has an equal covering |

### Module Structure

**Group.lean** defines the core algebraic types: `Group` (with monoid axioms), `IsSubgroup` (closure under multiplication, inverses, and containing the identity), `Subgroup` (a subgroup as a structure bundling a predicate with a proof), and `IsProper` (existence of an element outside the subgroup).

**Covering.lean** defines `Covering` (a family of proper subgroups indexed by I that covers the group) and `EqualCovering` (a covering where all members have the same size). It also introduces an axiomatized `Size` type for subgroup cardinality.

**Finite.lean** axiomatizes `order_subgroup`, `exp`, `order_elem`, the `lagrange` lemma (element order divides subgroup order), `exp_multiple` (element order divides group exponent), and `exp_divides` (if every element order divides N, then the exponent divides N). It also defines `FiniteEqualCovering` using numeric order.

**Products.lean** defines `DirectProduct` (the Cartesian product of two groups with componentwise operations) and `ProdSubgroup` (lifting a subgroup of G to a subgroup of G x H by ignoring the second coordinate).

**Homomorphisms.lean** defines `Homomorphism` (a structure with a function and a proof it preserves multiplication), `Surjective`, and `PreimageSubgroup` (the pullback of a subgroup along a homomorphism).

**Theorems.lean** contains all theorem statements and proofs. Key design decisions include:
- Theorem 2 uses `cancel_lemma` and `cancel_lemma2` (axiomatized) for the cancellation argument.
- Theorem 13 composes `lagrange` and `exp_divides` with the equal size condition.
- Corollary 1 uses `Nat.dvd_trans` for transitivity of divisibility.
- Theorem 16 constructs `ProdSubgroup` for each covering member and verifies properness and coverage.
- Theorem 19 constructs `PreimageSubgroup` for each covering member and uses surjectivity to prove properness.

### Challenges Encountered

1. **Axiomatizing rather than importing.** The self-contained Group/Subgroup core means re-axiomatizing Lagrange's theorem and the exponent-order relationship instead of reusing Mathlib proofs.

2. **Order as a type versus a number.** The initial Size type was later replaced by numeric order_subgroup for the finite group results, requiring finiteness hypotheses to be threaded through every subsequent lemma.

3. **Case-heavy classical proofs.** Results like Scorza's theorem require careful use of Classical.byContradiction and explicit witness extraction since the underlying group is not assumed decidable.

4. **No quotient groups yet.** Theorem 19 required building PreimageSubgroup from scratch. The full Theorem 19(ii) via semidirect product is left for future work.

---

## GAP Computational Investigation

The GAP code classifies groups of a given order range by testing whether each group admits an equal covering. The algorithm is based on Theorem 13 (exp(G) must divide the order of every covering subgroup) to prune the search space.

### Algorithm Overview

1. **Cyclic check.** If G is cyclic, return false (Theorem 1).

2. **Candidate orders.** Compute all divisors d of |G| with d < |G| and exp(G) divides d. These are the only possible orders for equal covering subgroups.

3. **Subgroup bucketing.** Enumerate all subgroups of G once and bucket them by order.

4. **Coverage test.** For each candidate order d, test whether the subgroups of order d cover all elements of G using a bitset representation.

5. **Early exit.** Return true as soon as a valid covering is found.

### Optimization History

The code evolved through several stages:

**Stage 1: Original code** (`Original Code/author.g`). The baseline algorithm by Velasquez-Berroteran. Calls `AllSubgroups(G)` inside the candidate order loop, so subgroups are enumerated repeatedly.

**Stage 2: Subgroup bucketing** (`Normal Range Code/s.g`). Computes `AllSubgroups(G)` once and buckets subgroups by order. Adds a bitset-based coverage test with early exit.

**Stage 3: Incremental optimizations** (`Optimizations/` directory). Seven optimizations applied progressively:
- 1a: Candidate order pruning using Theorem 13 (note: the file 1a_prune.g has a bug with reversed modulo)
- 1b: Theorem-based shortcuts for known yes/no cases (nilpotent, dihedral, p-groups, squarefree, simple with exp = |G|/2)
- 2a: Avoid calling AllSubgroups repeatedly
- 2b: Hash map for element-to-index lookup (using record with String keys)
- 2c: Early exit in coverage test as soon as all elements are covered
- 2d: Caching results by group order and ID to avoid recomputation

**Stage 4: Final optimized code** (`Formulized Optimized code/t.g`). All seven optimizations combined into a single clean implementation with EC_ prefix naming convention. Includes demo examples and a main entry point.

### Results for Orders 61 to 120

The complete classification table is in the LaTeX document (Table in Section 5). Key observations:

- **Order 64:** All 267 non-cyclic groups (all 2-groups) admit equal coverings, consistent with Theorem 15.
- **Order 120:** Mixed results. Includes S_5, SL(2,5), and C_2 x A_5, all with no equal covering (simple/near-simple composition factors).
- **Squarefree orders** (66, 70, 102, 110, etc.): Uniformly no equal covering, consistent with Theorem 18.
- **No exceptions** to Theorems 13 through 19 were found in the extended range.
- **Every simple group encountered** has no equal covering, further supporting the central conjecture.

### Challenges

- Runtime grows sharply with |G| because subgroup enumeration and coverage checks become expensive for large groups.
- Extending the verification to simple groups in general is computationally infeasible here; Velasquez-Berroteran already verified the conjecture up to order 10,000.

---

## How to Use the GAP Code

1. Install GAP from https://www.gap-system.org/

2. Launch GAP.

3. To load and run the fully optimized code:
```
Read("GAPfiles/Formulized Optimized code/t.g");
EC_Main();
```

4. To scan a specific order range:
```
Read("GAPfiles/Formulized Optimized code/t.g");
EC_ScanRange(61, 120);
```

5. To test a specific group:
```
Read("GAPfiles/Formulized Optimized code/t.g");
EC_ShowCovering(SmallGroup(64, 1));
```

6. To test the dihedral group theorem:
```
Read("GAPfiles/Formulized Optimized code/t.g");
EC_TestDihedralTheorem();
```

### Important Note About 1a_prune.g

The file `GAPfiles/Optimizations/1a_prune.g` contains a bug. The candidate order check reads `d mod expG = 0` but should read `expG mod d = 0` (or equivalently `d mod expG = 0` is wrong because it checks whether d divides expG rather than whether expG divides d). The correct version appears in the final optimized code as `d mod expG = 0`. Actually, looking more carefully, the correct condition from Theorem 13 is that exp(G) must divide d, so the check should be `d mod expG = 0`. The original code had this correct, while 1a_prune.g accidentally swapped to `expG mod d = 0`. The final code `t.g` has the correct version.

---

## How to Build the Lean Project

1. Install Lean 4 (elan) from https://leanprover.github.io/lean4/doc/quickstart.html

2. Navigate to the Lean project directory:
```
cd FormalizedTheorems/Lean
```

3. Build the project:
```
lake build
```

The project uses Lean 4.27.0 (as specified in `lean-toolchain`) and does not depend on Mathlib. All algebraic infrastructure is self-contained.

---

## How to Compile the LaTeX Document

1. Install a LaTeX distribution (TeX Live or MiKTeX) with the required packages.

2. From the repository root:
```
pdflatex documentation.tex
bibtex documentation
pdflatex documentation.tex
pdflatex documentation.tex
```

Or use latexmk:
```
latexmk documentation.tex
```

Required packages include: amsmath, amsthm, amssymb, mathtools, enumitem, hyperref, cleveref, booktabs, longtable, listings, xcolor, titlesec, tocloft, setspace, cite, microtype, fontenc, inputenc.

To compile the presentation:
```
cd presentation
xelatex presentation.tex
```

The presentation uses XeLaTeX with the Latin Modern Sans, Poppins, and DejaVu Sans Mono fonts.

---

## Results Summary

The project produces the following outputs:

1. **A complete theoretical foundation** with full proofs of 14 theorems/lemmas/corollaries on group coverings, equal coverings, and related structures.

2. **A Lean 4 formalization** of 7 results (Theorems 1, 2, 13, 16, 19, Corollary 1, and Theorem 1 forward and reverse directions) with 2 additional results stated as axioms (Theorems 15, 17).

3. **A complete computational classification** of all groups of orders 61 through 120 with respect to equal coverings, totaling hundreds of groups tested.

4. **A Beamer presentation** summarizing the work with a custom Deep Reef color theme and TikZ diagrams.

5. **A new conjecture** (Conjecture 6.4 in the document) that A_p has no equal covering for prime p >= 5, with a proof strategy outlined.

---

## Future Directions

1. Complete the Lean proofs of Theorems 15 (non-cyclic p-groups) and 17 (non-cyclic nilpotent groups).

2. Push Conjecture 6.4 to a full proof for alternating groups A_p with p prime and p >= 5.

3. Extend the GAP classification beyond order 120.

4. Formalize Scorza's theorem and the Haber-Rosenfeld theorem in Lean 4.

5. Formalize quotient groups and the Correspondence Theorem in the Lean framework to enable full treatment of Theorem 19(ii).

---

## References

- Scorza, G. (1926). Groups can be expressed as a union of three proper subgroups. Bollettino Unione Matematica Italiana.
- Haber, S. and Rosenfeld, A. (1959). Groups as unions of proper subgroups. The American Mathematical Monthly, 66:491-494.
- Cohn, J.H.E. (1994). On n-Sum Groups.
- Velasquez-Berroteran, A. (2022). Equal Coverings of Finite Groups. arXiv:2206.14843.
- Foguel, T.S. and Ragland, M.F. (2008). Groups with a Finite Covering by Isomorphic Abelian Subgroups.
- Lean 4. https://lean-lang.org/
- GAP -- Groups, Algorithms, Programming. https://www.gap-system.org/
