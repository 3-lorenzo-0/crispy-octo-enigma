**Number Representation Methods in Computer Systems**

Computer systems represent numbers in binary using different encoding schemes to handle both positive and negative integers. The four main methods are unsigned, sign-magnitude, one's complement, and two's complement.

**1. Unsigned Representation**

All n bits are used to represent the magnitude of the number. There is no sign bit, so only non-negative integers can be stored. For an n-bit system, the range is 0 to 2ⁿ−1. For 8 bits: 0 to 255. Example: the number 5 is stored as 00000101.

**2. Sign-Magnitude Representation**

The Most Significant Bit (MSB) is the sign bit — 0 for positive, 1 for negative. The remaining (n−1) bits hold the magnitude (absolute value). For 8 bits, the range is −127 to +127, i.e., −(2ⁿ⁻¹−1) to +(2ⁿ⁻¹−1).

8-bit example for −5: 1 0000101 (MSB=1 means negative, 0000101 = 5)

Drawback: two representations of zero exist — +0 (00000000) and −0 (10000000) — which wastes one bit pattern and complicates arithmetic circuits.

**3. One's Complement Representation**

To negate a number, invert all bits (flip every 0 to 1 and every 1 to 0). The range for n bits is the same as sign-magnitude: −(2ⁿ⁻¹−1) to +(2ⁿ⁻¹−1).

8-bit example for −5: +5 = 00000101 → invert all bits → 11111010

Drawback: still has two zeros (+0 = 00000000 and −0 = 11111111). Addition also requires an end-around carry — the carry out of the MSB must be added back to the result — which adds extra hardware complexity.

**4. Two's Complement Representation**

