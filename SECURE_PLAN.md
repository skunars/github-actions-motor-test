# Secure public-runner architecture

## Runtime boundary
- Public deployment shell only; no production repository checkout.
- Production source is packaged as an encrypted payload before deployment.
- Decryption material is supplied at runtime through a GitHub Actions repository secret.
- Plaintext runtime files are placed only in the runner RAM-backed workspace and removed on exit.
- Checkpoint state is separate from source and contains only handoff metadata.

## Handoff
- Target active motor window: 5h45m.
- Reserve up to 5 minutes for checkpoint, commit, and next-run dispatch.
- One runner owns the motor at a time through a concurrency lock.
- The next run reads the previous checkpoint and continues from it.
- Test mode stops after 5 consecutive handoffs; production mode has no artificial test cap.

## Scanning model
- The old 15-minute scheduler is not the production motor loop.
- The production motor stays alive inside the long-running job and scans on a short interval selected per data source and API limit.
- Fast market data target: roughly 15-30 seconds. Slower enrichment and risk checks: roughly 1-5 minutes.
- Signal decisions use event/change detection so Telegram alerts fire when a candidate crosses the configured threshold instead of waiting for a fixed 15-minute boundary.

## Rollout
1. User adds the runtime secret.
2. Run the short secure handoff test.
3. Verify 5 consecutive state handoffs.
4. Connect one real motor only.
5. Observe real forward data before connecting additional motors.
6. Keep every motor in its own deployment shell, state, Telegram configuration, and workflow.
