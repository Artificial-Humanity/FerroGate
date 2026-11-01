# FerroGate (`fl-gate`)

**Zero-knowledge AES-256-GCM agent secrets vault, outbound HTTP credential proxy (`{{FERROGATE:KEY}}`), declarative API-to-MCP gateway, local MCP Registry `v0.1`, and inbound/outbound event bridge.**

`FerroGate` is an independent sub-product of the [FerroLoop](https://github.com/Artificial-Humanity/FerroLoop) suite. It can be used standalone as a Rust library (`fl-gate`) or mounted as a submodule inside `FerroLoop` (`fl vault` and `fl gateway`).

## Capabilities

1. **Zero-Knowledge Encrypted Vault (`vault`):**
   - `ring` AES-256-GCM encrypted store (`vault.enc`) with 256-bit master keys and 96-bit nonces.
   - AEAD Additional Authenticated Data (AAD) binds each secret's policy (`name`, `allowed_hosts`, `allowed_methods`, `allowed_agents`, `allow_private_network`, `lease_ttl_secs`, `expires_at`) directly into the authentication tag so policy tampering on disk is rejected cryptographically.
2. **Outbound HTTP Credential Proxy & Secret Scrubber (`proxy`):**
   - Substitutes `{{FERROGATE:KEY}}` placeholders at the outbound HTTP transport boundary while keeping raw credentials out of agent prompts and memory.
   - Enforces per-secret host, HTTP method, agent identity, lease expiration, and SSRF private-network guards.
   - Scrubs plaintext secrets, base64-encoded secrets, and standard vendor token prefixes (`sk-ant-`, `sk-proj-`, `ghp_`, `github_pat_`, `xoxb-`, etc.) from responses and error logs.
3. **Declarative REST/OpenAPI-to-MCP Gateway (`connector`, `mcp`):**
   - Loads `.toml` connector manifests and imports OpenAPI 3.x specs into governed MCP `tools/list` and `tools/call` endpoints with response field projection.
4. **Local & Tiered MCP Registry `v0.1` (`registry`, `adapter`):**
   - Serves MCP Registry `v0.1` compatible discovery endpoints (`/v0.1/servers`) and supports pluggable `SecretProviderAdapter`, `McpRelayAdapter`, `McpRegistryAdapter`, and `EdgeGuardAdapter` traits.
5. **Inbound & Outbound Event Bridge (`bridge`):**
   - Constant-time webhook secret verification (`verify_inbound_bridge_webhook`), sender allowlists, and routing to `wire:<persona>` or `connector:<id>`.

## Verification

```bash
cargo fmt --all -- --check
cargo clippy --all-targets --workspace -- -D warnings
cargo test --workspace
```

## License

Apache-2.0. See [LICENSE](LICENSE).