The most widely used method in modern computers. To negate a number: invert all bits (one's complement) then add 1. The range for n bits is −2ⁿ⁻¹ to +(2ⁿ⁻¹−1). For 8 bits: −128 to +127.

8-bit example for −5:

- Start with +5 = 00000101
- Invert all bits = 11111010
- Add 1 = 11111011 ← this is −5

Key advantage: only one representation of zero (00000000). Adding −5 and +5 gives 00000000 with the carry discarded, which verifies correctness. The MSB carries a negative weight (−2ⁿ⁻¹), so the value of any two's complement number = −B₇×2⁷ + B₆×2⁶ + ... + B₀×2⁰.

**Comparison Table (Section 2.5)**

|Feature|Unsigned|Sign-Magnitude|One's Complement|Two's Complement|
|---|---|---|---|---|
|Negation method|N/A|Flip MSB|Invert all bits|Invert all bits + 1|
|Zeros|One|Two (+0, −0)|Two (+0, −0)|One|
|8-bit range|0 to 255|−127 to +127|−127 to +127|−128 to +127|
|Extra hardware|None|Sign-check logic|End-around carry|None|
|Used in modern CPUs|For addresses/counters|Rarely|Rarely|Yes — universally|

**Two's Complement Representation**

Two's complement is the most widely used signed number representation in modern computers. It encodes both positive and negative integers in n bits such that addition and subtraction use the same hardware circuit, with no special sign handling required.

---

**Negation Procedure (two steps)**

Step 1: Invert all bits (one's complement). Step 2: Add 1 to the result.

Example — represent −5 in 8 bits:

- Start with +5 = 00000101
- Invert all bits = 11111010
- Add 1 = 11111011 ← this is −5

Verification — represent +5 from −5:

- Start with −5 = 11111011
- Invert all bits = 00000100
- Add 1 = 00000101 = +5 ✓

---

**Shortcut Method**

Scan bits from right (LSB) to left. Copy all bits up to and including the first 1 encountered. Invert all remaining bits to the left of it.

Example — two's complement of 00101100:

- First 1 from right is at position 2
- Copy rightmost bits including that 1 → ...100
- Invert everything to the left → 11010100
- Result: 11010100 (same as the two-step method, faster by hand)

---

**Range Formula**

In n-bit two's complement, the MSB has a negative weight of −2ⁿ⁻¹. The most negative number is when MSB=1 and all other bits=0, giving −2ⁿ⁻¹. The most positive number is when MSB=0 and all other bits=1, giving +(2ⁿ⁻¹−1).

Therefore range = −2ⁿ⁻¹ to +(2ⁿ⁻¹−1)

For 8 bits: −128 to +127 For 16 bits: −32768 to +32767 For 32 bits: −2,147,483,648 to +2,147,483,647

Note: there is one more negative number than positive. The value −128 in 8 bits (10000000) has no positive counterpart since +128 cannot be represented.

---

**MSB Negative-Weight Formula**

For an 8-bit number B₇B₆B₅B₄B₃B₂B₁B₀, the decimal value is:

Value = −B₇×2⁷ + B₆×2⁶ + B₅×2⁵ + B₄×2⁴ + B₃×2³ + B₂×2² + B₁×2¹ + B₀×2⁰

Example — verify 11111011 = −5: = −1×128 + 1×64 + 1×32 + 1×16 + 1×8 + 0×4 + 1×2 + 1×1 = −128 + 64 + 32 + 16 + 8 + 2 + 1 = −128 + 123 = −5 ✓

---

**Advantages**

- Only one representation of zero (00000000). Adding −0 to itself gives 100000000; discarding the carry gives 00000000, confirming uniqueness.
- The same adder circuit handles both addition and subtraction — to subtract B from A, simply compute A + (two's complement of B). No separate subtraction hardware is needed.
- No end-around carry is required, unlike one's complement.
- Universally adopted in all modern processors and programming languages.


**Overflow Detection in Binary Arithmetic**

Overflow occurs in signed binary arithmetic when the result of an operation is too large or too small to be represented in the available number of bits. It happens only with signed numbers and only when adding two numbers of the same sign.

---

**Two Conditions for Overflow**

1. Positive + Positive = Negative (result exceeds +2ⁿ⁻¹−1, so MSB incorrectly becomes 1)
2. Negative + Negative = Positive (result is below −2ⁿ⁻¹, so MSB incorrectly becomes 0)

---

**Hardware Detection Rule**

Overflow is detected by XORing the carry into the MSB (Cᵢₙ) with the carry out of the MSB (Cₒᵤₜ):

Overflow = Cᵢₙ XOR Cₒᵤₜ

If both carries are the same (both 0 or both 1), no overflow. If they differ, overflow has occurred. This is how ALUs detect overflow in hardware using a single XOR gate.

---

**Example 3 — (+7) + (+4) in 4-bit two's complement**

```
  0111   (+7)
+ 0100   (+4)
------
  1011   (−5 in two's complement — WRONG)
```

Carry into MSB (bit 3) = 1 Carry out of MSB = 0 Cᵢₙ XOR Cₒᵤₜ = 1 XOR 0 = 1 → Overflow detected ✓

The correct answer is +11, but +11 cannot be represented in 4-bit two's complement (range is −8 to +7). The result wrapped around and appears as −5, which is clearly wrong since two positives cannot sum to a negative.

---

**Example 4 — (−7) + (−4) in 4-bit two's complement**

−7 in 4-bit = 1001, −4 in 4-bit = 1100

```
  1001   (−7)
+ 1100   (−4)
------
 10101   → discard carry out → 0101 (+5 — WRONG)
```

Carry into MSB (bit 3) = 1 Carry out of MSB = 1... let's redo carefully:

```
  1001
+ 1100
------
  0101  with carry out = 1
```

Carry into MSB = 0, Carry out of MSB = 1 Cᵢₙ XOR Cₒᵤₜ = 0 XOR 1 = 1 → Overflow detected ✓

The correct answer is −11, which cannot be represented in 4-bit two's complement (minimum is −8). The result appears as +5, which is clearly wrong since two negatives cannot sum to a positive.

---

**Opposite Sign Rule**

Overflow can never occur when adding two numbers of opposite signs. If one operand is positive and the other is negative, the true result is always closer to zero than either operand, so it always fits within the representable range. No XOR check is needed in this case.


