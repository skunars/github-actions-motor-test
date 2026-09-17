# Secure runtime test v2

This test repository is the isolated proving ground for the public-runner motor architecture.

## Security model
- The repository contains only encrypted motor payloads; plaintext motor source is not stored here.
- The decryption private key is supplied only through the GitHub Actions secret `MOTOR_PRIVATE_KEY`.
- The runner never clones or reads a production source repository.
- Decrypted files are extracted only into `/dev/shm`, a RAM-backed filesystem on the Linux runner, and are removed when the job exits.
- Persistent handoff state contains metadata only, not source code.
- The assistant can continue to maintain the source repositories separately and publish encrypted runtime payloads without needing the private decryption key.

The test payload uses hybrid RSA-OAEP-SHA256 + AES-256-GCM encryption with authenticated data.
