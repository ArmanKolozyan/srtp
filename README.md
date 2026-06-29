srtp (fork)
===========

Rust wrapper for libsrtp2. Forked from [HyeonuPark/srtp](https://github.com/HyeonuPark/srtp).

# Why this fork?

For fine-grained forward secrecy we need to rekey an SRTP stream far more often
than once per call setup (per video frame, or even per packet). The upstream crate
hides the underlying `srtp_t` handle, so it cannot reach a stream to swap its key
without rebuilding the whole stream, which is expensive.

# The change

Added one method, `Session::inplace_rekey(ssrc, key, salt)` (in `src/session.rs`).
It installs a new AES-128-GCM session key and salt directly into the existing
cipher (and preserves the replay database). It wraps the `srtp_inplace_rekey` C
function added in our
[libsrtp fork](https://github.com/ArmanKolozyan/libsrtp/tree/inplace-rekey-2.8.0).
