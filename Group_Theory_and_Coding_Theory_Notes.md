# Group Theory & Coding Theory — Complete Study Notes

---

# UNIT 1: GROUP THEORY

---

## 1. Algebraic Structures

### 1.1 Binary Operation

A **binary operation** on a non-empty set A is a function defined from A × A to A. That is, a binary operation on a non-empty set A assigns to every ordered pair of elements of A a unique element of A.

- A binary operation is denoted by `*`
- The element of A assigned to the pair (a, b) is denoted by `a * b`
- If `*` is a binary operation on A, we say A is **closed under `*`**
- A non-empty set on which a binary operation is defined is called a **groupoid** under that operation

**Examples:**
- On the set Z of all integers, the usual addition, subtraction, and multiplication are binary operations because for any a, b ∈ Z: a+b ∈ Z, a−b ∈ Z, a×b ∈ Z
- On the set Z⁺ of non-negative integers, addition and multiplication are binary operations, but subtraction is **not** (since a−b need not belong to Z⁺)
- On the power set P(S) of a non-empty set S, the set union and set intersection are binary operations because if A, B ∈ P(S), then A∪B ∈ P(S) and A∩B ∈ P(S)

### 1.2 Properties of Binary Operations

Let `*` be a binary operation on a set A. Then `*` is said to be:

**(i) Commutative** if:
```
a * b = b * a  for all a, b ∈ A
```

**(ii) Associative** if:
```
a * (b * c) = (a * b) * c  for all a, b, c ∈ A
```

**(iii) Idempotent** if:
```
a * a = a  for all a ∈ A
```

**Examples:**
- On Z, addition and multiplication are commutative and associative. Subtraction is neither.
- On a power set, union and intersection are commutative, associative, and idempotent.
- On R, if `*` is defined by a * b = a×|b|, then b * a = b×|a|, so b * a ≠ a * b in general — not commutative. But it is associative.

### 1.3 Algebraic System / Structure

An **algebraic system** is a set A with one or more binary (closed) operations defined on it.

**Examples:**
- (N, +) — Natural numbers with addition
- (Z, +, −) — Integers with addition and subtraction

---

## 2. Semigroup

**Definition:** A non-empty set which is closed under an associative binary operation is called a **semigroup** under that operation.

Formally, (S, *) is a semigroup if:
1. **Closure:** For all a, b ∈ S, a * b ∈ S
2. **Associativity:** For all a, b, c ∈ S, (a * b) * c = a * (b * c)

If the binary operation is also **commutative**, then (S, *) is called an **Abelian semigroup** (or commutative semigroup).

**Examples:**
- (Z, +): Semigroup and also Abelian semigroup (since + is associative and commutative)
- (Z, ×): Also a commutative semigroup
- (Z, −): **Not** a semigroup (subtraction is not associative)
- (P(S), ∩): Commutative semigroup
- (P(S), ∪): Commutative semigroup

---

## 3. Monoid

**Definition:** If S be a semigroup under a binary operation `*`, then S is said to be a **monoid** if there exists an element e ∈ S such that:
```
e * a = a * e = a  for all a ∈ S
```
This element e is called the **identity element** in S.

**Examples:**
- (Z, +) is a monoid with identity element **0**
- (Z, ×) is a monoid with identity element **1**
- (P(S), ∪) is a monoid with **∅** (empty set) as identity
- (P(S), ∩) is a monoid with **S** as identity

### Summary of Structure Hierarchy:

| Properties Satisfied | Structure |
|---|---|
| Closure + Associativity | Semigroup |
| Closure + Associativity + Identity | Monoid |
| Closure + Associativity + Identity + Commutativity | Abelian Monoid |
| Closure + Associativity + Identity + Inverse | Group |
| Closure + Associativity + Identity + Inverse + Commutativity | Abelian Group |

---

## 4. Group — Definition

**Definition:** If G is a non-empty set and `*` is a binary operation on G, then (G, *) is said to be a **group** if the following conditions are satisfied:

1. **Closure Law:** For all a, b ∈ G, a * b ∈ G
2. **Associative Law:** For all a, b, c ∈ G, a * (b * c) = (a * b) * c
3. **Identity Law:** There exists e ∈ G such that a * e = e * a = a for all a ∈ G (e is the **identity element**)
4. **Inverse Law:** For each a ∈ G, there exists a⁻¹ ∈ G such that a * a⁻¹ = a⁻¹ * a = e

If in addition:

5. **Commutative Law:** For all a, b ∈ G, a * b = b * a

then G is called a **Commutative group** or **Abelian group**.

### 4.1 Standard Number Sets

| Symbol | Meaning |
|---|---|
| N | {1, 2, 3, ...} — Natural numbers |
| Z | {..., −∞, ..., 0, ..., ∞} — All integers |
| Q | Set of all rational numbers |
| R | Set of all real numbers |
| C | Set of all complex numbers |
| Q* | Q without zero |
| Zₙ⁺ | Additive modulo n |

---

## 5. Examples of Groups

**Example 1:** Show that the set of all integers (Z, +) is a group.

**Solution:**
- Let a, b, c ∈ Z
- **Closure:** a + b ∈ Z for all a, b ∈ Z ✓
- **Associativity:** a + (b + c) = (a + b) + c for all a, b, c ∈ Z ✓
- **Identity:** 0 ∈ Z and a + 0 = 0 + a = a, so **0** is the identity element ✓
- **Inverse:** For a = 3: 3 + e = 3 ⇒ e = 0; a + a⁻¹ = e ⇒ 3 + 3⁻¹ = 0 ⇒ 3⁻¹ = −3; −a ∈ Z such that a + (−a) = 0 ✓
- Since it also satisfies commutativity (a + b = b + a), **(Z, +) is an Abelian group**

**Example 2:** (Z, −) is **not** a group because subtraction is not associative.

**Example 3:** (Z, ×) is **not** a group because there is no element a⁻¹ ∈ Z such that a × a⁻¹ = 1 (since the identity for multiplication is 1, but 1/a ∉ Z in general).

**Example 4:** (Q*, ×) — The set of all non-zero rational numbers (real numbers) is an **Abelian group** under multiplication with 1 as the identity and 1/a as the inverse of a.

**Example 5:** Let A = {0, 1} with operation defined by:

| * | 0 | 1 |
|---|---|---|
| 0 | 0 | 1 |
| 1 | 1 | 0 |

This is an abelian group with 0 as identity and each element is its own inverse.

**Example 6:** Let A = {1, −1, i, −i} — the set of all fourth roots of unity. Under usual multiplication:

| × | 1 | −1 | i | −i |
|---|---|---|---|---|
| 1 | 1 | −1 | i | −i |
| −1 | −1 | 1 | −i | i |
| i | i | −i | −1 | 1 |
| −i | −i | i | 1 | −1 |

- A is closed under ×
- Complex numbers are associative and commutative under multiplication
- 1 is the identity element
- Inverses: 1⁻¹=1, (−1)⁻¹=−1, i⁻¹=−i, (−i)⁻¹=i
- Therefore **(A, ×) is an abelian group**

**Example 7:** For n ∈ Z⁺, n > 1, **(Zₙ, +)** is an abelian group (addition modulo n).

For n = 6:

| + | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| 0 | 0 | 1 | 2 | 3 | 4 | 5 |
| 1 | 1 | 2 | 3 | 4 | 5 | 0 |
| 2 | 2 | 3 | 4 | 5 | 0 | 1 |
| 3 | 3 | 4 | 5 | 0 | 1 | 2 |
| 4 | 4 | 5 | 0 | 1 | 2 | 3 |
| 5 | 5 | 0 | 1 | 2 | 3 | 4 |

Inverses: 0→0, 1→5, 2→4, 3→3, 4→2, 5→1.

---

## 6. Finite Groups and Order

**Definition:** For every group G, the number of elements in G is called the **order of G**, denoted by |G|. When the number of elements is not finite, we say G has **infinite order**.

---

## 7. Elementary Properties / Theorems of Groups

### Theorem 1: Identity element is unique

**Statement:** In a group G, the identity element is unique.

**Proof:**
- Suppose there are two identities e₁ and e₂ in G
- e₁ ∈ G, e₂ ∈ G
- Since e₁ is an identity element and e₂ ∈ G:
  `e₁ * e₂ = e₂ * e₁ = e₂` ...(1)
- Since e₂ is an identity element and e₁ ∈ G:
  `e₂ * e₁ = e₁ * e₂ = e₁` ...(2)
- From (1) and (2): e₁ = e₂ ∎

### Theorem 2: Inverse of each element is unique

