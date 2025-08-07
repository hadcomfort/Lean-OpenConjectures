# Lean-OpenConjectures

Exploration and formal verification of mid-tier open problems in combinatorics, graph theory, and number theory using Lean 4 + `mathlib`. This repo focuses on rigorous proof development, careful organization of conjectures and special cases, and reproducible verification via CI.

Highlights
- Lean 4 + `mathlib` proofs with continuous integration checks
- Modular libraries for lemma discovery and proof structuring
- Reproducible experiments, datasets, and benchmarks
- Clear contribution pathways for new conjectures and special cases

Goals
- Formalize special cases and improved bounds for scoped open problems.
- Build a reusable toolkit for lemma development and proof search.
- Publish verified proofs, structured conjectures, and negative results (counterexamples) with reproducible artifacts.

Repository structure
- `lean/`: Lean 4 source files and proof scripts.
- `lean/Conjectures/`: Problem statements, partial results, and special cases.
- `lean/Tactics/`: Custom tactics and utilities.
- `scripts/`: Tools for dataset generation, search utilities, and verification.
- `data/`: Inputs, instances, small certificates, and logs.
- `notebooks/`: Exploratory analysis and visualization.
- `docs/`: Problem statements, design notes, and write-ups.
- `ci/`: GitHub Actions workflows and CI helpers.
- `Dockerfile` and `environment.yml`: Reproducible environments.

Getting started
1) Prerequisites
- Lean 4 and Lake
- `mathlib` (Lean community library)
- Python 3.10+ for scripts (optional)

2) Quick setup
- Clone the repo
- Install Lean toolchain and dependencies
- Build and run the tests

Example:
- Install Lean toolchain:
  curl https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh -sSf | sh
- Verify:
  lean --version
- Build:
  lake update
  lake build
- Run CI checks locally (optional):
  ./scripts/check.sh

3) Python environment (optional)
- Create environment:
  python -m venv .venv
  source .venv/bin/activate
  pip install -r requirements.txt

What this repo contains
- Formalized problems: Each conjecture lives under `lean/Conjectures/<Topic>/<Name>.lean` with a human-readable statement and references in comments and `docs/`.
- Proven results: Completed theorems with citations, tactics used, and links to related literature.
- Work-in-progress: Clearly marked files with `sorry` placeholders, plus a roadmap in comments.
- Tools: `scripts/generate_instances.py` for data; `scripts/verify_certs.py` for checking computational certificates.
- Datasets: Small instances and counterexamples in `data/`, with provenance and scripts to reproduce.

Methodology
- Conjecture selection: Focus on scoped problems where formalization is feasible and incremental results are valuable.
- Proof development: Prefer small, composable lemmas; document references and proof strategies.
- Verification: Every proof compiles in Lean; computational claims are backed by certificates or reproducible scripts.

Contributing
We welcome contributions of all sizes: fixes, new lemmas, special cases, or computational datasets.

- Issues: Propose conjectures, report bugs, or request features.
- PRs: Include minimal repro instructions and ensure `lake build` passes.
- Style: Prefer small, composable lemmas; add docstrings with references; keep names `CamelCase` for theorems and `snake_case` for helpers.
- Tests: Add minimal examples or `#eval` checks where appropriate.
- Licensing: Code and Lean proofs are MIT; docs and non-code are CC BY 4.0.

Suggested first contributions
- Formalize a known special case or bound from a recent paper.
- Add a small dataset and a script that reproduces it.
- Improve a proof by refactoring into reusable lemmas.
- Write or refine a tactic in `lean/Tactics/` that automates a frequent pattern.

CI and reproducibility
- GitHub Actions builds the Lean project, runs style checks, and executes lightweight scripts on each PR.
- Nightly workflows may run heavier enumeration or solver tasks; outputs are uploaded as artifacts.
- All random experiments use fixed seeds and pinned solver/tool versions.

Roadmap
- v0.1: Baseline structure, first formalized conjectures, CI, and scripts.
- v0.2: Expanded tactic library and examples.
- v0.3: First publishable results (new bounds/special cases) with formal proofs and reproducible data.

Citing and attribution
If you use this repository, please cite the commit hash or a tagged release. For academic use, include links to Lean files and artifacts. We maintain references in `docs/refs.bib` and inline comments.

License
- Code and Lean proofs: MIT (see `LICENSE`)
- Documentation and non-code assets: CC BY 4.0 (see `LICENSE-docs`)

Acknowledgments
Built on Lean 4 and `mathlib`. Thanks to the Lean community and open-source solver and CAS developers whose tools we rely on.

Contact
Open an issue or start a discussion in `Discussions`. If you’d like a tailored onboarding issue, ping the maintainers in an issue.
