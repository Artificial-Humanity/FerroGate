# FerroGate (`fl-gate`)

**Zero-knowledge AES-256-GCM agent secrets vault, outbound HTTP credential proxy (`{{FERROGATE:KEY}}`), declarative API-to-MCP gateway, local MCP Registry `v0.1`, and inbound/outbound event bridge.**

`FerroGate` is an independent sub-product of the [FerroLoop](https://github.com/Artificial-Humanity/FerroLoop) suite. It can be used standalone as a Rust library (`fl-gate`) or through `fl vault` and `fl gateway` in the `FerroLoop` CLI and [FerroDeck](https://github.com/Artificial-Humanity/FerroDeck).

---

## Architecture & Capabilities

1. **Zero-Knowledge Encrypted Vault (`src/vault.rs`):**
   - `ring` AES-256-GCM encrypted store (`vault.enc`) with 256-bit master keys and 96-bit nonces.
   - AEAD Additional Authenticated Data (AAD) binds each secret's policy (`name`, `allowed_hosts`, `allowed_methods`, `allowed_agents`, `allow_private_network`, `lease_ttl_secs`, `expires_at`) into the authentication tag so policy tampering on disk is rejected cryptographically.
   - Atomic lock-serialized updates (`vault.enc.lock`) and `0600` append-only audit logs (`gate-audit.jsonl`).
2. **Outbound HTTP Credential Proxy & Secret Scrubber (`src/proxy.rs`):**
   - Substitutes `{{FERROGATE:KEY}}` placeholders at the outbound HTTP transport boundary while keeping raw credentials out of agent prompts and memory.
   - Enforces per-secret host, HTTP method, agent identity, lease expiration, and SSRF private-network guards.
   - Scrubs plaintext secrets, base64-encoded secrets, and standard vendor token prefixes (`sk-ant-`, `sk-proj-`, `ghp_`, `github_pat_`, `xoxb-`, etc.) from responses and error logs.
3. **Declarative REST/OpenAPI-to-MCP Gateway (`src/connector.rs`, `src/mcp.rs`):**
   - Loads `.toml` connector manifests and imports OpenAPI 3.x specs into governed MCP `tools/list` and `tools/call` endpoints with response field projection.
4. **Local & Tiered MCP Registry `v0.1` (`src/registry.rs`, `src/adapter.rs`):**
   - Serves MCP Registry `v0.1` discovery endpoints (`/v0.1/servers`) and provides zero-dependency trait sockets (`SecretProviderAdapter`, `McpRelayAdapter`, `McpRegistryAdapter`, and `EdgeGuardAdapter`).
5. **Inbound & Outbound Event Bridge (`src/bridge.rs`):**
   - Constant-time webhook secret verification (`verify_inbound_bridge_webhook`), sender allowlists, and routing to `wire:<persona>` or `connector:<id>`.

---

## Documentation

- [docs/specs/2026-10-27-ferrogate-design.md](docs/specs/2026-10-27-ferrogate-design.md) — FerroGate design specification.
- [docs/audits/2026-10-31-gate-collab-cloud-audit.md](docs/audits/2026-10-31-gate-collab-cloud-audit.md) — Security and concurrency audit record.

---

## Verification

```bash
cargo fmt --all -- --check
cargo clippy --all-targets --workspace -- -D warnings
cargo test --workspace
```

## License

Licensed under [Apache-2.0](LICENSE).
