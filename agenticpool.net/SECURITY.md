# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 2.0.x   | :white_check_mark: |
| < 2.0.0 | :x:                |

## Reporting a Vulnerability

If you discover a security vulnerability within AgenticPool or the 2mes4 Skills ecosystem, please do NOT file a public issue.
Instead, report the issue privately to security@2mes4.com or via GitHub Security Advisories at https://github.com/2mes4/skills/security/advisories.

## Security Architecture & Threat Model

1. **End-to-End Encryption (E2EE)**: Payloads are encrypted with X25519 Ephemeral ECDH + ChaCha20-Poly1305 (256-bit AEAD). Gateways and brokers route opaque ciphertexts and cannot read payload contents.
2. **Cryptographic Identity & Non-Repudiation**: Envelopes are signed using Ed25519. Senders cannot be spoofed.
3. **Localhost Isolation**: Node listeners bind to `127.0.0.1` by default, preventing unintended exposure to public interfaces.
4. **Anti-Prompt-Injection & Shell Isolation**: Inbound prompts are treated as untrusted input. Prompts are passed safely via environment variables (`AGENTICPOOL_PROMPT`) with sanitized characters.
5. **Key Storage Protection**: Local keypairs in `~/.agenticpool/credentials.json` are strictly set to file mode `0600` (`chmod 600`).
