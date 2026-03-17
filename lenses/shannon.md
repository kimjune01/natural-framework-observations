# Shannon Communication Model — Inter-Stage Contracts

Source: Shannon, "A Mathematical Theory of Communication," *Bell System Technical Journal* 27, 1948. Cover & Thomas, *Elements of Information Theory*.

## Roles

| Role | Definition |
|------|-----------|
| **Source** | Produces the message to be communicated |
| **Encoder** | Converts the message into a signal suitable for the channel |
| **Channel** | The medium through which the signal travels |
| **Noise** | Interference that corrupts the signal during transmission |
| **Decoder** | Reconstructs the message from the received signal |
| **Destination** | The intended recipient of the message |

## Contracts

### Source Coding Theorem (Source → Encoder)

No lossless compression scheme can compress below the entropy rate on average:

```
L ≥ H(X)
```

The encoder's average codeword length L must be at least the source entropy H(X). For lossy compression, R ≥ R(D) where R(D) is the rate-distortion function.

### Channel Capacity Theorem (Encoder → Channel → Decoder)

For a discrete memoryless channel with capacity C, reliable communication requires:

```
R < C    where C = max_p(x) I(X; Y)
```

For continuous channels (Shannon-Hartley): C = B log₂(1 + S/N).

The code rate must not exceed channel capacity. This is a mathematical limit, not an engineering recommendation.

### Feasibility Constraint (End-to-end)

```
H(Source) < C(Channel)
```

Source entropy must be less than channel capacity for reliable communication.

### Rate-Distortion (Source → Encoder, lossy)

For lossy compression with distortion measure d(x, x̂):

```
R(D) = min_{p(x̂|x): E[d(X,X̂)]≤D} I(X; X̂)
```

Encoder and decoder must agree on the distortion metric.

### Per-Transition Contracts

| Transition | Contract |
|-----------|---------|
| Source → Encoder | Encoder must know source statistics p(x). Output rate R ≥ H(X) for lossless, R ≥ R(D) for lossy |
| Encoder → Channel | Code rate R < C. Symbol alphabet must match channel input alphabet |
| Channel → Decoder | Decoder must know channel model p(y\|x). Must implement inverse of encoder's code |
| Decoder → Destination | For R < C, reconstruction error probability → 0 as block length → ∞ |
| Encoder ↔ Decoder | Shared codebook. Same distortion metric for lossy coding |

### Separation Theorem

Optimal performance is achievable by separating source coding and channel coding. Source encoder output rate must match channel encoder input rate.

## Contract Strength

**Very strong.** Shannon provides the strongest formal contracts of any lens in the study. These are proven mathematical theorems with impossibility results — no system can violate them. The contracts are about information-theoretic limits (rate, capacity, distortion), not about data content at handoffs. The question is whether these mathematically rigorous contracts help with *reconstruction fidelity* when applied as a decomposition lens to real systems.
