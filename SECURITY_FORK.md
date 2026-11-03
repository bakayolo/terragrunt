# Shuffle Terragrunt security fork

This branch is based on official Terragrunt v1.2.1, commit
ed85b42a24b52494212b622cd45cb349cec53a3b. Its CLI implementation and fixtures
remain unchanged. The only runtime dependency change is go-jose v4.1.4 to
v4.1.6, recorded in go.mod/go.sum, to fix CVE-2026-116587 (excess CPU usage
when parsing crafted RSA keys inside JWKs).

This was the final fixable HIGH finding in Shuffle's Atlantis runtime image
after updating Terraform, removing unused tools, and patching the config
generator. Official Terragrunt v1.2.1 was still the latest published release
when this fork was prepared. Go 1.27.3 remains the upstream-pinned toolchain.

The maintained source is https://github.com/bakayolo/terragrunt on branch
bena/codex/security-dependencies. The Atlantis Dockerfile pins the tested
commit and source archive SHA-256, builds with -mod=readonly in a separate
native builder, and reports version v1.2.1-shuffle.1. It must not run go get
or make dependency changes while building the image. Only the binary and
license are copied into the runtime.

Validation includes the upstream IAM, AWS credential-helper, and version
unit suites; the complete go-jose package suite, including its security
regressions; native CLI smoke checks; and a Linux ARM64 binary scan under
the image's pinned Trivy HIGH/CRITICAL policy. Cloud integration suites are
not run by this fork's CI and receive no AWS credentials.

Return to an official release once it includes the go-jose fix, passes the
same scan, and preserves CLI behavior. Do not widen this fork's runtime
changes without a separate review.
