# Release Process

Publishes `com.orbitronai:novaos-agent-sdk` to Maven Central via the Central
Publishing Portal. Unlike PyPI/npm, Maven Central has no OIDC "trusted publisher" —
auth is a token + GPG signing key stored as GitHub Actions secrets.

## One-time setup (do before first release)

1. Create an account at https://central.sonatype.com.
2. Verify the `com.orbitronai` namespace (DNS TXT record on orbitronai.com, or a
   verified GitHub org if using `io.github.orbitronai-repo` instead — pick one and
   update `groupId` in `pom.xml` to match).
3. Generate a Central Portal user token (Account → Generate User Token) → gives you
   a username/password pair.
4. Generate a GPG key for signing (`gpg --full-generate-key`), publish the public
   key to a keyserver (`gpg --keyserver keyserver.ubuntu.com --send-keys <KEYID>`).
5. Add these repo secrets (Settings → Secrets → Actions):
   - `CENTRAL_TOKEN_USERNAME`, `CENTRAL_TOKEN_PASSWORD` (from step 3)
   - `GPG_PRIVATE_KEY` (armored private key export: `gpg --export-secret-keys --armor <KEYID>`)
   - `GPG_PASSPHRASE`

## Release Checklist

1. Bump `version` in `pom.xml` (and `Version.VERSION` if kept in sync).
2. Commit, PR, merge to `main`.
3. Tag the merge commit: `git tag -a vX.Y.Z -m "Release vX.Y.Z" && git push origin vX.Y.Z`.
4. Create a GitHub Release from that tag — `.github/workflows/release.yml` builds,
   signs, and deploys to Maven Central automatically.
5. `autoPublish` is `false` in `pom.xml` — the deployment lands in a Central Portal
   staging repo; log in and manually publish it the first few times until you trust
   the pipeline, then flip `autoPublish` to `true`.