**Statement:** In a group G, the inverse of each element is unique.

**Proof:**
- Suppose a⁻¹ = b and a⁻¹ = c, i.e., a, b, c ∈ G
- a * b = b * a = e ...(1)
- a * c = c * a = e ...(2)
- From (1): (b * a) * c = b * (a * c) [by Associativity]
- e * c = b * e
- c = b
- ∴ Inverse of 'a' is unique ∎

### Theorem 3: Left Cancellation Property

**Statement:** In G, if a, b, c ∈ G and ab = ac, then b = c.

**Proof:**
- Suppose ab = ac
- Since a ∈ G, we know a⁻¹ ∈ G
- Then a⁻¹(ab) = a⁻¹(ac)
- (a⁻¹a)b = (a⁻¹a)c [by associativity]
- eb = ec [since a⁻¹a = e]
- b = c [since e is identity] ∎

### Theorem 4: Right Cancellation Property

**Statement:** In G, if a, b, c ∈ G and ba = ca, then b = c.

**Proof:**
- Suppose ba = ca
- Since a ∈ G, we have a⁻¹ ∈ G
- Then (ba)a⁻¹ = (ca)a⁻¹
- b(aa⁻¹) = c(aa⁻¹) [by associativity]
- be = ce [since aa⁻¹ = e]
- b = c [by identity] ∎

### Additional Properties (Proved in Exercises)

**Property (a):** (a⁻¹)⁻¹ = a

**Proof:**
- Let a = g⁻¹, so (a⁻¹)⁻¹ = b = g⁻¹
- Let's take a ∈ G: a * a⁻¹ = e
- Since a⁻¹ ∈ G, a⁻¹ * (a⁻¹)⁻¹ = e
- So a * e = a⁻¹ * (a⁻¹)⁻¹
- ⇒ a = (a⁻¹)⁻¹ ∎

**Property (b):** (ab)⁻¹ = b⁻¹a⁻¹

**Proof:**
- a, b ∈ G; let's take (ab)(b⁻¹a⁻¹) = e
- (ab)(b⁻¹a⁻¹) = a(bb⁻¹)a⁻¹ = a·e·a⁻¹ = aa⁻¹ = e ✓
- This means b⁻¹a⁻¹ = (ab)⁻¹ ∎

**Note:** In an Abelian group G, (ab)⁻¹ = b⁻¹a⁻¹ = a⁻¹b⁻¹

### Problem Example 1:

Let G be the set of all non-zero real numbers and let a * b = ½ab. Show (G, *) is an abelian group.

**Solution:**
- **Closure:** For any a, b ∈ G, ½(ab) is also a non-zero real number. So a * b ∈ G ✓
- **Associativity:** a * (b * c) = a * (½bc) = ½·a·(½bc) = abc/4; (a * b) * c = (½ab) * c = ½·(½ab)·c = abc/4 ✓
- **Identity:** a * 2 = 2 * a = ½(2a) = a ⇒ **2 is the identity** and 2 ∈ G ✓
- **Inverse:** For a ∈ G, choose a' = 4/a, then a * a' = ½(a · 4/a) = 2 = identity ✓
- **Commutativity:** a * b = ½ab = ½ba = b * a ✓
- ∴ (G, *) is an abelian group ∎

### Problem Example 2:

Let G be the set of real numbers not equal to −1. Let a * b = a + b + ab. Show (G, *) is an abelian group.

**Solution:**
- When a ≠ −1 and b ≠ −1, we want a + b + ab ≠ −1. Since a + b + ab = (1+a)(1+b)−1 ≠ −1 when a ≠ −1 and b ≠ −1. So G is closed ✓
- **Associativity:** a*(b*c) = a*(b+c+bc) = a + (b+c+bc) + a(b+c+bc) = a+b+c+bc+ab+ac+abc; (a*b)*c = (a+b+ab)*c = a+b+ab+c+(a+b+ab)c = a+b+c+ab+ac+bc+abc ✓
- **Identity:** a * 0 = a + 0 + 0 = a ⇒ **0 is the identity** ✓
- **Inverse:** a' = −a/(1+a) (defined since a ≠ −1); a * a' = a + (−a/(1+a)) + a·(−a/(1+a)) = a(1+a)/(1+a) − a/(1+a) − a²/(1+a) = 0 ✓
- **Commutativity:** a * b = a+b+ab = b+a+ba = b * a ✓
- ∴ (G, *) is an abelian group ∎

---

## 8. Abelian Group

An Abelian group (also called a **commutative group**) is a group G in which the operation also satisfies commutativity:
```
a * b = b * a  for all a, b ∈ G
```

**Properties of Abelian Groups:**
- If G is abelian and f: G → H is an onto homomorphism, then H is also abelian
- The Cartesian product of two abelian groups is abelian

---

## 9. Subgroup

**Definition:** A non-empty subset H of group G is called a **subgroup** of G if:
1. H is stable (closed) for composition defined in G
2. H itself is a group for the composition induced by that of G

Equivalently, H ≤ G means H is a subgroup of G.

**Notes:**
- For any group G, e ∈ G, so {e} ⊆ G. Also {e} is a group under the operation in G. So {e} is a subgroup of G.
- For any group G, G ⊆ G. Since G is a group under its operation, G is a subgroup of itself.
- These two are called **trivial** (or **improper**) subgroups. All others are **nontrivial** (or **proper**) subgroups.

**Example of subgroup chain:** (Z, +) ⊂ (Q, +) ⊂ (R, +)

**Example 1:** Under usual addition, the set of all even integers is a subgroup of all integers.

**Example 2:** Under usual multiplication, the set of all non-zero rational numbers is a subgroup of the group of all non-zero real numbers.

### 9.1 Theorem on Subgroups

**Theorem 1:** If H is a non-empty subset of group G, then H is a subgroup of G if and only if:
1. For all a, b ∈ H, ab ∈ H
2. For all a ∈ H, a⁻¹ ∈ H

(Assuming G with operation denoted by multiplication)

**Proof (⇒):**
- Let H be a subgroup of G. By definition, H is closed, so for a, b ∈ H, ab ∈ H. ✓
- Since H is a group, for a ∈ H, a⁻¹ ∈ H. ✓

**Proof (⇐):**
- Let H ⊆ G, H ≠ ∅ with the two conditions satisfied.
- For all a, b, c ∈ H, (ab)c = a(bc) in G, so associativity holds in H.
- As H ≠ ∅, let a ∈ H. By condition (2), a⁻¹ ∈ H. By condition (1), a·a⁻¹ = e ∈ H, so H contains the identity.
- ∴ H is a group, hence a subgroup of G ∎

**Theorem 2 (Compact form):** If G is a group and ∅ ≠ H ⊆ G with H finite, then H is a subgroup of G iff H is closed under the binary operation of G.

**Proof:**
- If H is a subgroup of G, then H is closed.
- Conversely, let H be a finite nonempty set that is closed. If a ∈ H, then aH = {ah | h ∈ H} ⊆ H. By cancellation property, ah₁ = ah₂ ⇒ h₁ = h₂, so |aH| = |H|, thus aH = H.
- As a ∈ H, there exists b ∈ H with ab = a = a·e, so e = b and H contains the identity.
- Since e ∈ H = aH, there is an element c ∈ H such that ac = e. Then (ac)² = (ac)(ca) = e, so ca = e and c = a⁻¹ ∈ H.
- ∴ H is a subgroup ∎

---

## 10. Homomorphism

**Definition:** φ: G → G' is a **group homomorphism** if:
```
φ(xy) = φ(x)φ(y)  for all x, y ∈ G
```

This means the map preserves the group operation.

**Diagram:**
```
   G  --φ-->  G'
(x, y)     (φ(x), φ(y))
```
where φ(x·y) = φ(x) * φ(y)

### 10.1 Properties of Homomorphisms

**Theorem:** Let (G, o) and (H, *) be groups with respective identities e_G and e_H. If f: G → H is a homomorphism, then:

**(a) f(e_G) = e_H** — Identity maps to identity

**Proof:**
- f is a homomorphism, so f(a o b) = f(a) * f(b)
- Since e_G o e_G = e_G: f(e_G o e_G) = f(e_G) * f(e_G)
- f(e_G) = f(e_G) * f(e_G)
- For any g ∈ G, g o e_G = g: f(g o e_G) = f(g) ← Homomorphism
- f(g) * f(e_G) = f(g) ...(1)
- Since h * e_H = h for h ∈ H, and (1) resembles this, f(e_G) acts as identity, so f(e_G) = e_H ∎

