---
title: Equivalence relations
---

```agda
{-# OPTIONS --without-K --exact-split #-}

module foundation-core.equivalence-relations where

open import foundation.binary-relations using
  ( Rel-Prop; is-reflexive-Rel-Prop; is-symmetric-Rel-Prop;
    is-transitive-Rel-Prop; type-Rel-Prop; is-prop-type-Rel-Prop;
    is-prop-is-reflexive-Rel-Prop; is-prop-is-symmetric-Rel-Prop;
    is-prop-is-transitive-Rel-Prop)

open import foundation-core.cartesian-product-types using (_×_)
open import foundation-core.dependent-pair-types using (Σ; pair; pr1; pr2)
open import foundation-core.equivalences using (_≃_)
open import foundation-core.propositions using
  ( is-prop; equiv-prop; is-prop-prod; UU-Prop)
open import foundation-core.universe-levels using (Level; UU; _⊔_; lsuc)
```

## Idea

An equivalence relation is a relation valued in propositions, which is reflexive,symmetric, and transitive.

## Definition

```agda
is-equivalence-relation :
  {l1 l2 : Level} {A : UU l1} (R : Rel-Prop l2 A) → UU (l1 ⊔ l2)
is-equivalence-relation R =
  is-reflexive-Rel-Prop R ×
    ( is-symmetric-Rel-Prop R × is-transitive-Rel-Prop R)

Eq-Rel :
  (l : Level) {l1 : Level} (A : UU l1) → UU ((lsuc l) ⊔ l1)
Eq-Rel l A = Σ (Rel-Prop l A) is-equivalence-relation

prop-Eq-Rel :
  {l1 l2 : Level} {A : UU l1} → Eq-Rel l2 A → Rel-Prop l2 A
prop-Eq-Rel = pr1

sim-Eq-Rel :
  {l1 l2 : Level} {A : UU l1} → Eq-Rel l2 A → A → A → UU l2
sim-Eq-Rel R = type-Rel-Prop (prop-Eq-Rel R)

abstract
  is-prop-sim-Eq-Rel :
    {l1 l2 : Level} {A : UU l1} (R : Eq-Rel l2 A) (x y : A) →
    is-prop (sim-Eq-Rel R x y)
  is-prop-sim-Eq-Rel R = is-prop-type-Rel-Prop (prop-Eq-Rel R)

is-prop-is-equivalence-relation :
  {l1 l2 : Level} {A : UU l1} (R : Rel-Prop l2 A) →
  is-prop (is-equivalence-relation R)
is-prop-is-equivalence-relation R =
  is-prop-prod
    ( is-prop-is-reflexive-Rel-Prop R)
    ( is-prop-prod
      ( is-prop-is-symmetric-Rel-Prop R)
      ( is-prop-is-transitive-Rel-Prop R))

is-equivalence-relation-Prop :
  {l1 l2 : Level} {A : UU l1} → Rel-Prop l2 A → UU-Prop (l1 ⊔ l2)
pr1 (is-equivalence-relation-Prop R) = is-equivalence-relation R
pr2 (is-equivalence-relation-Prop R) = is-prop-is-equivalence-relation R

is-equivalence-relation-prop-Eq-Rel :
  {l1 l2 : Level} {A : UU l1} (R : Eq-Rel l2 A) →
  is-equivalence-relation (prop-Eq-Rel R)
is-equivalence-relation-prop-Eq-Rel R = pr2 R

refl-Eq-Rel :
  {l1 l2 : Level} {A : UU l1} (R : Eq-Rel l2 A) →
  is-reflexive-Rel-Prop (prop-Eq-Rel R)
refl-Eq-Rel R = pr1 (is-equivalence-relation-prop-Eq-Rel R)

symm-Eq-Rel :
  {l1 l2 : Level} {A : UU l1} (R : Eq-Rel l2 A) →
  is-symmetric-Rel-Prop (prop-Eq-Rel R)
symm-Eq-Rel R = pr1 (pr2 (is-equivalence-relation-prop-Eq-Rel R))

equiv-symm-Eq-Rel :
  {l1 l2 : Level} {A : UU l1} (R : Eq-Rel l2 A) (x y : A) →
  sim-Eq-Rel R x y ≃ sim-Eq-Rel R y x
equiv-symm-Eq-Rel R x y =
  equiv-prop
    ( is-prop-sim-Eq-Rel R x y)
    ( is-prop-sim-Eq-Rel R y x)
    ( symm-Eq-Rel R)
    ( symm-Eq-Rel R)

trans-Eq-Rel :
  {l1 l2 : Level} {A : UU l1} (R : Eq-Rel l2 A) →
  is-transitive-Rel-Prop (prop-Eq-Rel R)
trans-Eq-Rel R = pr2 (pr2 (is-equivalence-relation-prop-Eq-Rel R))
```