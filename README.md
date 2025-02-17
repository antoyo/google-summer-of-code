# Rust project ideas
This page contains a list of ideas for various projects that could help improve
the Rust Project and potentially also the wider Rust community.

These project ideas can be used as inspiration for various OSS contribution programs,
such as [Google Summer of Code](https://summerofcode.withgoogle.com/) or [OSPP](https://summer-ospp.ac.cn/).

This document contains ideas that should still be actual and that were not yet completed. Here you can also find an archive of older projects from past GSoC events:

- Past Google Summer of Code projects
  - [2024](gsoc/past/2024.md)

We invite contributors that would like to participate in projects such as GSoC or that would just want to find a Rust project that they would like to work on to examine the project list and use it as an inspiration. Another source of inspiration can be the [Rust Project Goals](https://rust-lang.github.io/rust-project-goals/index.html), particularly the orphaned goals.

If you would like to participate in GSoC, please read [this](gsoc/README.md).
If you would like to discuss projects ideas or anything related to them, you can do so on our [Zulip](https://rust-lang.zulipchat.com/).

We use the GSoC project size parameters for estimating the expected time complexity of the project ideas. The individual project sizes have the following expected amounts of hours:
- Small: 90 hours
- Medium: 175 hours
- Large: 350 hours

## Index
- **Rust Compiler**
    - [C codegen backend for rustc](#C-codegen-backend-for-rustc)
    - [Extend annotate-snippets with features required by rustc](#Extend-annotate-snippets-with-features-required-by-rustc)
    - [Reproducible builds](#reproducible-builds)
    - [Bootstrap of rustc with rustc_codegen_gcc](#Bootstrap-of-rustc-with-rustc_codegen_gcc)
- **Infrastructure**
    - [Implement merge functionality in bors](#implement-merge-functionality-in-bors)
    - [Improve bootstrap](#Improve-bootstrap)
- **Cargo**
    - [Prototype an alternative architecture for `cargo fix`](#prototype-an-alternative-architecture-for-cargo-fix)
    - [Prototype Cargo plumbing commands](#prototype-cargo-plumbing-commands)
    - [Move cargo shell completions to Rust](#move-cargo-shell-completions-to-Rust)
    - [Build script delegation](#build-script-delegation)
- **Rustfmt**
    - [Improve rustfmt infrastructure and automation](#improve-rustfmt-infrastructure-and-automation)
- **Crate ecosystem**
    - [Modernize the libc crate](#Modernize-the-libc-crate)
    - [Add more lints to `cargo-semver-checks`](#add-more-lints-to-cargo-semver-checks)
    - [Enable witness generation in `cargo-semver-checks`](#enable-witness-generation-in-cargo-semver-checks)
    - [Implement a cryptographic algorithm in RustCrypto](#implement-a-cryptographic-algorithm-in-rustcrypto)
    - [Wild linker with test suites from other linkers](#wild-linker-with-test-suites-from-other-linkers)

# Project ideas
The list of ideas is divided into several categories.

## Rust Compiler

### C codegen backend for `rustc`

**Description**

`rustc` currently has three in-tree codegen backends: LLVM (the default), Cranelift, and GCC.
These live at <https://github.com/rust-lang/rust/tree/master/compiler>, as `rustc_codegen_*` crates.

The goal of this project is to add a new experimental `rustc_codegen_c` backend that could turn Rust's internal
representations into `C` code (i.e. transpile) and optionally invoke a `C` compiler to build it. This will allow Rust
to use benefits of existing `C` compilers (better platform support, optimizations) in situations where the existing backends
cannot be used.

**Expected result**

The minimum viable product is to turn `rustc` data structures that represent a Rust program into `C` code, and write the
output to the location specified by `--out-dir`. This involves figuring out how to produce buildable `C` code from the
inputs provided by `rustc_codegen_ssa::traits::CodegenBackend`.

A second step is to have `rustc` invoke a `C` compiler on these produced files. This should be designed in a pluggable way,
such that any `C` compiler can be dropped in.

**Desirable skills**

Knowledge of Rust and `C`, basic familiarity with compiler functionality.

**Project size**

Large.

**Difficulty**

Hard.

**Mentor**
- Trevor Gross ([GitHub](https://github.com/tgross35), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/532317-Trevor-Gross))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20C.20codegen.20backend.20for.20.60rustc.60)
- [Compiler team](https://rust-lang.zulipchat.com/#narrow/stream/131828-t-compiler)
- [Previous discussion about this topic](https://rust-lang.zulipchat.com/#narrow/stream/122651-general/topic/rustc_codegen_c)

### Extend `annotate-snippets` with features required by rustc

**Description**

`rustc` currently has incomplete support for using [`annotate-snippets`](https://github.com/rust-lang/annotate-snippets-rs/)
to emit errors, but it doesn't support all the features that `rustc`'s built-in diagnostic rendering does. The goal
of this project is to execute the `rustc` test suite using `annotate-snippets`, identify missing features or bugs,
fix those, and repeat until at feature-parity.

**Expected result**

More of the `rustc` test suite passes with `annotate-snippets`.

**Desirable skills**

Knowledge of Rust.

**Project size**

Medium.

**Difficulty**

Medium or hard.

**Mentor**
- David Wood ([GitHub](https://github.com/davidtwco), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/116107-davidtwco))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20extend.20annotate-snippets)
- [Compiler team](https://rust-lang.zulipchat.com/#narrow/stream/131828-t-compiler)

### Reproducible builds

**Description**

Recent OSS attacks such as the [XZ backdoor](https://en.wikipedia.org/wiki/XZ_Utils_backdoor)
have shown the importance of having reproducible builds.

Currently, the Rust toolchain distributed to Rust developers is not very reproducible.
Our source code archives should be reproducible as of [this pull request](https://github.com/rust-lang/rust/pull/123246),
however making the actual binary artifacts reproducible is a much more difficult effort.

The goal of this project is to investigate what exactly makes Rust builds not reproducible,
and try to resolve as many such issues as possible.

While the main motivation is to make the Rust toolchain (compiler, standard library, etc.) releases
reproducible, any improvements on this front should benefit the reproducibility of all Rust programs.

**Expected result**

Rust builds are more reproducible, ideally the Rust toolchain can be compiled in a reproducible manner.

**Desirable skills**

Knowledge of Rust and ideally also build systems.

**Project size**

Medium.

**Difficulty**

Large.

**Mentor**
- Jakub Beránek ([GitHub](https://github.com/kobzol), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/266526-Jakub-Ber%C3%A1nek))

**Related links**
- [Prior art in Go](https://go.dev/blog/rebuild)

### Bootstrap of rustc with `rustc_codegen_gcc`

**Description**

[`rustc_codegen_gcc`](https://github.com/rust-lang/rustc_codegen_gcc) [used to be able to compile `rustc`](https://blog.antoyo.xyz/rustc_codegen_gcc-progress-report-10) and use the resulting compiler to successfully compile a `Hello, World!` program.
While it can still compile a [stage 2](https://rustc-dev-guide.rust-lang.org/building/bootstrapping/what-bootstrapping-does.html#stage-2-the-truly-current-compiler) `rustc`, the resulting compiler cannot compile the standard library anymore.

The goal of this project would be to fix in `rustc_codegen_gcc` any issue preventing the resulting compiler to compile a `Hello, World!` program and the standard library.
Those issues are not known, so the participant would need to attempt to do a bootstrap and investigate the issues that arises.

If time allows, an optional additional goal could be to be able to do a full bootstrap of `rustc` with `rustc_codegen_gcc`, meaning fixing even more issues to achieve this result.

**Expected result**

A `rustc_codegen_gcc` that can compile a stage 2 `rustc` where the resulting compiler can compile a `Hello, World!` program using the standard library (also compiled by that resulting compiler).

An optional additional goal would be: a `rustc_codegen_gcc` that can do a full bootstrap of the Rust compiler. This means getting a stage 3 `rustc` that is identical to stage 2.

**Desirable skills**

Good debugging ability. Basic knowledge of:

 * Intel x86-64 assembly (for debugging purposes).
 * `rustc` internals, especially the [codegen part](https://rustc-dev-guide.rust-lang.org/backend/backend-agnostic.html).
 * [`libgccjit`](https://gcc.gnu.org/onlinedocs/jit/) and [GCC internals](https://gcc.gnu.org/onlinedocs/gccint/).

**Project size**

Medium-Large depending on the chosen scope.

**Difficulty**

Hard.

**Mentor**
- Antoni Boucher ([GitHub](https://github.com/antoyo), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/404242-antoyo))

**Zulip streams**
- Idea discussion
- [rustc_codegen_gcc](https://rust-lang.zulipchat.com/#narrow/channel/386786-rustc-codegen-gcc/)

## Infrastructure

### Implement merge functionality in bors

**Description**

Various Rust repositories under the [rust-lang](https://github.com/rust-lang) organization use a merge queue bot (bors) for testing and merging pull requests. Currently, we use a legacy implementation called [homu](https://github.com/rust-lang/homu), which is quite buggy and very difficult to maintain, so we would like to get rid of it. We have started the implementation of a new bot called simply [bors](https://github.com/rust-lang/bors), which should eventually become the primary method for merging pull requests in the [rust-lang/rust](https://github.com/rust-lang/rust) repository.

The bors bot is a GitHub app that responds to user commands and performs various operations on a GitHub repository. Primarily, it creates merge commits and reports test workflow results for them. It can currently perform so-called "try builds", which can be started manually by users on a given PR to check if a subset of CI passed on the PR. However, the most important functionality, actually merging pull requests into the main branch, has not been implemented yet.

**Expected result**

bors can be used to perform pull request merges, including "rollups". In an ideal case, bors will be already usable on the `rust-lang/rust` repository.

**Desirable skills**

Intermediate knowledge of Rust. Familiarity with GitHub APIs is a bonus.

**Project size**

Medium.

**Difficulty**

Medium.

**Mentors**
- Jakub Beránek ([GitHub](https://github.com/kobzol), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/266526-Jakub-Ber%C3%A1nek))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20improve.20infrastructure.20automation.20tools)
- [Infra team](https://rust-lang.zulipchat.com/#narrow/stream/242791-t-infra)

### Improve bootstrap

**Description**

The Rust compiler it bootstrapped using a complex set of scripts and programs generally called just `bootstrap`.
This tooling is constantly changing, and it has accrued a lot of technical debt. It could be improved in many areas, for example:

- Design a new testing infrastructure and write more tests.
- Write documentation.
- Remove unnecessary hacks.

**Expected result**

The `bootstrap` tooling will have less technical debt, more tests, and better documentation.

**Desirable skills**

Intermediate knowledge of Rust. Knowledge of the Rust compiler bootstrap process is welcome, but not required.

**Project size**

Medium or large.

**Difficulty**

Medium.

**Mentor**
- AlbertLarsan68 ([GitHub](https://github.com/albertlarsan68), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/510016-Albert-Larsan))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20improve.20bootstrap)
- [Bootstreap team](https://rust-lang.zulipchat.com/#narrow/stream/326414-t-infra.2Fbootstrap)

## Cargo

### Prototype an alternative architecture for `cargo fix`

**Description**

Some compiler errors know how to fix the problem and `cargo fix` is the command for applying those fixes.
Currently, `cargo fix` calls into the APIs that implement `cargo check` with
`cargo` in a way that allows getting the json messages from rustc and apply
them to workspace members.
To avoid problems with conflicting or redundant fixes, `cargo fix` runs `rustc` for workspace members in serial.
As one fix might lead to another, `cargo fix` runs `rustc` for each workspace member in a loop until a fixed point is reached.
This can be very slow for large workspaces.

We want to explore an alternative architecture where `cargo fix` runs the
`cargo check` command in a loop,
processing the json messages,
until a fixed point is reached.

Benefits
- Always runs in parallel
- May make it easier to extend the behavior, like with an interactive mode

Downsides
- Might have issues with files owned by multiple packages or even multiple build targets

This can leverage existing CLI and crate APIs of Cargo and can be developed as a third-party command.

See [cargo#13214](https://github.com/rust-lang/cargo/issues/13214) for more details.

**Expected result**

- A third-party command as described above
- A comparison of performance across representative crates
- An analysis of corner the behavior with the described corner cases

**Desirable skills**

Intermediate knowledge of Rust.

**Project size**

Medium

**Difficulty**

Medium.

**Mentor**
- Ed Page ([GitHub](https://github.com/epage), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/424212-Ed-Page))

**Zulip streams**
- Idea discussion
- [Cargo team](https://rust-lang.zulipchat.com/#narrow/stream/246057-t-cargo)

### Prototype Cargo plumbing commands

**Description**

Cargo is a high-level, opinionated command.
Instead of trying to directly support every use case,
we want to explore exposing the building blocks of the high-level commands as
"plumbing" commands that people can use programmatically to compose together to
create custom Cargo behavior.

This can be prototyped outside of the Cargo code base, using the Cargo API.

See the [Project Goal](https://rust-lang.github.io/rust-project-goals/2025h1/cargo-plumbing.html) for more details.

**Expected result**

Ideal: a performant `cargo porcelain check` command that calls out to
individual `cargo plumbing <name>` commands to implement its functionality.

Depending on the size the particpant takes on and their experience,
this may be out of reach.
The priorities are:
1. A shell of `cargo porcelain check`
2. Individual commands until `cargo porcelain check` is functional
3. Performance

**Desirable skills**

Intermediate knowledge of Rust.

**Project size**

Scaleable

**Difficulty**

Medium.

**Mentor**
- Ed Page ([GitHub](https://github.com/epage), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/424212-Ed-Page))

**Zulip streams**
- Idea discussion
- [Cargo team](https://rust-lang.zulipchat.com/#narrow/stream/246057-t-cargo)

### Move cargo shell completions to Rust

**Description**

Cargo maintains Bash and Zsh completions, but they are duplicated and limited in features.

A previous GSoC participant added unstable support for completions in Cargo itself,
so we can have a single implementation with per-shell skins ([rust-lang/cargo#6645](https://github.com/rust-lang/cargo/issues/6645)).
- [**Final project report**](https://hackmd.io/@PthRWaPvSmS_2Yu_GLbGpg/Hk-ficKpC)
- [GSoC project annotation](https://summerofcode.withgoogle.com/programs/2024/projects/jjnidpgn)
- [Project discussion on Zulip](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Project.3A.20Move.20cargo.20shell.20completions.20to.20Rust)

There are many more arguments that need custom completers as well as polish in the completion system itself before this can be stabilized.

See
- [Clap's tracking issue](https://github.com/clap-rs/clap/issues/3166)
- [Cargo's tracking issue](https://github.com/rust-lang/cargo/issues/14520)

**Expected result**

Ideal:
- A report to clap maintainers on the state of the unstable completions and why its ready for stabilization
- A report to cargo maintainers on the state of the unstable completions and why its ready for stabilization

**Desirable skills**

Intermediate knowledge of Rust. Shell familiarity is a bonus.

**Project size**

Medium.

**Difficulty**

Medium.

**Mentor**
- Idea discussion
- Ed Page ([GitHub](https://github.com/epage), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/424212-Ed-Page))

### Build script delegation

**Description**

When developers need to extend how Cargo builds their package,
they can write a [build script](https://doc.rust-lang.org/cargo/reference/build-scripts.html).
This gives users quite a bit of flexibility but
- Allows running arbitrary code on the users system, requiring extra auditing
- Needs to be compiled and run before the relevant package can be built
- They are all-or-nothing, requiring users to do extra checks to avoid running expensive logic
- They run counter to the principles of third-party build tools that try to mimic Cargo

A developer could make their build script a thin wrapper around a library
(e.g. [shadow-rs](https://crates.io/crates/shadow-rs))
but a build script still exists to be audited (even if its small) and each individual wrapper build script must be compiled and linked.
This is still opaque to third-party build tools.

Leveraging an unstable feature,
[artifact dependencies](https://doc.rust-lang.org/nightly/cargo/reference/unstable.html#artifact-dependencies),
we could allow a developer to say that one or more dependencies should be run as build scripts, passing parameters to them.

This project would add unstable support for build script delegation that can
then be evaluated for proposing as an RFC for approval.

See [the proposal](https://github.com/rust-lang/cargo/issues/14903#issuecomment-2523803041) for more details.

**Expected result**

Milestones
1. An unstable feature for multiple build scripts
2. An unstable feature for passing parameters to build scripts from `Cargo.toml`, built on the above
3. An unstable feature for build script delegation, built on the above two

Bonus: preparation work to stabilize a subset of artifact dependencies.

**Desirable skills**

Intermediate knowledge of Rust, especially experience with writing build scripts.

**Project size**

Large.

**Difficulty**

Medium.

**Mentor**
- Idea discussion
- Ed Page ([GitHub](https://github.com/epage), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/424212-Ed-Page))

## Rustfmt

### Improve rustfmt infrastructure and automation

**Description**

Rustfmt is the code formatter for Rust code. Currently, to ensure stability, rustfmt uses unit tests that ensure a source file do not get reformatted unexpectedly. Additionally, there is a tool (currently a shell script) called [`diffcheck`](https://github.com/rust-lang/rustfmt/blob/master/ci/check_diff.sh#L187-L216) that gets run to check potentially unexpected changes across different large codebases. We would like to improve our tooling around that, namely improving the diffcheck job to include more crates, improve reporting (with HTML output, like a mini [crater](https://crater-reports.s3.amazonaws.com/pr-114776-1/index.html), which runs compiler changes against all Rust crates published to crates.io), potentially rewriting the job in Rust, and reliability.

Rustfmt currently has a versioning system that gates unstable changes behind `Version=Two`, and the diffcheck job may be less reliable to report changes to `Version=One` when changes to unstable formatting are introduced. We'd like to see this story improved to make our test system more robust.

**Expected result**

A more robust and reliable infrastructure for testing the rustfmt codebase, potentially rewritten in Rust, with HTML output.

**Desirable skills**

Intermediate knowledge of Rust. Knowledge of CI and automation welcomed.

**Project size**

Small or medium, depending on the scale proposed.

**Difficulty**

Small or medium.

**Mentor**
- Yacin Tmimi ([GitHub](https://github.com/ytmimi), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/441976-Yacin-Tmimi))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20improve.20rustfmt.20infrastructure.20and.20automation)

**Related Links**
- [Previous discussion around the idea](https://rust-lang.zulipchat.com/#narrow/stream/357797-t-rustfmt/topic/meeting.202023-01-08/near/411836200)

## Crate ecosystem

### Modernize the libc crate

**Description**

The [libc](https://github.com/rust-lang/libc) crate is one of the oldest crates of the Rust ecosystem, long predating
Rust 1.0. Additionally, it is one of the most widely used crates in the ecosystem (#4 most downloaded on crates.io).
This combinations means that the current version of the libc crate (`v0.2`) is very conservative with breaking changes and
remains backwards-compatible with all Rust compilers since Rust 1.13 (released in 2016).

The language has evolved a lot since Rust 1.13, and we would like to make use of these features in libc. The main one is
support for `union` types to proper expose C unions.

At the same time there, is a backlog of desired breaking changes tracked in [this issue](https://github.com/rust-lang/libc/issues/3248). Some of these come from
the evolution of the underlying platforms, some come from a desire to use newer language features, while others are
simple mistakes that we cannot correct without breaking existing code.

The goal of this project is to prepare and release the next major version of the libc crate.

**Expected result**

The libc crate is cleaned up and modernized, and released as version 0.3.

**Desirable skills**

Intermediate knowledge of Rust.

**Project size**

Medium.

**Difficulty**

Medium.

**Mentor**
- Amanieu d'Antras ([GitHub](https://github.com/Amanieu), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/143274-Amanieu))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20modernize.20the.20libc.20crate)
- [Library team](https://rust-lang.zulipchat.com/#narrow/stream/219381-t-libs)

### Add more lints to `cargo-semver-checks`

**Description**

[`cargo-semver-checks`](https://github.com/obi1kenobi/cargo-semver-checks) is a linter for semantic versioning. It ensures
that Rust crates adhere to semantic versioning by looking for breaking changes in APIs.

It can currently catch ~120 different kinds of breaking changes, meaning there are hundreds of kinds of breaking changes it
still cannot catch! The goal of this project is to extend its abilities, so that it can catch and prevent more breaking changes, by:
- adding more lints, which are expressed as queries over a database-like schema ([playground](https://play.predr.ag/rustdoc))
- extending the schema, so more Rust functionality is made available for linting

**Expected result**

`cargo-semver-checks` will contain new lints, together with test cases that both ensure the lint triggers when expected
and does not trigger in situations where it shouldn't (AKA false-positives).

**Desirable skills**

Intermediate knowledge of Rust. Familiarity with databases, query engines, or query language design is welcome but
not required.

**Project size**

Medium or large, depends on how many lints will be implemented. The more lints, the better!

**Difficulty**

Medium to high, depends on the choice of implemented lints or schema extensions.

**Mentor**
- Predrag Gruevski ([GitHub](https://github.com/obi1kenobi/), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/474284-Predrag-Gruevski-(he-him)))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20add.20more.20lints.20to.20.60cargo-semver-checks.60)

**Related Links**
- [Playground where you can try querying Rust data](https://play.predr.ag/rustdoc)
- [GitHub issues describing not-yet-implemented lints](https://github.com/obi1kenobi/cargo-semver-checks/issues?q=is%3Aissue+is%3Aopen+label%3AE-mentor+label%3AA-lint+)
- [Opportunities to add new schema, enabling new lints](https://github.com/obi1kenobi/cargo-semver-checks/issues/241)
- [Query engine adapter](https://github.com/obi1kenobi/trustfall-rustdoc-adapter)

### Enable witness generation in `cargo-semver-checks`

**Description**

When `cargo-semver-checks` reports a breaking change, it in principle has seen enough information for the breakage to be reproduced with an example program: a *witness* program.
Witness programs are valuable as they confirm that the suspected breakage did indeed happen, and is not a false-positive.

**Expected result**

Automatic witness generation is something we've explored, but we've only scratched the surface at implementing it so far.
The goal of this project would be to take it the rest of the way: enable `cargo-semver-checks` to (with the user's opt-in) generate witness programs for each lint, verify that they indeed demonstrate the detected breakage, and inform the user appropriately of the breakage and the manner in which it was confirmed.
If a witness program *fails* to reproduce breakage flagged by one of our lints, we've found a bug — the tool should then prepare a diagnostic info packet and offer to help the user open an auto-populated GitHub issue.

**Stretch goal:** having implemented witness generation, run another study of SemVer compliance in the Rust ecosystem, similar to [the study we completed in 2023](https://predr.ag/blog/semver-violations-are-common-better-tooling-is-the-answer/). The new study would cover many more kinds of breaking changes, since `cargo-semver-checks` today has 2.5x times more lints than it did back then. It would also reveal any new false-positive issues, crashes, or other regressions that may have snuck into the tool in the intervening years.

**Desirable skills**

Intermediate knowledge of Rust. Interest in building dev tools, and empathy for user needs so we can design the best possible user experience.
Familiarity with databases, query engines, or programming language design is welcome but not required.

**Project size**

Large

**Difficulty**

Medium

**Mentor**
- Predrag Gruevski ([GitHub](https://github.com/obi1kenobi/), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/474284-Predrag-Gruevski-(he-him)))

**Related Links**
- [Playground where you can try querying Rust data](https://play.predr.ag/rustdoc)
- [Use of witness programs to verify breaking change lints](https://predr.ag/blog/semver-violations-are-common-better-tooling-is-the-answer/#automated-validation-via-witnesses)

### Implement a cryptographic algorithm in RustCrypto

**Description**

The [RustCrypto Project](https://github.com/RustCrypto) maintains pure Rust implementations of
hundreds of cryptographic algorithms, organized into repositories by algorithm type, e.g.
block ciphers, stream ciphers, hash functions.

Each of these repositories contains a tracking issue identifying specific algorithms which currently
lack an implementation, some of which are linked in the "Related Links" section below. Interested
students can look through these issues and identify an algorithm which is currently unimplemented
which sounds interesting to them, and then implement it as part of this project.

Alternatively, instead of implementing a new algorithm from scratch, a student could potentially
choose to implement some significant unit of functionality in an existing algorithm implementation
with an open associated issue on our GitHub trackers, an example of which might be
[implementing hardware acceleration support for our "bignum" library](https://github.com/RustCrypto/crypto-bigint/issues/1).

**Expected result**

One or more Rust crates/libraries containing a new implementation of a cryptographic algorithm implemented in pure Rust.

**Desirable skills**

Intermediate knowledge of Rust.

A background in mathematics, and some prior knowledge of cryptography, is helpful but not required,
and we can provide guidance and review to ensure code is correct and securely implemented.

**Project size**

Will vary depending on the algorithm/project selected, but ideally small.

Note that while the code size of the deliverable may not be significant, due to the nature of
cryptographic work it will typically still involve significant effort and iteration to deliver an
implementation which is correct and secure.

**Difficulty**

Will also vary depending on the algorithm/project selected, but expected difficulty is medium/hard, as noted above.

**Mentor**
- Tony Arcieri ([GitHub](https://github.com/tarcieri/), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/132721-Tony-Arcieri))

**Zulip streams**
- [Idea discussion](https://rust-lang.zulipchat.com/#narrow/stream/421156-gsoc/topic/Idea.3A.20implement.20a.20cryptographic.20algorithm.20in.20RustCrypto)

**Related Links**
- [Potential AEAD cipher projects](https://github.com/RustCrypto/AEADs/issues/1)
- [Potential block cipher projects](https://github.com/RustCrypto/block-ciphers/issues/1)
- [Potential elliptic curve projects](https://github.com/RustCrypto/elliptic-curves/issues/114)
- [Potential hash function projects](https://github.com/RustCrypto/hashes/issues/1)
- [Potential signature algorithm projects](https://github.com/RustCrypto/signatures/issues/8)
- [Potential stream cipher projects](https://github.com/RustCrypto/stream-ciphers/issues/219)
- [Potential SSH-related projects](https://github.com/RustCrypto/SSH/issues/2)

### Wild linker with test suites from other linkers

**Description**

The Wild linker is a project to build a very fast linker in Rust that has incremental linking and
hot reload capabilities.

It currently works well enough to link itself, the Rust compiler, clang (provided you use the right
compiler flags) and a few other things. However, there are various features and combinations of
flags that don’t yet work correctly. Furthermore, we have a pretty incomplete picture of what we
don’t support.

The proposed project is to run the test suite of other linkers with Wild as the linker being tested,
then for each failure, determine what the problem is. It’s expected that many failures will have the
same root cause.

**Expected result**

Write a program, ideally in Rust, that runs the test suite of some other linker. Mold’s test suite
is pretty easy to run with Wild, so that’s probably a good default choice. The Rust program should
emit a CSV file with one row per test, whether the test passes or fails and if it fails, an attempt
to identify the cause based on errors / warnings emitted by Wild.

For tests where Wild doesn’t currently emit any error or warning that is related to the cause of the
test failure, attempt to make it do so. Some of the tests might fail for reasons that are hard to
identify. It’s OK to just leave these as uncategorised. Where tests fail due to bugs or differences
in behaviour of Wild, automatic classification likely isn’t practical. A one-off classification of
these would be beneficial.

If time permits, pick something achievable that seems like an important feature / bug to support /
fix and implement / fix it.

**Desirable skills**

Knowledge of Rust. Any existing knowledge of low-level details like assembly or the ELF binary
format is useful, but can potentially be learned as we go.

**Project size**

Small to large depending on chosen scope.

**Difficulty**

Some of the work is medium. Diagnosing and / or fixing failures is often pretty hard.

**Mentor**

- David Lattimore ([GitHub](https://github.com/davidlattimore), [Zulip](https://rust-lang.zulipchat.com/#narrow/dm/198560-David-Lattimore))

**Further resources**

- [Wild linker](https://github.com/davidlattimore/wild)  
- [Blog posts, most of which are about Wild](https://davidlattimore.github.io/)