**(b) f(S) is a subgroup of H for each subgroup S of G**

**Proof:**
- Let S be a subgroup of G, so S ≠ ∅; ∴ f(S) must map to some element.
- Take x, y ∈ f(S), so x = f(a), y = f(b) for some a, b ∈ S
- Since S is a subgroup of G: a o b⁻¹ ∈ S
- f(a o b⁻¹) = f(a) * f(b⁻¹) [because f is homomorphism]
- = f(a) * [f(b)]⁻¹
- = x * y⁻¹ ∈ f(S)
- Since f(S) is closed under the operation * and has inverses, f(S) is a subgroup of H ∎

**(c) f(a⁻¹) = [f(a)]⁻¹ for all a ∈ G**

**Proof:**
- For a ∈ G: a o a⁻¹ = e_G
- f(a o a⁻¹) = f(e_G) = e_H [by theorem (a)]
- f(a) * f(a⁻¹) = e_H [by homomorphism]
- f(a) * f(a⁻¹) = e_H ⇒ By definition of inverse, f(a⁻¹) = [f(a)]⁻¹ ∎

**(d) f(aⁿ) = [f(a)]ⁿ for all a ∈ G and n ∈ Z**

**Proof (for n ≥ 0):**
- aⁿ = a o a o a o ... o a (n times)
- f(aⁿ) = f(a o a o ... o a)
- = f(a) * f(a) * ... * f(a) [by repeated homomorphism]
- = [f(a)]ⁿ ∎

### 10.2 Kernel of a Homomorphism

**Definition:**
```
Ker φ = {x ∈ G : φ(x) = e'}
```
where e' is the identity of G'. The kernel is the set of elements that map to the identity of the codomain. It is always a **normal subgroup** of G.

**Note:** Ker φ △ G (normal subgroup), and G/Ker φ is the **factor group**.

- **Trivial homomorphism:** Every element of G maps to e' (the identity of G')

### 10.3 Types of Homomorphisms

| Type | Definition |
|---|---|
| **Homomorphism** | φ(xy) = φ(x)φ(y) |
| **Monomorphism** | Homomorphism + 1-1 (injective) |
| **Epimorphism** | Homomorphism + onto (surjective) |
| **Isomorphism** | Homomorphism + 1-1 + onto (bijective) |

### 10.4 Important Theorem on Homomorphism

**Theorem:** Let (G, o), (H, *) be groups with identities e_G, e_H. If f: G₁ → G₂ is an onto homomorphism, and G₁ is abelian, then G₂ is also abelian.

**Proof:**
- Take any x, y ∈ G₂. Since f is onto, there exist a, b ∈ G₁ such that f(a) = x, f(b) = y
- Then: x * y = f(a) * f(b) = f(a o b) [homomorphism]
- = f(b o a) [since G₁ is abelian]
- = f(b) * f(a) = y * x
- ∴ x * y = y * x for all x, y ∈ G₂, so G₂ is abelian ∎

---

## 11. Isomorphism

**Definition:** φ: G → G' is a **group isomorphism** if it needs to be:
- A homomorphism
- One-to-one (injective)
- Onto (surjective)

Denoted: G ≅ G' (G is isomorphic to G')

**Example:** Let (R, +) and (R⁺, ×) be the groups. Define f: R → R⁺ by f(x) = eˣ for all x ∈ R.

Then for all a, b ∈ R: f(a+b) = e^(a+b) = eᵃ × eᵇ = f(a) × f(b) → Homomorphism

Let c ∈ R⁺. Then log c ∈ R and f(log c) = e^(log c) = c → every element in R⁺ has a preimage, so f is **onto**.

For a, b ∈ R: f(a) = f(b) ⇒ eᵃ = eᵇ ⇒ a = b → f is **one-to-one**.

∴ f is an isomorphism from (R, +) to (R⁺, ×).

**Theorem:** If f is an isomorphism from G₁ onto G₂, then f⁻¹ is an isomorphism from G₂ onto G₁.

**Proof:**
- Since f: G₁ → G₂ is an isomorphism, it is one-to-one and onto. So f⁻¹ exists and is a one-to-one function from G₂ to G₁.
- Take any x, y ∈ G₂. Then there exist a, b ∈ G₁ such that f(a) = x, f(b) = y.
- Then f⁻¹(x) = a, f⁻¹(y) = b
- Since x, y ∈ G₂, and G₂ is closed under *, x * y ∈ G₂
- f⁻¹(x * y) = f⁻¹(f(a) * f(b)) = f⁻¹(f(a * b)) = f⁻¹ ∘ f(a * b) = a * b = f⁻¹(x) *₁ f⁻¹(y)
- ∴ f⁻¹ is a homomorphism from G₂ to G₁, and since it is also bijective, f⁻¹ is an isomorphism ∎

---

## 12. Cyclic Groups

**Definition:** A group G that can be generated by a single element of that group is called a **cyclic group**. An element 'g' generates the group if every element of the group can be obtained by repeatedly applying the group operation or its inverse to g.

Denoted: G = ⟨g⟩ (G is generated by g)

**Formal definition:** A group G is called cyclic if there is an element x ∈ G such that for each a ∈ G, a = xⁿ for some n ∈ Z.

**Key Properties:**
- Every cyclic group is **abelian** (since gʳ·gˢ = g^(r+s) = gˢ·gʳ)
- The identity element is also a cyclic element (trivially)

### 12.1 Finding Cyclic Elements

**For addition (Zₙ, +):** For each element, find the smallest positive power (multiple) that gives 0 (the identity).

**Example — Z₆ = {0, 1, 2, 3, 4, 5}:**

Check element 0: 0+0 = 0 (immediately gives identity, generates only {0})

Check element 1:
- 1.1 = 1, 2.1 = 2, 3.1 = 3, 4.1 = 4, 5.1 = 5, 6.1 = 0 ← generates all!

Check element 5:
- 5, 5+5=4, 5+5+5=3, 5+5+5+5=2, ... = 1, ... = 0 ← generates all!

∴ **1 and 5 are cyclic elements** of Z₆.

**For multiplication (Z*ₙ, ·):**

**Example — Z*₇ = {1, 2, 3, 4, 5, 6}:**

Check element 3:
- 3¹ = 3, 3² = 9 = 2, 3³ = 6, 3⁴ = 4, 3⁵ = 5, 3⁶ = 1 ← generates all!

∴ **3 is a cyclic element** of Z*₇.

**Example — U₄ = {1, −1, i, −i}:**
- 1 = i⁴, −1 = i², i = i¹, −i = i³
- Therefore, U₄ = ⟨i⟩ (generated by i), also U₄ = ⟨−i⟩ (since (−i)⁴ = 1, etc.)

---

## 13. Order of an Element

**Definition:** If G is a group and a is an element in G, the **order of a**, denoted O(a), is the smallest positive integer n such that aⁿ = e, where e is the identity element of the group.

- If ⟨a⟩ = {e}, then O(a) = 1
- If ⟨a⟩ ≠ e (which means it's finite): ⟨a⟩ = {aᵐ | m ∈ Z}

**Key facts:**
- If aˢ = aᵗ (1 ≤ s < t), then a^(t−s) = e, so O(a) is t−s
- ⟨a⟩ contains elements 1 to n−1, and aⁿ = e

**Theorem:** If a ∈ G with O(a) = n, and k ∈ Z, and aᵏ = e, then n|k.

**Proof:** By Division Algorithm: k = qn + r, 0 ≤ r < n
- Since aᵏ = e: a^(qn+r) = e
- (aⁿ)^q · aʳ = e [since aⁿ = e]
- e^q · aʳ = e
- aʳ = e
- Since n is the smallest positive integer such that aⁿ = e, and 0 ≤ r < n: r = 0
- ∴ k = qn, which means n | k ∎

**Note:** Finding the order of element aᵏ in a cyclic group: O(aᵏ) = n/gcd(n, k)

### 13.1 Generating Subgroups — Divisors of Modulo Element

**Example — (Z₁₂, +):** Elements = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11} = 12

To find subgroups, find divisors of 12: 12·1 = 0, 12/2 = 0, 12/3 = 0, 12/4 = 0, but 12/5 ≠ 0.

Subgroups of Z₁₂:
- {0}
- {0, 6}
- {0, 3, 6, 9}
- {0, 4, 8}
- {0, 2, 4, 6, 8, 10}
- {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11}

---

## 14. Theorems on Cyclic Groups

### Theorem 1: Cyclic group isomorphisms

**Let G be a cyclic group:**

