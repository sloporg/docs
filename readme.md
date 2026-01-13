# slop documentation

source code for the official slop programming language documentation.

**live:** [docs.sloplang.org](https://docs.sloplang.org)

## structure

this repository uses a multi-branch structure for versioning:

- **`main`**: this branch. contains repo metadata.
- **`v0.0.1`**: source for the stable v0.0.1 documentation.
- **`v0.0.1-dev`**: active development for the upcoming v0.0.1 patch/features.

## contributing

to contribute to the documentation:

1. checkout the relevant dev branch (e.g., `v0.0.1-dev`).
2. make your changes in `src/`.
3. open a pr against that dev branch.

## building

powered by [mdbook](https://rust-lang.github.io/mdBook/).

```bash
# install mdbook
cargo install mdbook

# build locally
mdbook build

# serve locally
mdbook serve --open
```
