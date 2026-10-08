# XZ Utils Supply-Chain Integrity Lab

> **Safe classroom simulation:** This repository contains no XZ Utils source code, exploit, backdoor, credential theft, remote execution, or malicious payload.

## Background

In March 2024, the XZ Utils backdoor (CVE-2024-3094) showed that open-source software can be compromised through its supply chain rather than through an ordinary coding bug. The attacker gradually gained trust in the project, obtained release influence, and introduced malicious build logic into distributed release artifacts. The incident was discovered before the affected versions reached broad stable deployment.

One important lesson from the incident is that reviewing a public source repository is not always enough. A release archive distributed to users must also be checked against the reviewed source and expected build output.

## What this repository demonstrates

This is a deliberately small, harmless simulation of **source-to-artifact divergence**:

- `src/app.txt` is the clean, reviewed source file.
- `dist/clean-release.tar.gz` is the expected clean release archive.
- `dist/simulated-release.tar.gz` contains the same source plus `release-only-marker.txt`.
- The marker is plain explanatory text only; it is not executable and does not affect the system.

The repository also includes a normal, harmless collaborator pull request in `docs/contributor-notes.md`. The purpose is to show that contributors and merged pull requests are ordinary open-source activity; the security question is whether the artifact finally released to users matches what was reviewed.

## How to verify the simulated release

From the repository root, list both archive contents and remove the archive-specific `./` prefix before comparing them:

```bash
tar -tzf dist/clean-release.tar.gz | sort > /tmp/clean-files.txt
tar -tzf dist/simulated-release.tar.gz | sed 's#^\\./##' | sed '/^$/d' | sort > /tmp/simulated-files.txt
diff -u /tmp/clean-files.txt /tmp/simulated-files.txt
```

The expected result contains the following added file:

```text
+release-only-marker.txt
```

This difference is the intended detection result. In a real release process, an unexplained mismatch should block publication until it has been investigated and approved.

## Relation to the real XZ case

The real XZ incident was far more sophisticated than this lab. It involved long-term social engineering, maintainer-trust compromise, build and release-process manipulation, and narrowly conditioned runtime behaviour. This repository does **not** reproduce any of those harmful mechanisms. It demonstrates only one defensive principle: independently compare the distributed artifact with the expected source or build output.

## Suggested controls

- Reproducible builds
- Signed release provenance and release manifests
- Independent source-to-artifact verification
- Multi-maintainer review and release approval
- Investigation of unexpected build, performance, or runtime anomalies

## Educational scope

This repository was created for a cybersecurity case-study submission. It is intentionally non-malicious and should be used only to explain software supply-chain integrity and defensive verification.