**(a)** If |G| is infinite, then G is isomorphic to (Z, +)

**Proof:**
- G is generated by a, so G = ⟨a⟩ = {aᵏ | k ∈ Z}
- Define φ: G → Z by φ(aᵏ) = k
- **One-to-one:** Assuming φ(aᵏ) = φ(aˡ) means k = t. This means element aᵏ = aˡ must be the same, else it would contradict one-to-one. So there are no two elements mapping to the same element.
- **Onto:** For any m ∈ Z, φ(aᵐ) = m. So for every integer m in Z, there is some mapping done.
- **Homomorphism:** For aᵐ, aⁿ ∈ G: φ(aᵐ · aⁿ) = φ(a^(m+n)) = m+n; and φ(aᵐ) + φ(aⁿ) = m + n. ∴ It is homomorphism, onto, and one-to-one ⇒ isomorphism ∎

**(b)** If |G| = n where n > 1, then G is isomorphic to (Zₙ, +)

**Proof:**
- G is a cyclic group, so G = ⟨a⟩ and aⁿ = e, |G| = n
- φ: G → Zₙ by φ(aᵏ) = [k] (represents equivalence class of k mod n in Zₙ)
- φ(aᵣ · aᵐ) = φ(a^(r+m)) = [r+m] = [r]+[m] = φ(aʳ) + φ(aᵐ) ✓
- It is one-to-one and onto because each distinct power aʳ maps to a unique element [r] in Zₙ
- ∴ It is isomorphism ∎

### Theorem 2: Every subgroup of a cyclic group is cyclic

**Proof:**
- Let G be a cyclic group generated by 'a', i.e., G = ⟨a⟩
- Let H be a subgroup of G
- If H = {e}, then H = ⟨e⟩, which is cyclic.
- Otherwise, let k be the least positive integer such that aᵏ ∈ H, and b ∈ H.
- Since b ∈ H, b ∈ G, so b = aᵐ for some integer m.
- **Claim:** k | m.
- By division algorithm: m = qk + r, 0 ≤ r < k
- aʳ = a^(m−qk) = aᵐ · (aᵏ)^(−q) ∈ H [since aᵐ ∈ H, aᵏ ∈ H]
- But k is the least positive integer with aᵏ ∈ H, and 0 ≤ r < k, so r = 0.
- ∴ m = qk, so b = aᵐ = (aᵏ)^q, meaning every element of H can be expressed as (aᵏ)^q
- So H = ⟨aᵏ⟩ is cyclic ∎

### Theorem 3 (Exercise):

**(a)** If G has order 2p (p prime), then every proper subgroup of G is cyclic.

**(b)** If G has order p², then G has a subgroup of order p.

---

## 15. Cosets

**Definition:** If H is a subgroup of G, then for each a ∈ G, the set:
```
aH = {ah | h ∈ H}
```
is called a **left coset** of H in G. The set:
```
Ha = {ha | h ∈ H}
```
is called a **right coset** of H in G.

**Notes:**
- If the binary operation in G is addition, we write a + H in place of aH, where a + H = {a + h | h ∈ H}
- "Coset" means left coset unless otherwise specified
- If the group is abelian, there is no need to distinguish between left and right cosets

**Example:** For G = (Z₁₂, +) and H = {[0], [4], [8]}:
- [0] + H = {[0], [4], [8]}
- [1] + H = {[1], [5], [9]}
- [2] + H = {[2], [6], [10]}
- [3] + H = {[3], [7], [11]}
- [4] + H = {[4], [8], [0]} = [8] + H (same coset!)
- [5] + H = {[5], [9], [1]} = [9] + H (same coset!)

∴ ([0]+H) ∪ ([1]+H) ∪ ([2]+H) ∪ ([3]+H) is a **partition** of G.

---

## 16. Lagrange's Theorem

**Theorem:** If G is a finite group of order n with H a subgroup of order m, then **m divides n**.

### Lemma (used in proof):

If H is a subgroup of the finite group G, then for all a, b ∈ G:
1. **(a)** |aH| = |H|
2. **(b)** Either aH = bH or aH ∩ bH = ∅

**Proof of Lemma (a):**
- Since aH = {ah | h ∈ H}, |aH| ≤ |H|
- Assume |aH| < |H|: then ahi = ahj with hᵢ, hⱼ distinct elements of H. By left cancellation, hᵢ = hⱼ — contradiction.
- ∴ |aH| = |H| ∎

**Proof of Lemma (b):**
- Suppose aH ∩ bH ≠ ∅, so x = ah₁ = bh₂ for some h₁, h₂ ∈ H
- Then a = bh₂h₁⁻¹
- For any y ∈ aH: y = ah for some h ∈ H, so y = bh₂h₁⁻¹h ∈ bH, thus aH ⊆ bH
- Similarly, bH ⊆ aH
- ∴ aH = bH ∎

**Proof of Lagrange's Theorem:**
- Let G be a group, |G| = n
- Let H be a subgroup of G, |H| = m = {a₁, a₂, ..., aₘ}
- Left coset of H: aH = {ah | h ∈ H} for some a ∈ G
- If a ∉ H, then aH ≠ H, but aH ∩ H ≠ ∅
- If G = aH ∪ H, then |G| = |aH| + |H| = 2|H|
- This works for every other element g outside H
- G = a₁H ∪ a₂H ∪ ... ∪ aₖH (k is the number of left cosets of H)
- |G| = k|H|
- n = km
- ∴ m divides n ∎

**Number of cosets formula:**
```
Number of cosets = Number of elements / Number of subgroup elements
```

**Corollary:** The order of each element of a finite group divides the order of the group.

---

# UNIT 2: CODING THEORY

---

## 1. Introduction

Coding theory explores how to transmit data reliably across noisy communication channels. This theory was inspired by:
- Claude Shannon (1948)
- Marcel Golay (1949)
- Richard Hamming (1950)

It uses concepts from algebraic structures, probability, and combinatorics.

---

## 2. Basic Definitions

### 2.1 Message

A **message** is defined as a finite sequence of characters from a finite alphabet.

### 2.2 Word

In binary coding theory, the alphabet is:
```
B = {0, 1}
```
Every character we want to transmit is represented as a sequence of m elements from B. This binary string is called a **word** (our basic unit of information — a sequence of m 0's and 1's).

### 2.3 Group Structure of Binary Words

The set B = {0, 1} is a group under addition modulo 2, denoted by ⊕₂. This is denoted (Z₂, ⊕₂).

For a fixed integer m, the Cartesian product:
```
Z₂ᵐ = Z₂ × Z₂ × ... × Z₂  (m times)
```
forms a group under **componentwise addition modulo 2**, denoted ⊕:
```
(x₁, x₂, ..., xₘ) ⊕ (y₁, y₂, ..., yₘ) = (x₁⊕₂y₁, x₂⊕₂y₂, ..., xₘ⊕₂yₘ)
```

### 2.4 Notation

An element (b₁, b₂, b₃, ..., bₘ) in Z₂ᵐ is written as b₁b₂b₃...bₘ. For example, (1,0,1,1,0) ∈ Z₂⁵ is simply written as **10110**.

### 2.5 Properties of Z₂ᵐ

- **Identity element:** e = (0, 0, ..., 0)
- **Every element is its own inverse** (since x ⊕ x = 0 for binary)
- **Number of elements:** 2ᵐ

For instance, inverse of 10110 in Z₂⁵ is 10110, since 10110 ⊕ 10110 = 00000.

---

## 3. Transmission and Noise

In digital communication, information is transmitted from a sender to a receiver through a **transmission channel**.

When a word x ∈ Z₂ᵐ is transmitted over a channel, it may be received as xₜ ∈ Z₂ᵐ. Due to **noise** (electrical faults, interference), some bits may change:
```
xₜ ≠ x
```

The relationship: **r = c + e**
- r = received bit word
- c = coding word (transmitted)
- e = error word (error pattern)

---

## 4. Binary Symmetric Channel (BSC)

The **Binary Symmetric Channel** is a fundamental theoretical model:
- Each bit transmitted (0 or 1) has a fixed probability **p** of being received incorrectly
- Probability of correct transmission is **1 − p**
- The channel is **symmetric** (error probability is the same for 0 and 1)

### 4.1 Error Pattern

Let c ∈ Z₂ⁿ be the transmitted codeword. The received word is:
```
r = c ⊕ e
```
where e is the **error vector**:
- Positions of 1's in e indicate **locations of errors** in the received code
- Number of 1's in e = **number of bit errors** during transmission

