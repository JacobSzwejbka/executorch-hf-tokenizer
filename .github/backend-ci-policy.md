# Backend CI coverage

QNN, Arm (including VGF), XNNPACK and Vulkan use their full configured flow
matrices for backend changes and shared export/runtime/test/build/dependency
changes. The reduced matrix is reserved for unrelated PRs and unsampled main
pushes. Main sampling uses the existing CI run decision without changing it.

| Event | Matrix |
| --- | --- |
| Relevant PR or main-push change | Full |
| Unrelated PR | Reduced |
| Unrelated sampled main push | Full |
| Unrelated unsampled main push | Reduced |
| Schedule, release push, manual or tag run | Full |
| Unknown/unavailable change information | Full |

The policy obtains complete Git diffs, including deleted and renamed paths,
rather than relying on a truncated API file list. Existing callers that do not
provide a reduced matrix continue to use their full flow list.

## Publication qualification

`Backend Qualification` runs only full matrices and is tied to its workflow
commit. It runs on nightly/release source branches, release/binary tags, manual
dispatch, and dedicated `ciflow/backend/*` test tags. It does not run on or alter
viable/strict promotion tags or rules.

The test-infra companion change checks this exact-commit result before binary
publication, stamps source provenance, and validates that provenance during
promotion and PyPI staging. Versioned AAR and Maven uploads also wait for
qualification, resolving their source ref once and building that immutable SHA.
Unversioned AAR snapshots retain their existing behavior.

Deploy this workflow on the source branches before enabling the shared upload
guard. Existing release candidates without provenance must be rebuilt and
restaged. Both diffs are required for the publication barrier; the backend
cadence change itself is independent of the shared guard.
