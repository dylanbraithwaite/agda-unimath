---
title: Choice of representatives for an equivalence relation
---

```agda
{-# OPTIONS --without-K --exact-split #-}

module foundation.choice-of-representatives-equivalence-relation where

open import foundation.cartesian-product-types using (_×_)
open import foundation.contractible-types using
  ( is-contr; center; is-contr-equiv)
open import foundation.dependent-pair-types using (Σ; pair; pr1; pr2)
open import foundation.embeddings using (is-emb)
open import foundation.equivalence-classes using
  ( equivalence-class; class; eq-class-equivalence-class;
    apply-effectiveness-class';
    is-effective-class)
open import foundation.equivalences using (_∘e_; is-equiv; _≃_)
open import foundation.fibers-of-maps using (fib)
open import foundation.functoriality-dependent-pair-types using (equiv-tot)
open import foundation.fundamental-theorem-of-identity-types using
  ( fundamental-theorem-id)
open import foundation.identity-types using (_∙_; ap; refl)
open import foundation.logical-equivalences using (backward-implication)
open import foundation.propositional-truncations using
  ( apply-universal-property-trunc-Prop; trunc-Prop; unit-trunc-Prop;
    is-prop-type-trunc-Prop)
open import foundation.propositions using (eq-is-prop)
open import foundation.surjective-maps using
  ( is-surjective; is-equiv-is-emb-is-surjective)
open import foundation.type-arithmetic-dependent-pair-types using (assoc-Σ)
open import foundation.universe-levels using (Level; UU; _⊔_)

open import foundation-core.equivalence-relations using
  ( Eq-Rel; sim-Eq-Rel; symm-Eq-Rel; prop-Eq-Rel; refl-Eq-Rel)
```

## Idea

If we can construct a choice of representatives for each equivalence class, then we can construct the set quotient as a retract of the original type.

```agda
module _
  {l1 l2 l3 : Level} {A : UU l1} (R : Eq-Rel l2 A)
  where
    
  is-choice-of-representatives :
    (A → UU l3) → UU (l1 ⊔ l2 ⊔ l3)
  is-choice-of-representatives P =
    (a : A) → is-contr (Σ A (λ x → P x × sim-Eq-Rel R a x))
  
  representatives :
    {P : A → UU l3} → is-choice-of-representatives P → UU (l1 ⊔ l3)
  representatives {P} H = Σ A P
  
  class-representatives :
    {P : A → UU l3} (H : is-choice-of-representatives P) →
    representatives H → equivalence-class R
  class-representatives H a =
    class R (pr1 a)

  abstract
    is-surjective-class-representatives :
      {P : A → UU l3} (H : is-choice-of-representatives P) →
      is-surjective (class-representatives H)
    is-surjective-class-representatives H (pair Q K) =
      apply-universal-property-trunc-Prop K
        ( trunc-Prop
          ( fib (class-representatives H) (pair Q K)))
        ( λ (pair a φ) →
          unit-trunc-Prop
            ( pair
              ( pair (pr1 (center (H a))) (pr1 (pr2 (center (H a)))))
              ( ( apply-effectiveness-class' R
                  ( symm-Eq-Rel R (pr2 (pr2 (center (H a)))))) ∙
                ( eq-class-equivalence-class R
                  ( pair Q K)
                  ( backward-implication
                    ( φ a)
                    ( refl-Eq-Rel R))))))

  abstract
    is-emb-class-representatives :
      {P : A → UU l3} (H : is-choice-of-representatives P) →
      is-emb (class-representatives H)
    is-emb-class-representatives {P} H (pair a p) =
      fundamental-theorem-id
        ( is-contr-equiv
          ( Σ A (λ x → P x × sim-Eq-Rel R a x))
          ( ( assoc-Σ A P (λ z → sim-Eq-Rel R a (pr1 z))) ∘e
            ( equiv-tot
              ( λ t →
                is-effective-class R a (pr1 t))))
          ( H a))
        ( λ y →
          ap (class-representatives H) {pair a p} {y})

  abstract
    is-equiv-class-representatives :
      {P : A → UU l3} (H : is-choice-of-representatives P) →
      is-equiv (class-representatives H)
    is-equiv-class-representatives H =
      is-equiv-is-emb-is-surjective
        ( is-surjective-class-representatives H)
        ( is-emb-class-representatives H)

  equiv-equivalence-class-representatives :
    {P : A → UU l3} (H : is-choice-of-representatives P) →
    representatives H ≃ equivalence-class R
  equiv-equivalence-class-representatives H =
    pair ( class-representatives H)
         ( is-equiv-class-representatives H)
```