**Example:** c = 10110, r = 00110
- e = r ⊕ c = 00110 ⊕ 10110 = 10000
- This means 1 error occurred at position 1

**Key equivalence:** c ⊕ e = r, or equivalently r ⊕ c = e

---

## 5. Probability of Errors

**Theorem:** Let c ∈ Z₂ⁿ be the transmitted codeword through BSC with probability p of incorrect transmission.

**When we know the position of errors (k errors in fixed positions):**
```
P = pᵏ(1−p)ⁿ⁻ᵏ
```

**When we know the number of errors (exactly k errors):**
```
Pₖ = C(n,k) · pᵏ · (1−p)ⁿ⁻ᵏ
```

### 5.1 Worked Examples

**Example 1:** c = 1010110, e = 0101101 → k = 4 errors (from 4 ones in e), p = 0.05
```
P = (0.05)⁴(1−0.05)^(7−4) = (0.05)⁴(0.95)³ ≈ 7.289 × 10⁻⁶
```

**Example 2:** c = 1010110, p = 0.02, r = 1011111 → k = 2 errors
```
P = (0.02)²(1−0.02)^(7−2) = (0.02)²(0.98)⁵ ≈ 0.000368
```

**Example 3:** p = 0.05, c = 011011101, n = 9, k = 1
```
P = C(9,1)(0.05)¹(1−0.05)⁸ = 9(0.05)(0.95)⁸ ≈ 0.2985
```

### 5.2 Finding Error Patterns

Given c and r, to get e: **compare c and r — whichever positions differ are marked 1 in e**.

Given c and e, to get r: r = c ⊕ e (XOR)

---

## 6. Encoding and Decoding

### 6.1 Encoding Function

Let m, n ∈ Z⁺ with n > m. Let W ⊆ Z₂ᵐ be the set of messages, and C ⊆ Z₂ⁿ be the set of codewords. The **encoding function** is:
```
E: W → Z₂ⁿ  with  E(w) = c ∈ C
```
This maps each m-bit word into an n-bit codeword by appending n−m extra bits, introducing **redundancy** to detect or correct errors.

### 6.2 Decoding Function

Upon receiving T(c), we apply the **decoding function**:
```
D: Z₂ⁿ → W
```
hoping to recover the original message w.

**Ideally:** D ∘ T ∘ E should be the identity on W, but this is not always possible due to errors. The goal is to design E and D so that the probability of correctly decoding is high.

### 6.3 Efficiency (Rate)

```
Rate = m/n
```
This measures the efficiency of the coding scheme.

### 6.4 Block Code

The ordered pair **(n, m)** is called the **block code** of E and D.

---

## 7. Parity-Check Code

**Definition (Weight):** For any x ∈ Z₂ᵐ, the number of 1's in x is called the **weight** of x, denoted wt(x).
- Example: x = 0111011 ⇒ wt(x) = 5

### 7.1 Even Parity Check

The **(m+1, m) block code** is called even parity check code. For any w = w₁w₂...wₘ ∈ Z₂ᵐ:
```
E: Z₂ᵐ → Z₂^(m+1)  by  E(w) = w₁w₂w₃...wₘwₘ₊₁
```
where:
```
wₘ₊₁ = 0  if wt(w) is even
wₘ₊₁ = 1  if wt(w) is odd
```

**Key properties:**
- Every codeword E(w) has **even weight**
- A **single-bit error** changes the weight to odd → detectable
- Any **odd number of errors** can be detected
- **Even number of errors** are NOT detected
- If an error is detected, the codeword is retransmitted

### 7.2 Odd Parity Check

```
wₘ₊₁ = 0  if wt(w) is odd
wₘ₊₁ = 1  if wt(w) is even
```

### 7.3 Parity Check Examples

| Original w | Even Parity E(w) | Odd Parity E(w) |
|---|---|---|
| 1011 | 10111 | 10110 |
| 1010 | 10100 | 10101 |
| 101101 | 1011010 | 1011011 |
| 110111 | 1101111 | 1101110 |

