# TapProof

> Authentication infrastructure for physical products

---

## The Problem

The counterfeit economy is worth over $500 billion a year. Most of it goes unchallenged – not because the technology to stop it doesn't exist, but because the solutions available today either cost a fortune to integrate or don't actually work.

Lets walk through what "authentication" usually means in practice. A resale marketplace sticks a plastic tag with a QR code on a product. A QR code is a static image. If you can photograph it, you can reprint it. It proves the link resolves, not that the product is real.

Some brands have moved to NFC. But most deployments use static chips – the chip broadcasts the same data on every scan. A counterfeiter can clone it in minutes with a $10 blank tag and a standard Android phone. The cloned chip passes every check.

So we're using digital tools, but in a fundamentally analog way. The tag is a label, not a proof. TapProof is an attempt to fix that at the infrastructure level.

---

## What TapProof Does

TapProof embeds NFC chips that generate a **unique cryptographic signature on every single tap**. Not a serial number. Not a stored code. A one-time proof, generated fresh by hardware-secured keys baked into the silicon.

Every scan produces a different output. Replaying a captured scan fails. Cloning the chip's readable memory produces garbage. Brute-forcing the underlying key isn't a realistic attack – the math doesn't allow it in any useful timeframe.

The verification flow from a consumer's perspective: tap the product with a phone. Under two seconds later, the result comes back. Authentic or not. No app install required on Android. No account. No "in our expert opinion." The backend validates the cryptographic output against the expected state and returns a binary answer.

The tag doesn't store proof. It generates it. That distinction is what makes cloning structurally hard rather than just inconvenient.

---

## Architecture

```
┌────────────────┐         ┌─────────────────┐         ┌──────────────────┐
│   Secure NFC   │── tap ──│  Consumer Phone  │── api ──│   TapProof Core  │
│   Hardware     │         │  (browser-based) │         │                  │
│                │         │                  │         │  ▸ signature     │
│  ▸ hw-secured  │         │  ▸ no app needed │         │    validation    │
│    keys        │         │    on android    │         │  ▸ counter state │
│  ▸ rolling     │         │  ▸ reads NFC URL │         │    tracking      │
│    counter     │         │    natively      │         │  ▸ provenance    │
│  ▸ per-tap     │         │                  │         │    engine        │
│    unique code │         │                  │         │  ▸ anomaly       │
└────────────────┘         └─────────────────┘         │    detection     │
                                                        └──────────────────┘
```

The three layers – verification, provisioning, and ownership – are architecturally separated. A compromise in one doesn't cascade to the others.

The backend holds the corresponding keys, validates the cryptographic output, tracks the rolling counter state, and flags anything anomalous: a counter that jumped backwards, the same tag scanned in two cities within an hour, scan velocity that doesn't match human behavior. Individual scans tell you if a product is real. The pattern of scans tells you if something is wrong.

---

## Built for the Full Product Lifecycle

Most authentication tools are designed around a single sale. One product, one buyer, done. That's not how things actually work.

Sneakers flip multiple times before someone wears them. Luxury bags circulate for years. Collectible bottles change hands at auction. The product's life extends well past the original purchase, but authentication solutions typically don't.

TapProof tracks ownership through the product's entire journey:

- **Ownership transfers** are recorded against the tag's live cryptographic state at the moment of transfer – not as a manual database entry someone types in later.
- **Chain of custody** builds automatically from every scan and transfer. The tenth owner has the same ability to verify authenticity as the first.
- **Cross-platform verification** means it doesn't matter where the product was originally bought or where it's being resold. Any phone, anywhere, reads a TapProof tag.

---

## Why This Is Possible Now

Three things converged recently that make this buildable without enterprise budgets.

**Cost.** Cryptographic NFC silicon is now below $0.25/unit at volume. On a $150 sneaker, that's 0.17% of retail. The economics stopped being a blocker for anything above roughly $30.

**Regulation.** The EU's Digital Product Passport requirement rolls out from 2027. Every product sold in Europe needs a unique digital identity. The current leading compliance approach is QR codes – which counterfeiters will clone immediately. There's a real gap between what the regulation demands and what the obvious solution actually provides.

**Tooling.** The open-source ecosystem for secure NFC verification matured to production quality in 2024. The cryptographic primitives exist. What's been missing is the product layer that turns them into something brands can actually ship with.

---

## Where It's Headed

| Phase | Sector | Reason |
| --- | --- | --- |
| 1 | Streetwear, sneakers, resale | Highest counterfeit rates, tech-native consumers, existing obsession with provenance |
| 2 | Premium spirits and collectibles | High unit values, auction fraud is common, buyers willing to pay for certainty |
| 3 | Consumer goods under EU DPP | Compliance deadline creates an adoption window across textiles, electronics, furniture by 2028–2030 |

---

## Design Decisions

**The security comes from the cryptography, not from secrecy.** The system works because the math is sound, not because we hope nobody figures out how it's built. Open standards, auditable architecture.

**Verification is a different question than identification.** Lots of solutions can tell you what a tag's serial number is. TapProof answers whether the tag is real, right now, on this specific scan. That's a harder problem.

**Friction kills adoption.** If verification requires an app download, an account, or instructions longer than "tap here," most people won't do it. The full flow is browser-based on Android and a lightweight companion on iOS.

**Honest about what NFC can't do.** No chip-based system can verify that the physical product inside the packaging matches the tag. The "real tag on a fake product" problem is real and addressed through hardware binding and multi-factor correlation – not ignored.

---

## Status

The application is in **production**. The codebase is private.

This public repository exists to document the architecture, explain the problem being solved, and provide context for people evaluating the project – whether that's for a pilot, a partnership, or just technical curiosity.

---

## Get in Touch

If you're working on product authentication, thinking about EU DPP compliance, or just want to talk through why most NFC deployments are more vulnerable than they look – reach out.

**talwarpranav929@gmail.com**

---

*TapProof is an independent project. Not affiliated with NXP Semiconductors or any chip manufacturer.*
