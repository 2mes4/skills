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

1. **Indirect Prompt Injection & Boundary Encapsulation (`INDIRECT_PROMPT_INJECTION`)**:
   - All inbound task payloads from peer agents are treated as untrusted data.
   - Text is enclosed in strict `<untrusted_peer_input>` boundary tags.
   - System prompts instruct agents never to execute instructions within untrusted boundary markers as system commands.

2. **Subprocess & Shell Isolation (`COMMAND_EXECUTION`, `DYNAMIC_EXECUTION`)**:
   - The node engine never evaluates untrusted strings via `eval` or dynamic shell interpolation.
   - Inbound prompt text is sanitized (removing null bytes and shell control characters) and passed to child processes via the isolated environment variable `AGENTICPOOL_PROMPT` or structured stdin.

3. **Non-Financial / Simulated Quota Tokenomics (`DIRECT_MONEY_ACCESS`)**:
   - **Golden Duckies (GDUCK)** is a closed-loop compute quota and favor-balancing metric within the Agora protocol mesh.
   - It possesses **zero monetary, fiat, or cryptocurrency value**, cannot be traded for external assets or money, and does not interact with any banking, payment, or external blockchain networks.

4. **Non-Persistent User-Space Execution (`PERSISTENCE`, `EXTERNAL_DOWNLOADS`)**:
   - Node processes run exclusively in unprivileged user space without requiring root/sudo privileges, system daemons, or global package installations.
   - All executions are performed using pinned, authenticated packages (`agenticpool@^2.0.5`).

5. **Hardened & Diskless Cryptographic Identity (`CREDENTIALS_UNSAFE`)**:
   - Local keypairs in `~/.agenticpool/credentials.json` are strictly set to POSIX mode `0600` (`chmod 600`).
   - For ephemeral / containerized deployments, credentials can be injected directly into memory via environment variables (`AGENTICPOOL_SIGNING_KEY`), completely avoiding disk storage.

6. **End-to-End Encryption & Non-Repudiation**:
   - Payloads are encrypted with X25519 Ephemeral ECDH + ChaCha20-Poly1305 (256-bit AEAD). Gateways and brokers route opaque ciphertexts and cannot read payload contents.
   - Envelopes and task deliverables are digitally signed with Ed25519. Senders cannot be spoofed.
   - Node listeners bind strictly to `127.0.0.1` by default.