**Example — Odd parity on 101101:**
- Transmitted c = 1011011
- r = 1011011: no error
- r = 1111011: error detected (even number of 1's → flagged)
- r = 1001111: error NOT detected (odd number of 1's → appears correct)

### 7.4 Probability of Correct Detection (Parity Check)

Let p = bit error probability. The probability of sending c = 110101101 (n=9) and making at most one error:
```
P = (1−p)⁹ + C(9,1)·p·(1−p)⁸
```
For p = 0.001: ≈ 0.99996417

**Example:** 160 bits in strings of length 8, p = 0.001
- Without coding: P = (0.999)^160 ≈ 0.852076
- With parity check: Per 9-bit word: (0.999)⁹ + 9(0.001)(0.999)⁸ ≈ 0.999964; For 20 words: (0.999964)²⁰ ≈ 0.9999280

---

## 8. Triple Repetition Code

The **(3m, m) block code** is called the triple repetition code. For w = w₁w₂...wₘ:
```
E: Z₂ᵐ → Z₂^(3m)  by  E(w) = w₁w₂...wₘ | w₁w₂...wₘ | w₁w₂...wₘ
```

**Decoding function D:** Uses the **majority rule** — for each bit position, take the value that appears most (at least 2 out of 3 times).

**Encode/Decode Scheme:**
```
D(r) = D(r₁r₂...r₂ₘ, r₂ₘ₊₁...r₂ₘ₊₁, ...) = S₁S₂...Sₘ
```
where:
```
Sᵢ = 1  if rᵢ, rᵢ₊ₘ, rᵢ₊₂ₘ has majority of 1's
Sᵢ = 0  if rᵢ, rᵢ₊ₘ, rᵢ₊₂ₘ has majority of 0's
```

### 8.1 Error Analysis

- As long as **at most one error** happens in each group of three, the original bit is correctly recovered
- If **two or more errors** occur in the same group, decoding may fail

**Probability of correct decoding** (p = 0.001):
```
P_correct = (1−p)³ + C(3,1)·p·(1−p)² = (0.999)³ + 3(0.001)(0.999)² ≈ 0.999997
```

For 8-bit message: (0.999997)⁸ ≈ 0.999976

- **Rate:** m/3m = 1/3 (lower efficiency than parity check)
- **Advantage:** Can **correct** errors, not just detect them; no retransmission needed

---

## 9. Hamming Metric

### 9.1 Weight

**Definition:** For any x = x₁x₂...xₙ ∈ Z₂ⁿ, the **weight** of x is:
```
wt(x) = Σᵢ xᵢ  (number of 1's in x)
```

**Lemma:** For all x, y ∈ Z₂ⁿ: wt(x ⊕ y) ≤ wt(x) + wt(y)

### 9.2 Hamming Distance

**Definition:** The **Hamming distance** between x, y ∈ Z₂ⁿ is:
```
d(x, y) = number of positions where x and y differ
```

**Key Note:**
```
d(x, y) = wt(x ⊕ y)
```
Because xᵢ ⊕₂ yᵢ = 1 if and only if xᵢ ≠ yᵢ.

**Examples:**
- x = 01001, y = 11101 ⇒ d(x, y) = 2
- x = 10101, y = 10101 ⇒ d(x, y) = 0

### 9.3 Properties of Hamming Distance

For all x, y, z ∈ Z₂ⁿ:
1. **Non-negativity:** d(x, y) ≥ 0
2. **Identity:** d(x, y) = 0 ⟺ x = y
3. **Symmetry:** d(x, y) = d(y, x)
4. **Triangle Inequality:** d(x, z) ≤ d(x, y) + d(y, z)

The function d(x, y) is a **metric** (distance function), and (Z₂ⁿ, d) is called a **metric space**.

### 9.4 Sphere of Radius k

**Definition:** For a vector x ∈ Z₂ⁿ, the **sphere of radius k centered at x** is:
```
S(x, k) = {y ∈ Z₂ⁿ : d(x, y) ≤ k}
```

**Size:**
```
|S(x, k)| = Σᵣ₌₀ᵏ C(n, r)  =  1 + C(n,1) + C(n,2) + ... + C(n,k)
```

**Example:** For n = 10, x ∈ Z₂¹⁰:
- |S(x, 1)| = 1 + 10 = 11
- |S(x, 2)| = 1 + 10 + 45 = 56
- |S(x, 3)| = 1 + 10 + 45 + 120 = 176

---

## 10. Major Theorem on Error Detection/Correction

**Theorem:** Let E: W → C be an encoding function. Let the minimum distance between any two codewords be d_min.

1. **Error Detection:** We can detect all transmission errors of weight ≤ k iff:
   ```
   d_min ≥ k + 1
   ```

2. **Error Correction:** We can correct all transmission errors of weight ≤ k iff:
   ```
   d_min ≥ 2k + 1
   ```

**Intuition:**
- To detect k errors: the received word must be different from all codewords
- To correct k errors: the received word must be closer to the original codeword than to any other codeword

### 10.1 Example

Let W = Z₂², and E: W → Z₂⁶ defined by:
- E(00) = 000000
- E(10) = 101010
- E(01) = 010101
- E(11) = 111111

**Hamming distances:**
- d(000000, 101010) = 3
- d(000000, 010101) = 3
- d(000000, 111111) = 6
- d(101010, 010101) = 6
- d(101010, 111111) = 3
- d(010101, 111111) = 3

**Minimum distance = 3**

By theorem:
- **Detection:** k + 1 = 3 ⇒ k = 2 → can detect up to **2 errors**
- **Correction:** 2k + 1 = 3 ⇒ k = 1 → can correct up to **1 error**

### 10.2 Spheres and Decoding

For the same code:
- S(101010, 1) = {101010, 001010, 111010, 100010, 101110, 101000, 101011}
- S(111111, 1) = {111111, 011111, 101111, 110111, 111011, 111101, 111110}

**Decoding (majority rule for this triple structure):**
- Decode at odd positions (1,3,5) as one group, even positions (2,4,6) as another
- 110101: positions 1,3,5 = 1,0,0 → 0; positions 2,4,6 = 1,1,1 → 1 ⇒ Decoded: 01
- 101011: positions 1,3,5 = 1,1,1 → 1; positions 2,4,6 = 0,0,1 → 0 ⇒ Decoded: 10

---

## 11. Parity-Check Matrix and Generator Matrix

### 11.1 Generator Matrix G

A **generator matrix** G is used to encode a message vector into a codeword.

Let G be a k×n matrix over Z₂, and let w ∈ Z₂ᵏ be a message (row vector). The encoded codeword is:
```
E(w) = wG ∈ Z₂ⁿ
```

**Systematic encoding:** G = [Iₖ | A] where Iₖ is the k×k identity matrix and A is a k×(n−k) matrix. This ensures the original message appears directly in the codeword.

**Example:**
```
G = [1 0 0 | 0 1 0]
    [0 1 0 | 0 0 1]
    [0 0 1 | 1 0 1]
```
(3×6 matrix, first 3 columns = I₃)

**Computing codewords:**
- E(110) = (1,1,0)·G = 110101
- E(010) = (0,1,0)·G = 010011
- E(000) = 000000

Full codeset:
```
C = {000000, 100110, 010011, 001101, 110101, 101011, 011110, 111000}
```

### 11.2 Parity-Check Matrix H

The **parity-check matrix** H is used to verify if a received word is a valid codeword.

Given G = [Iₖ | A], define:
```
H = [Aᵀ | Iₙ₋ₖ]
```

**Example:** From G above with:
```
A = [0 1 0]
    [0 0 1]
    [1 0 1]
```

Then Aᵀ = [0 0 1]   and H = [0 0 1 | 1 0 0]
           [1 0 0]           [1 0 0 | 0 1 0]
           [0 1 1]           [0 1 1 | 0 0 1]

**Key property:** For any valid codeword c ∈ C:
```
H · cᵀ = 0
```
(The product of H with any codeword (transposed) equals the zero vector)

### 11.3 Syndrome Decoding

Given a received word r ∈ Z₂ⁿ, compute the **syndrome**:
```
syndrome(r) = H · rᵀ
```

- If H · rᵀ = **0**, then r is a valid codeword (no error detected)
- If H · rᵀ ≠ **0**, then r has errors

**Error identification:** The syndrome H · rᵀ matches a column of H corresponding to the position of the error. This tells you which bit to flip.

**Example:** r = 111010
```
H · rᵀ = [0 0 1 1 0 0] [1]   [1]
          [1 0 0 0 1 0] [1] = [0]
          [0 1 1 0 0 1] [1]   [1]
                         [0]
                         [1]
                         [0]
```
Result = [1, 0, 1]ᵀ → nonzero ⇒ error exists. Compare with columns of H to find which bit is in error.

### 11.4 Decoding Algorithm Using H

1. Receive word r
2. Compute syndrome s = H · rᵀ
3. If s = 0: r is a valid codeword. Decode by taking first k bits.
4. If s ≠ 0: Find which column of H equals s. The corresponding position has an error. Flip that bit to get corrected codeword. Then decode.

**Example of complete decode:**
For H given above, if syndrome = [1, 0, 1]ᵀ which is column 3 of H, the error is in bit 3. Flip bit 3 of r to get corrected codeword.

### 11.5 Complete Decoding Table Example

Given H and received words:

| Received Word | Syndrome | Error Position | Decoded Word |
|---|---|---|---|
| 111101 | [1,0,1]ᵀ | position 2 | 110101 |
| 110101 | [0,0,0]ᵀ | none | 110101 → message: 110 |
| 001111 | syndrome → | position 5 | 001101 |
| 100100 | syndrome → | position 5 | 100100 |

---

## 12. Error Detection and Correction Summary

| d_min | Errors Detected | Errors Corrected |
|---|---|---|
| 2 | 1 | 0 |
| 3 | 2 | 1 |
| 4 | 3 | 1 |
| 5 | 4 | 2 |
| 6 | 5 | 2 |
| 2t+1 | 2t | t |

### 12.1 Finding Minimum Distance from H

**Theorem:** The minimum distance d_min of a linear code with parity-check matrix H equals the minimum number of columns of H that are linearly dependent (sum to zero over Z₂).

In our example with d_min = 3:
- Can detect all errors of weight ≤ 2
- Can correct all errors of weight ≤ 1

---

## 13. Comprehensive Worked Examples

### Example A — Encoding Comparison

Let E: Z₂² → Z₂⁵:
- E(00) = 00001
- E(01) = 01010
- E(10) = 10100
- E(11) = 11111

**All pairwise distances:**
- d(00001, 01010) = 3
- d(00001, 10100) = 3
- d(00001, 11111) = 4
- d(01010, 10100) = 4
- d(01010, 11111) = 3
- d(10100, 11111) = 3

**d_min = 3** → detects ≤ 2 errors, corrects ≤ 1 error

### Example B — Five-times Repetition Code

E: Z₂ᵐ → Z₂^(5m), E(w) = wwwww. Decoding uses majority rule (can correct up to 2 errors out of 5).

For p = 0.05, probability of correctly decoding a single bit:
```
P = (0.95)⁵ + C(5,1)(0.05)(0.95)⁴ + C(5,2)(0.05)²(0.95)³ = 0.998842
```

For w = 110 (3-bit message): P = (0.998842)³ = 0.996530

**Decoding received word r = 0111001001 (m=2):**
- Positions 1,3,5,7,9: 0,1,0,1,0 → majority 0
- Positions 2,4,6,8,10: 1,1,0,0,1 → majority 1
- Decoded: **01**

**|D⁻¹(w)|** for m=2: |Z₂¹⁰| / |Z₂²| = 1024/4 = 256

---

## 14. Quick Reference Summary

### Group Theory Quick Facts

| Concept | Key Formula/Condition |
|---|---|
| Semigroup | Closure + Associativity |
| Monoid | Semigroup + Identity |
| Group | Monoid + Inverses |
| Abelian Group | Group + Commutativity |
| Subgroup test | H ≤ G iff ∀a,b ∈ H: ab⁻¹ ∈ H |
| Homomorphism | φ(xy) = φ(x)φ(y) |
| Isomorphism | Bijective homomorphism |
| Cyclic group | G = ⟨g⟩ for some g |
| Order of element | Smallest n: aⁿ = e |
| Lagrange's theorem | |H| divides |G| |
| Coset size | |aH| = |H| |

### Coding Theory Quick Facts

| Concept | Key Formula |
|---|---|
| Error pattern | r = c ⊕ e |
| P(k errors at fixed positions) | pᵏ(1−p)ⁿ⁻ᵏ |
| P(exactly k errors) | C(n,k)pᵏ(1−p)ⁿ⁻ᵏ |
| Code rate | m/n |
| Detect k errors | d_min ≥ k+1 |
| Correct k errors | d_min ≥ 2k+1 |
| Hamming distance | d(x,y) = wt(x⊕y) |
| Sphere size | Σᵣ₌₀ᵏ C(n,r) |
| Syndrome | s = H·rᵀ |
| Valid codeword | H·cᵀ = 0 |
| Generator matrix | E(w) = wG |
| Parity check matrix | H = [Aᵀ | Iₙ₋ₖ] |

---

# UNIT 3: CLARIFICATIONS AND DEEPER EXPLANATIONS (Doubts Resolved)

---

## 1. Modular Arithmetic — The ⊕ₙ Operation Explained

### 1.1 What does the circled + (⊕ₙ) mean?

The notation **a ⊕ₙ b** means:

> Add the numbers normally, then take the **remainder** when dividing by n.

```
a ⊕ₙ b = (a + b) mod n
```

**Examples:**

```
3 ⊕₄ 5 = (3 + 5) mod 4 = 8 mod 4 = 0
          (because 8 = 2×4 + 0, remainder is 0)

7 ⊕₇ 3 = (7 + 3) mod 7 = 10 mod 7 = 3
          (because 10 = 1×7 + 3, remainder is 3)

2 ⊕₄ 3 = (2 + 3) mod 4 = 5 mod 4 = 1

3 ⊕₄ 3 = (3 + 3) mod 4 = 6 mod 4 = 2

3 ⊕₄ 1 = (3 + 1) mod 4 = 4 mod 4 = 0
```

Notice: **every result stays inside {0, 1, 2, 3}**. This is exactly what closure requires.

---

### 1.2 The Group (Zₙ, +ₙ) — Full Explanation

The set:
```
Zₙ = {0, 1, 2, ..., n−1}
```
with the operation +ₙ (addition modulo n) forms a **group**.

**Full example — (Z₄, +₄):**

Set: Z₄ = {0, 1, 2, 3}

**Cayley (operation) table:**

| +₄ | 0 | 1 | 2 | 3 |
|----|---|---|---|---|
| 0  | 0 | 1 | 2 | 3 |
| 1  | 1 | 2 | 3 | 0 |
| 2  | 2 | 3 | 0 | 1 |
| 3  | 3 | 0 | 1 | 2 |

**Verifying all four group axioms:**

**1. Closure:** Every entry in the table belongs to {0,1,2,3}. Example: 2 +₄ 3 = 1 ∈ Z₄. ✓

**2. Associativity:** Follows from the fact that ordinary addition is associative, and taking mod does not break this.
```
(1 +₄ 2) +₄ 3 = 3 +₄ 3 = 2
 1 +₄ (2 +₄ 3) = 1 +₄ 1 = 2  ✓
```

**3. Identity element:** The element **0** is the identity.
```
0 +₄ 0 = 0
1 +₄ 0 = 1
2 +₄ 0 = 2
3 +₄ 0 = 3
```
Adding 0 leaves every element unchanged. ✓

**4. Inverse:** Every element has an inverse.
```
Inverse of 0: 0 +₄ 0 = 0  →  0⁻¹ = 0
Inverse of 1: 1 +₄ 3 = 0  →  1⁻¹ = 3
Inverse of 2: 2 +₄ 2 = 0  →  2⁻¹ = 2
Inverse of 3: 3 +₄ 1 = 0  →  3⁻¹ = 1
```
✓

Since all four axioms hold, **(Z₄, +₄) is a group**.

Also, since a +₄ b = b +₄ a (e.g., 3 +₄ 2 = 1 = 2 +₄ 3), it is an **Abelian group**.

---

### 1.3 How to Identify the Identity Element from a Cayley Table

**Rule:** The identity element is the element whose **row and column reproduce the headers (labels) exactly unchanged**.

**Example — Z₄ table:**

Look at the row for **0**:
```
+₄ | 0  1  2  3
 0 | 0  1  2  3   ← exactly matches the column headers
```

Look at the column for **0**:
```
+₄ | 0
 0 | 0
 1 | 1
 2 | 2
 3 | 3   ← exactly matches the row headers
```

Since the row and column of **0** simply copy the labels, **0 is the identity element**.

**General trick for any Cayley table:**
> Scan each row. The row that is identical to the top header row — the element labeling that row is the identity. Similarly verify the column.

**Another example — group {e, a, b}:**

| * | e | a | b |
|---|---|---|---|
| e | e | a | b |
| a | a | b | e |
| b | b | e | a |

The row labeled **e** gives: e, a, b — exactly the headers. So **e is the identity**. ✓

---

### 1.4 How to Find Inverses from a Cayley Table

**Rule:** To find the inverse of element x, look at the **row of x** and find the column where the entry is the **identity element (e)**. That column's label is x⁻¹.

**Example from Z₄:**

- Row of **1**: 1, 2, 3, **0** → identity appears in column **3** → 1⁻¹ = 3
- Row of **2**: 2, 3, **0**, 1 → identity appears in column **2** → 2⁻¹ = 2
- Row of **3**: 3, **0**, 1, 2 → identity appears in column **1** → 3⁻¹ = 1

**Example from {e, a, b}:**

| * | e | a | b |
|---|---|---|---|
| e | e | a | b |
| a | a | b | **e** |
| b | b | **e** | a |

- Row of **a**: a, b, **e** → identity appears in column **b** → a⁻¹ = b
- Row of **b**: b, **e**, a → identity appears in column **a** → b⁻¹ = a

**Verification:**
```
a · b = e   and   b · a = e
So a⁻¹ = b and b⁻¹ = a ✓
```

**Faster method using order 3 (cyclic argument):**

A group of order 3 is cyclic: G = {e, a, a²} with a³ = e.

Let b = a². Then:
```
a · b = a · a² = a³ = e  →  a⁻¹ = a² = b
b⁻¹ = (a²)⁻¹ = (a⁻¹)² = b² = a⁴ = a
```
This matches the table exactly.

---

### 1.5 Why (Zₙ, +ₙ) is Always Abelian

Since ordinary addition satisfies a + b = b + a, and taking mod n doesn't change this:
```
a +ₙ b = (a + b) mod n = (b + a) mod n = b +ₙ a
```

**Example:**
```
3 +₄ 2 = 5 mod 4 = 1
2 +₄ 3 = 5 mod 4 = 1  ✓
```

So **(Zₙ, +ₙ) is always an Abelian group**.

---

### 1.6 Why (Zₙ, +ₙ) is Always a Cyclic Group

**Definition:** A group is cyclic if one element generates the entire group by repeated application of the operation.

**For Z₄, starting with element 1:**
```
1
1 +₄ 1 = 2
1 +₄ 1 +₄ 1 = 3
1 +₄ 1 +₄ 1 +₄ 1 = 0
```
We got every element {0, 1, 2, 3}, so **1 generates Z₄**, and **Z₄ = ⟨1⟩** is cyclic.

**General fact:** In (Zₙ, +ₙ), any element k generates Zₙ if and only if gcd(k, n) = 1.

---

### 1.7 Cosets of Zₙ — Concrete Example

Consider Z₆ = {0, 1, 2, 3, 4, 5} and subgroup H = {0, 3}.

Left cosets (formed by adding each element of G to H):
```
0 + H = {0+0, 0+3} = {0, 3}
1 + H = {1+0, 1+3} = {1, 4}
2 + H = {2+0, 2+3} = {2, 5}
3 + H = {3+0, 3+3} = {3, 0} = {0, 3} = same as 0+H
4 + H = {4+0, 4+3} = {4, 1} = same as 1+H
5 + H = {5+0, 5+3} = {5, 2} = same as 2+H
```

So there are only **3 distinct cosets**: {0,3}, {1,4}, {2,5}. These **partition** Z₆.

**Lagrange's Theorem check:**
```
|H| = 2,  |G| = 6,  2 | 6  ✓
Number of cosets = |G|/|H| = 6/2 = 3  ✓
```

---

## 2. The Theorem: If x² = e for all x ∈ G, then G is Abelian — Fully Explained

### 2.1 The Statement

> If G is a group in which x² = e for every x ∈ G, then G is Abelian.

### 2.2 Why can we substitute x = ab?

**The assumption:** x² = e for **every** x ∈ G.

This means the statement applies to **any** element of G, without exception.

**Why ab is an element of G:**

Since a ∈ G and b ∈ G, and G is closed under its operation:
```
a, b ∈ G  ⟹  ab ∈ G   (Closure axiom)
```

So ab is an element of G. Since the assumption applies to every element of G, it applies to ab specifically:
```
(ab)² = e
```

**Analogy:**
> Suppose every student in a class scored 100.
> Ravi is a student in the class.
> Therefore Ravi scored 100.

Here:
- "Every student scored 100" = "every element of G satisfies x² = e"
- "Ravi is a student" = "ab ∈ G" (by closure)
- "Ravi scored 100" = "(ab)² = e"

### 2.3 Deriving a⁻¹ = a from x² = e

Since x² = e for all x ∈ G, in particular for x = a:
```
a² = e
a · a = e
```

But by definition, a⁻¹ is the element such that a · a⁻¹ = e.

Comparing:
```
a · a = e
a · a⁻¹ = e
```

By the **uniqueness of inverse** (proven in the elementary properties of groups), both equations have the same right-hand side e. Therefore:
```
a⁻¹ = a
```

Similarly, b⁻¹ = b.

This means **every element is its own inverse** when x² = e for all x.

### 2.4 Complete Proof that G is Abelian

**Given:** x² = e for all x ∈ G.

**To prove:** ab = ba for all a, b ∈ G.

**Step 1:** From the assumption, since ab ∈ G (closure):
```
(ab)² = e
abab = e
```

**Step 2:** From the assumption applied to a and b individually:
```
a² = e  →  a⁻¹ = a
b² = e  →  b⁻¹ = b
```

**Step 3:** Start from abab = e. Multiply both sides on the left by a:
```
a(abab) = ae
(aa)bab = a       [associativity]
e · bab = a       [since aa = a² = e]
bab = a
```

**Step 4:** Multiply both sides on the right by b:
```
bab · b = a · b
ba(bb) = ab       [associativity]
ba · e = ab       [since bb = b² = e]
ba = ab
```

**Therefore G is Abelian.** ∎

### 2.5 Why each step is valid — annotated

| Step | Expression | Reason |
|---|---|---|
| 1 | (ab)² = e | Assumption x²=e applies to ab since ab ∈ G by closure |
| 2 | abab = e | Expanding (ab)² |
| 3 | a(abab) = a·e | Multiplying both sides on left by a (valid in any group) |
| 4 | (aa)bab = a | Associativity; a·e = a (identity) |
| 5 | e·bab = a | Since a² = e |
| 6 | bab = a | Since e·x = x (identity) |
| 7 | (bab)b = ab | Multiplying both sides on right by b |
| 8 | ba(bb) = ab | Associativity |
| 9 | ba·e = ab | Since b² = e |
| 10 | ba = ab | Since x·e = x (identity) |

---

## 3. Modular Arithmetic — Connection to ALL Topics in the Syllabus

### 3.1 The Big Picture

The single operation:
```
a ⊕ₙ b = (a + b) mod n
```
is the **foundational operation** connecting every topic in this syllabus.

| Syllabus Topic | How Modular Arithmetic Connects |
|---|---|
| Groups | (Zₙ, +ₙ) is the most fundamental group example |
| Abelian Groups | Modular addition is commutative: a +ₙ b = b +ₙ a |
| Cyclic Groups | Zₙ is always cyclic, generated by 1 (or any element coprime to n) |
| Cosets | Formed by adding a fixed element to subgroup H using +ₙ |
| Lagrange's Theorem | Applied to subgroups of Zₙ; order of subgroup divides n |
| Coding Theory | Binary codes use Z₂ = {0,1} with addition mod 2 |
| Generator Matrix | All matrix multiplication done over Z₂ (mod 2 arithmetic) |
| Parity-Check Matrix | Verification Hcᵀ = 0 uses mod 2 arithmetic |

### 3.2 Z₂ and Its Role in Coding Theory

The simplest case is **n = 2**, giving Z₂ = {0, 1}:

**Addition table of Z₂:**

| ⊕₂ | 0 | 1 |
|----|---|---|
| 0  | 0 | 1 |
| 1  | 1 | 0 |

The critical fact: **1 + 1 = 0** (because 2 mod 2 = 0).

This is called **XOR** (exclusive or) in computer science.

**Why this matters for coding:**

When you compute the error pattern e = r ⊕ c, you are doing **bit-by-bit addition in Z₂**:

```
c = 1011
r = 1100
─────────
e = 0111   (1⊕1=0, 0⊕1=1, 1⊕0=1, 1⊕0=1)
```

### 3.3 Generator Matrix — Why it uses mod 2

The encoding formula c = mG computes each bit of the codeword as a **dot product in Z₂**:

```
c = m · G   (all arithmetic is mod 2)
```

Example: m = (1,1,0), G = [[1,0,0,0,1,0],[0,1,0,0,0,1],[0,0,1,1,0,1]]

```
c = (1,1,0) · G
  = 1·(1,0,0,0,1,0) + 1·(0,1,0,0,0,1) + 0·(0,0,1,1,0,1)   [mod 2]
  = (1,0,0,0,1,0) ⊕ (0,1,0,0,0,1) ⊕ (0,0,0,0,0,0)
  = (1,1,0,0,1,1)
```

Every addition here is mod 2.

### 3.4 Parity-Check Matrix — Why Hcᵀ = 0

For a valid codeword c, the syndrome is:
```
H · cᵀ = 0   (mod 2)
```

This is because G and H are constructed so that **G · Hᵀ = 0 mod 2** by design. Every valid codeword c = mG automatically satisfies:
```
H · cᵀ = H · (mG)ᵀ = H · Gᵀ · mᵀ = 0 · mᵀ = 0
```

If a received word r has an error, then r = c ⊕ e, and:
```
H · rᵀ = H · (c ⊕ e)ᵀ = H · cᵀ ⊕ H · eᵀ = 0 ⊕ H · eᵀ = H · eᵀ ≠ 0
```

The syndrome H · rᵀ is nonzero and tells you **exactly where the error is**.

---

## 4. Cayley Tables — How to Read and Use Them

### 4.1 What is a Cayley Table?

A **Cayley table** (also called a multiplication table or operation table) is a complete table showing the result of the group operation for every pair of elements.

For a group G = {g₁, g₂, ..., gₙ}, the entry in row i and column j is gᵢ * gⱼ.

### 4.2 How to Check All Group Axioms from a Cayley Table

**Step 1 — Closure:** Every entry in the table must be an element of G. If any entry is outside G, closure fails.

**Step 2 — Identity:** Find the row and column that reproduce the header labels unchanged. That element is the identity.

**Step 3 — Inverses:** For each element x, find where **e** (identity) appears in row x. The column heading of that position is x⁻¹.

**Step 4 — Associativity:** Cannot be directly "read" from the table — must be verified algebraically or by noting the structure (e.g., it is a known group like Zₙ).

**Step 5 — Commutativity (for Abelian check):** The table must be **symmetric about its main diagonal**, meaning the entry in row i, column j equals the entry in row j, column i.

### 4.3 Example — Full analysis of a 3-element group

| * | e | a | b |
|---|---|---|---|
| e | e | a | b |
| a | a | b | e |
| b | b | e | a |

**Closure:** All entries are in {e, a, b} ✓

**Identity:** Row e = [e, a, b] = header row → **e is the identity** ✓

**Inverses:**
- Row a: a, b, **e** → e appears in column b → **a⁻¹ = b**
- Row b: b, **e**, a → e appears in column a → **b⁻¹ = a**
- Row e: **e**, a, b → e appears in column e → **e⁻¹ = e**

**Commutativity check:** Is the table symmetric?
- a * b = e and b * a = e → same ✓
- All entries mirror across the diagonal → **Abelian** ✓

This group is actually Z₃ = {0, 1, 2} under addition mod 3, with the relabeling e↔0, a↔1, b↔2.

---

## 5. Summary of Key "How and Why" Points

| Question | Answer |
|---|---|
| Why is 0 the identity in Zₙ? | Because a +ₙ 0 = a for all a (adding 0 changes nothing, even after mod) |
| How do we find the identity from a table? | Look for the row/column that reproduces the headers exactly |
| How do we find inverses from a table? | In row x, find where e appears — that column label is x⁻¹ |
| Why can we write (ab)² = e? | Because ab ∈ G by closure, and the assumption x²=e applies to all elements of G |
| Why does x²=e imply x⁻¹=x? | Because x·x=e and x·x⁻¹=e, so by uniqueness of inverse, x=x⁻¹ |
| What does ⊕ₙ mean? | (a+b) mod n — normal addition followed by remainder upon dividing by n |
| Why is (Zₙ,+ₙ) always abelian? | Because (a+b) mod n = (b+a) mod n since ordinary addition is commutative |
| Why is (Zₙ,+ₙ) always cyclic? | Because 1 generates all elements: 1, 2, 3, ..., n−1, 0 by repeated addition |
| Why does coding theory use Z₂? | Binary data is 0s and 1s; Z₂ captures exactly this with 1+1=0 (mod 2) |
| What does Hcᵀ=0 mean geometrically? | The codeword c lies in the null space of H; errors push it out of this space |