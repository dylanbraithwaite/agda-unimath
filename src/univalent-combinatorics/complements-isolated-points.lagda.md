---
title: Complements of isolated points of finite types
---

```agda
{-# OPTIONS --without-K --exact-split #-}

module univalent-combinatorics.complements-isolated-points where

open import elementary-number-theory.natural-numbers using (ℕ; zero-ℕ; succ-ℕ)

open import foundation.dependent-pair-types using (Σ; pair; pr1; pr2; _,_)
open import foundation.equivalences using (inv-equiv; _∘e_; _≃_; map-equiv)
open import foundation.equivalences-maybe using (equiv-equiv-Maybe)
open import foundation.functions using (_∘_; id)
open import foundation.homotopies using (_~_)
open import foundation.identity-types using (Id; refl)
open import foundation.isolated-points using
  ( isolated-point; complement-isolated-point;
    equiv-maybe-structure-isolated-point; equiv-complement-isolated-point)
open import foundation.maybe using (Maybe)
open import foundation.mere-equivalences using (mere-equiv-Prop)
open import foundation.propositional-truncations using
  ( apply-universal-property-trunc-Prop; unit-trunc-Prop)
open import foundation.universe-levels using (Level; UU; lzero)

open import univalent-combinatorics.equality-finite-types using
  ( has-decidable-equality-has-cardinality)
open import univalent-combinatorics.finite-types using
  ( UU-Fin; type-UU-Fin; equiv-UU-Fin; has-cardinality; has-cardinality-Prop;
    has-cardinality-type-UU-Fin)
open import univalent-combinatorics.standard-finite-types using (Fin)
```

## Idea

For any point `x` in a finite type `X` of cardinality `n+1`, we can construct its complement, which is a type of cardinality `n`.

## Definition

### The complement of a point in a k-element type of arbitrary universe level

```agda
isolated-point-UU-Fin :
  {l : Level} (k : ℕ) (X : UU-Fin l (succ-ℕ k)) →
  type-UU-Fin (succ-ℕ k) X →
  isolated-point (type-UU-Fin (succ-ℕ k) X)
pr1 (isolated-point-UU-Fin k X x) = x
pr2 (isolated-point-UU-Fin k X x) =
  has-decidable-equality-has-cardinality
    ( succ-ℕ k)
    ( has-cardinality-type-UU-Fin (succ-ℕ k) X)
    ( x)

type-complement-point-UU-Fin :
  {l1 : Level} (k : ℕ) →
  Σ (UU-Fin l1 (succ-ℕ k)) (type-UU-Fin (succ-ℕ k)) → UU l1
type-complement-point-UU-Fin k (X , x) =
  complement-isolated-point
    ( type-UU-Fin (succ-ℕ k) X)
    ( isolated-point-UU-Fin k X x)

equiv-maybe-structure-point-UU-Fin :
  {l : Level} (k : ℕ) (X : UU-Fin l (succ-ℕ k)) →
  (x : type-UU-Fin (succ-ℕ k) X) →
  Maybe (type-complement-point-UU-Fin k (pair X x)) ≃
  type-UU-Fin (succ-ℕ k) X
equiv-maybe-structure-point-UU-Fin k X x =
   equiv-maybe-structure-isolated-point
     ( type-UU-Fin (succ-ℕ k) X)
     ( isolated-point-UU-Fin k X x)

has-cardinality-type-complement-point-UU-Fin :
  {l1 : Level} (k : ℕ)
  (X : Σ (UU-Fin l1 (succ-ℕ k)) (type-UU-Fin (succ-ℕ k))) →
  has-cardinality k (type-complement-point-UU-Fin k X)
has-cardinality-type-complement-point-UU-Fin k (pair (pair X H) x) =
  apply-universal-property-trunc-Prop H
    ( has-cardinality-Prop k
      ( type-complement-point-UU-Fin k (pair (pair X H) x)))
    ( λ e →
      unit-trunc-Prop
        ( equiv-equiv-Maybe
          ( ( inv-equiv
              ( equiv-maybe-structure-point-UU-Fin k (pair X H) x)) ∘e
            ( e))))
  
complement-point-UU-Fin :
  {l1 : Level} (k : ℕ) →
  Σ (UU-Fin l1 (succ-ℕ k)) (type-UU-Fin (succ-ℕ k)) →
  UU-Fin l1 k
pr1 (complement-point-UU-Fin k T) =
  type-complement-point-UU-Fin k T
pr2 (complement-point-UU-Fin k T) =
  has-cardinality-type-complement-point-UU-Fin k T

inclusion-complement-point-UU-Fin :
  {l1 : Level} (k : ℕ)
  (X : Σ (UU-Fin l1 (succ-ℕ k)) (type-UU-Fin (succ-ℕ k))) →
  type-complement-point-UU-Fin k X → type-UU-Fin (succ-ℕ k) (pr1 X)
inclusion-complement-point-UU-Fin k X x = pr1 x
```

### The action of equivalences on complements of isolated points

```agda
equiv-complement-point-UU-Fin :
  {l1 : Level} (k : ℕ)
  (X Y : Σ (UU-Fin l1 (succ-ℕ k)) (type-UU-Fin (succ-ℕ k))) →
  (e : equiv-UU-Fin (succ-ℕ k) (pr1 X) (pr1 Y))
  (p : Id (map-equiv e (pr2 X)) (pr2 Y)) →
  equiv-UU-Fin k
    ( complement-point-UU-Fin k X)
    ( complement-point-UU-Fin k Y)
equiv-complement-point-UU-Fin
  k S T e p =
  equiv-complement-isolated-point e
    ( pair x (λ x' → has-decidable-equality-has-cardinality (succ-ℕ k) H x x'))
    ( pair y (λ y' → has-decidable-equality-has-cardinality (succ-ℕ k) K y y'))
    ( p)
  where
  H = pr2 (pr1 S)
  x = pr2 S
  K = pr2 (pr1 T)
  y = pr2 T
```

## Properties

### The map from a pointed (k+1)-element type to the complement of the point is an equivalence
