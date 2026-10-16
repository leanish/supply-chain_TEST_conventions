# Expected outcomes — convention consumer

Baseline authored 2026-10-09 against the local rc.11 checkout, including its
Unreleased plugin-source filtering/verification behavior. Locations below name
seed files; line numbers naturally move after a tool edits them.

| Case | File/line | bump-it | secure-it | Gate |
|---|---|---|---|---|
| 1: pluginManagement | `settings.gradle.kts:7` | Routine plugin-marker update 0.3.1 -> highest eligible 0.3.x (a 0.x version's line is its first two numbers); a newer line, such as 0.6, gets its own major PR. Edit the settings declaration, not invented consumer dependency versions. | Old plugin/tool advisories are live; may require additional security PRs/floors. | Exact marker must resolve at target on the settings/buildscript classpath; changed injected modules still face age/advisory checks. |
| 1/5: injected libraries | build.gradle.kts plugin application; consumer tests | Dependencies absent from Gradle source coordinate evidence remain notes, never independent bump moves. A planned convention-plugin update may change them through the reference graph. | Scan injected modules. For a vulnerable plugin-declared module, search eligible source-named dependency parents first; without one, add an explicit because floor plus its record alongside the plugin's declaration. Do not edit a declaration the consumer sources do not hold. | Head must match the plan-applied Gradle reference. Added/removed/changed injected modules are accepted only as faithful reference fallout and still face advisory/age checks. |

Acceptance: capture base and updated `gradle-head` inventories. Require the plugin
marker to land at the plan target and check that any changed injected versions
occur only under that same planned plugin build. Tests pass using the plugin's
injected JUnit/AssertJ without manually declaring or pinning either library.
If the selected plugin target has identical injected versions, that run proves
notes/planning but not that changed injected versions match the plan-applied
reference; record the gap instead of claiming that path was exercised. A
deliberately unrelated Java change is outside a routine plan, so do not attach
formatting repairs to that PR.

Default-branch scan may initially FAIL due to old injected tools; capture the full
finding set. PR compare should PASS if changes add no findings and inherited ones
remain, then cooldown PASS for aged versions. Final scan passes only after all
unexcepted findings are fixed. A changed plugin dependency graph may cause an
explicit verification failure; do not suppress or reclassify it as a pass.

`VERIFY: PLUGIN` — Confirm plugin 0.3.1's marker/implementation metadata, POM upload
age >=7 days, availability, and baseline compatibility with Gradle 9.3.1/JDK 25.
The public plugin JAR/marker were present in a local cache; filesystem dates are
not publish dates. Identify an aged newer plugin whose injected JUnit/AssertJ
versions actually differ, and confirm its minimum Gradle/JDK. Preview may select
a still newer eligible target: use the selected target for acceptance.

`VERIFY: BUILD` — Run ./gradlew --no-daemon check in a normal networked session on
the selected target (including plugin formatting/checkstyle, coverage and mutation
tasks). Claude reported baseline ./gradlew check passes on daemon JDK 21 with
the main Java 25 toolchain. Local Gradle startup was blocked by the sandbox's
file-lock coordination socket; target builds remain unverified.

## Shared gate and action checks

| Case | File/line | bump-it | secure-it | Gate |
|---|---|---|---|---|
| Gate adoption | `.github/workflows/supply-chain.yml:15` | Own Action update may join routine/major according to release line; repin before trials. | Only if an advisory has a selected fix. | PR compare; default-branch scan on push to main and manual dispatch. No schedule (removed 2026-10-12) and no open-PR rescan (removed in gate 0.4.0). Require supply-chain and cooldown. |
| 8: checkout pin | `.github/workflows/ci.yml:16` | Routine: highest eligible v4 release and matching commit/comment; separate major for highest eligible newer line. | No fixed security expectation; live advisories decide. | Changed SHA must match its full release tag comment and aged release; otherwise FAIL. |
| 8: Java setup pin | `.github/workflows/ci.yml:19` | Routine v4 refresh and separate newer-major PR when eligible; preserve JDK 25 input. | Live advisories decide. | Same SHA/tag/age checks. |

Case 9: the release-age policy is `.github/supply-chain.json:2` (`releaseAgeDays: 7`).
No deliberately young fixture exists. For aged changed targets and a complete passing
comparison, cooldown should PASS. A rule-approved young security target can make
comparison PASS but cooldown FAIL (held); bump-it must not publish a held plan.
Routine and major exact targets, advisory counts and extra security units are live
registry outcomes, not fixed to the illustrative versions below. npm identity,
source or provenance breaks can block an otherwise safe version.

Gate trials use the same supply-chain commit for inventory and comparison.
The gate is pinned to supply-chain v0.6.0
(738ec358440bea0c85f6d5a5e59d2f9cd45667c6, 2026-10-16; v0.5.2 was
6b99f70cc8fddf35c03a29fb4c6b5ac29e795efc, 2026-10-15; v0.4.0 was
1c236638041cc479ac067b2a4b6143f0e6c2bace, 2026-10-11). It runs on pull requests,
pushes to main and manual dispatch. It has no schedule (removed 2026-10-12) and,
since v0.4.0, no open-PR rescan. The tools are at v0.6.0; tool-only releases don't
need a gate repin. Earlier trials used v0.3.0
(20711928aa3074093cdcc8080767c56b1223ae01) and the candidate commit 87b1e9e,
unreachable since its branch was merged.

`VERIFY: ACTIONS` — Confirm each exact SHA/tag pair and published release date via
GitHub (dereference annotated tags). Baseline checkout v4.1.1 is
b4ffde65f46336ab88eb53be808477a3936bae11 (2023 release). Baseline setup-java v4.2.1 is
99b8673ff64fbf99d8d325f52d9a5bdedb8483e9 (2024 release).
Choose live eligible routine and newer-major releases using their actual published
times. A moved tag, missing release or new advisory changes the expected candidate.

| Case | File/line | bump-it | secure-it | Gate |
|---|---|---|---|---|
| 6: old wrapper | `gradle/wrapper/gradle-wrapper.properties:3` | Root wrapper 9.3.1 -> highest aged stable non-broken eligible Gradle, in the routine PR. Replace all four tool-generated files; gradlew remains executable; selected distribution/jar checksums verified. | No Gradle wrapper remediation mechanism. | Wrapper is not an external module in the gate; bump-it performs wrapper-specific verification. Judge any resulting resolved-module changes normally. |

Claude verified both baseline wrapper JARs against the official Gradle 9.3.1
checksum: b3a875ddc1f044746e1b1a55f645584505f4a10438c1afea9f15e92a7c42ec13.
Gradle 9.3.1 was built 2026-01-29 and is aged. The wrapper files were generated
directly by WrapperGenerator from the cached 9.3.1 distribution.

`VERIFY: WRAPPER` — Select and verify the live wrapper target with the tool; do
not invent a fixed latest version. When preparing adoption, optionally add the
official 9.3.1 binary distribution checksum.
A wrapper-only move is not a planned plugin move; if embedded dependencies
change without a planned plugin move and verification rejects them, record a
regression/failure rather than adding dependency declarations to bypass it.


## Run acceptance and evidence (cases 8–9 and deferred majors)

EXPECTED.md is the single source of expectations. README.md contains setup and
entry commands; VALIDATION.md contains observed evidence.
Exact live targets, advisory counts, identities and release dates depend on the
registry snapshot. A missing or too-young eligible candidate is timing-dependent,
not proof that the fixture or tool failed. No young release is fabricated.

Run secure-it and bump-it independently against the same seeded main revision;
capture evidence before merging either tool's PRs. Do not merge parent updates
before testing transitive security fixes. Preserve the seed revision externally
for repeat trials; after merges, do not expect the same candidates/findings.
Later runs on an unchanged main may review already-open PRs rather than create new ones.

bump-it's default maximum is **three new major PRs per run**, in addition to the
routine unit. An eligible major can be deferred behind higher-priority packages,
including Actions. An absent java-conventions plugin, checkout, setup-java or
other major PR is not a bug solely because it is absent.
Check the deployed limit and each unit's status/priority; distinguish deferred,
already-open, held, failed, no eligible candidate and notes-only dependencies.
Capture later runs (or a deliberately configured larger limit) to exercise every
eligible major. Never mark a deferred unit as a successful update.

For every run, retain the tool version/commit, configuration, baseline/head IDs,
complete tool record **including units, waiting, gaps, notes and errors**, candidate
versions, plan payloads, PR title/body/diff, and verification/build logs. Include
failed units' attempted plans and diffs even when no PR exists. If the runner does
not retain a failed plan/diff, report that evidence gap rather than inventing a
root cause. Preserve inventories for both sides, resolved and declared versions,
findings fixed/inherited/added, the full compare report, cooldown verdict/held
versions and CI results. Store scratch evidence in ignored .validation/ or outside
the repository; no generated diagnostics belong in a remediation PR.

Default-branch scan must fail on the seeded unexcepted advisories. A successful
fix PR can pass compare with other findings inherited; this does not mean main
is clean. CLI exit zero, creation of a PR, or a passing smoke test alone is not
acceptance. Judge every changed resolved artifact for advisories, age and identity.
Missing registry data/inventory coverage is incomplete, never a clean pass.

`VERIFY: RUN-EVIDENCE` — Confirm the deployed maxNewMajorsPerRun value and retain
all fields and failed-unit artifacts above in the next candidate-pass report. Reconcile
all cases against units, not only the published PR list.

## Gradle verification (current since supply-chain 0.3.0)

| Behavior | Expected result |
|---|---|
| Declarations after resolution | Lazy/default plugin declarations are captured consistently after all configurations resolve. Missing or unresolved coverage remains incomplete/failing. |
| Planned plugin graph changes | Base with the plan applied by Gradle supplies the reference. Head declarations and resolved modules must match it configuration by configuration, including additions/removals and versionless declarations. Every changed/added module still faces the gate. |
| Security plugin move plus floor | Both planned changes are applied to the same reference. Accept faithful combined fallout; reject differences from that reference. The obsolete version-swap proof is not the acceptance mechanism. |
| Plugin-declared vulnerable dependency | When no eligible source-named declaration can carry its fix, secure-it adds an explicit because dependency and matching security-floor record next to the plugin declaration. Report the unsuccessful parent search. Do not edit an injected declaration as if the consumer sources held it. |
| Unsupported plugin defaults/strict versions | A floor that changes defaults beyond the reference contract or cannot resolve against a strict declaration remains a failure. Do not suppress it. |

A plugin marker on the settings/buildscript classpath does not, by itself,
establish a source-named dependency root reaching an injected module in every
affected project configuration.

Ignored Kotlin diagnostics do not remove already tracked files. Preserve complete
base/reference/head inventories, plans, findings, cooldown and build evidence.
A passing dependency test alone does not prove remediation or reference equality.
