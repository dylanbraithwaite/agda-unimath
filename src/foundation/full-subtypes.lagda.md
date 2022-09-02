---
title: Full subtypes of types
---

```agda
module foundation.full-subtypes where

open import foundation.contractible-maps
open import foundation.dependent-pair-types
open import foundation.equivalences
open import foundation.identity-types
open import foundation.propositions
open import foundation.subtypes
open import foundation.type-arithmetic-dependent-pair-types
open import foundation.unit-type
open import foundation.universe-levels
```

## Idea

The full subtype of a type contains every element. We define a full subtype at each universe level.

## Definition

### Full subtypes

```agda
is-full-subtype : {l1 l2 : Level} {A : UU l1} → subtype l2 A → UU (l1 ⊔ l2)
is-full-subtype {A = A} P = (x : A) → is-in-subtype P x
```

### The full subtype at a universe level

```agda
full-subtype : {l1 : Level} (l2 : Level) (A : UU l1) → subtype l2 A
full-subtype l2 A x = raise-unit-Prop l2

type-full-subtype : {l1 : Level} (l2 : Level) (A : UU l1) → UU (l1 ⊔ l2)
type-full-subtype l2 A = type-subtype (full-subtype l2 A)

module _
  {l1 l2 : Level} {A : UU l1}
  where

  is-in-full-subtype : (x : A) → is-in-subtype (full-subtype l2 A) x
  is-in-full-subtype x = raise-star

  inclusion-full-subtype : type-full-subtype l2 A → A
  inclusion-full-subtype = inclusion-subtype (full-subtype l2 A)

  is-equiv-inclusion-full-subtype : is-equiv inclusion-full-subtype
  is-equiv-inclusion-full-subtype =
    is-equiv-pr1-is-contr (λ a → is-contr-raise-unit)
```

## Properties

### A subtype is full if and only if the inclusion is an equivalence

```agda
module _
  {l1 l2 : Level} {A : UU l1} (P : subtype l2 A)
  where
  
  is-equiv-inclusion-is-full-subtype :
    is-full-subtype P → is-equiv (inclusion-subtype P)
  is-equiv-inclusion-is-full-subtype H =
    is-equiv-pr1-is-contr
      ( λ x → is-proof-irrelevant-is-prop (is-prop-is-in-subtype P x) (H x))

  equiv-inclusion-is-full-subtype :
    is-full-subtype P → type-subtype P ≃ A
  pr1 (equiv-inclusion-is-full-subtype H) = inclusion-subtype P
  pr2 (equiv-inclusion-is-full-subtype H) = is-equiv-inclusion-is-full-subtype H

  is-full-is-equiv-inclusion-subtype :
    is-equiv (inclusion-subtype P) → is-full-subtype P
  is-full-is-equiv-inclusion-subtype H x =
    tr
      ( is-in-subtype P)
      ( issec-map-inv-is-equiv H x)
      ( pr2 (map-inv-is-equiv H x))
```