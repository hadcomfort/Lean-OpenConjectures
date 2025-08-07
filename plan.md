### Phase 1: Foundational Setup (Goal: v0.1)

This phase is about creating the skeleton of the project, setting up the development environment, and ensuring everything is verifiable from day one with Continuous Integration (CI).

#### **Step 1: Initialize the Repository Structure**

First, create the directory and file structure outlined in your `README.md`. This organizes the project logically before you write any code.

```bash
# Create directories
mkdir -p lean/Conjectures lean/Tactics lean/Lib scripts data notebooks docs ci

# Create placeholder files
touch lean/Conjectures/.gitkeep
touch lean/Tactics/.gitkeep
touch lean/Lib/.gitkeep # A good place for general-purpose lemmas
touch scripts/.gitkeep
touch data/.gitkeep
touch notebooks/.gitkeep
touch docs/refs.bib
touch ci/.gitkeep
touch Dockerfile
touch environment.yml
touch requirements.txt
touch LICENSE
touch LICENSE-docs
touch .gitignore
```

#### **Step 2: Initialize the Lean Project**

Set up the Lean 4 project using `lake`, the official build system and package manager.

1.  **Initialize Lake:** In the repository's root directory, run:
    ```bash
    lake init LeanOpenConjectures
    ```
2.  **Add `mathlib` Dependency:** Open the newly created `lakefile.lean` and add the `mathlib` library as a dependency.
    ```lean
    -- In lakefile.lean
    import Lake
    open Lake DSL

    package «lean-open-conjectures» where
      -- add package configuration options here

    lean_lib «LeanOpenConjectures» where
      -- add library configuration options here

    require mathlib from git
      "https://github.com/leanprover-community/mathlib4.git"

    @[default_target]
    lean_exe «lean-open-conjectures» where
      root := `Main
    ```
3.  **Fetch Dependencies:** Run `lake update` to download `mathlib`.
4.  **Initial Build:** Run `lake build` to confirm everything is configured correctly.

#### **Step 3: Configure Environments**

Create the configuration files for reproducible environments.

1.  **`.gitignore`:** Add standard ignores for Lean, Python, and OS files (e.g., `build/`, `lake-packages/`, `.venv/`, `__pycache__/`, `.DS_Store`).
2.  **`Dockerfile`:** Create a `Dockerfile` that installs `elan` (the Lean toolchain manager), Python, copies the project files, and runs a `lake build`. This is crucial for CI and reproducibility.
3.  **`environment.yml` (for Conda):** Set up a basic Conda environment.
    ```yaml
    name: lean-open-conjectures
    channels:
      - defaults
    dependencies:
      - python=3.10
      - pip
      - pip:
        - -r requirements.txt
    ```
4.  **`requirements.txt` (for Python):** List initial Python dependencies for your scripts.
    ```txt
    # For data analysis and scripting
    numpy
    pandas
    matplotlib
    ```

#### **Step 4: Implement Continuous Integration (CI)**

Set up a basic CI workflow using GitHub Actions to automatically build the project on every push and pull request.

1.  **Create Workflow File:** Create a file named `ci/main.yml` (or `.github/workflows/ci.yml`).
2.  **Define the Workflow:** The workflow should:
      * Trigger on `push` and `pull_request`.
      * Check out the code.
      * Install `elan`.
      * Cache the `lake-packages` directory to speed up future builds.
      * Run `lake build`.

This ensures that any contribution an be successfully compiled.

-----

### Phase 2: Formalizing the First Conjecture

Now that the foundation is solid, it's time to start the core work. Choose a simple, well-known conjecture to test the entire workflow.

#### **Step 5: Select and Scope the First Conjecture**

Choose an initial problem. Good candidates are problems with simple statements but deep results, like a specific case of **Ramsey's Theorem** or a property of the **Collatz sequence**.

1.  **Create a Tracking Issue:** Open a new issue on GitHub titled "Formalize Conjecture: [Name of Conjecture]". Use it to track progress, references, and discussion.
2.  **Start a Doc Page:** Create `docs/[ConjectureName].md`. Write down the problem statement in plain English and LaTeX, and add links to key papers or Wikipedia articles.

#### **Step 6: Formalize the Problem Statement**

Translate the mathematical statement into a formal Lean 4 definition.

1.  **Create the Lean File:** Create `lean/Conjectures/Topic/ConjectureName.lean` (e.g., `lean/Conjectures/NumberTheory/Collatz.lean`).
2.  **Write the Definition:** Inside, define the conjecture. Use doc comments (`/-- ... -/`) to explain the definition and link to your documentation page and external references.
    ```lean
    import Mathlib.Tactic

    /--
    The Collatz conjecture states that for any positive integer `n`, the sequence
    defined by `f(n) = n/2` if `n` is even and `f(n) = 3n+1` if `n` is odd
    eventually reaches 1.

    **References:**
    - [Link to docs/Collatz.md]
    - [Link to Wikipedia]
    -/
    def CollatzConjecture :=
      ∀ n : ℕ, n > 0 → ∃ k : ℕ, (fun x => if Even x then x / 2 else 3 * x + 1)^[k] n = 1
    ```

#### **Step 7: Prove a Base Case or a Simple Lemma**

Start with a small, achievable proof to build momentum. This is the "Hello, World\!" of formal verification.

1.  **State the Theorem:** Formally state a simple special case (e.g., the conjecture holds for $n \\in {1, 2, 3, 4}$).
2.  **Prove It:** Use tactics from `mathlib` to prove the theorem. Initially, it's fine to use `sorry` to placeholder parts of the proof you haven't finished.
    ```lean
    theorem collatz_holds_for_4 : -- A simplified statement for one case
      ∃ k : ℕ, (fun x => if Even x then x / 2 else 3 * x + 1)^[k] 4 = 1 := by
      -- Proof steps here
      use 2
      rfl -- `rfl` proves things that are true by definition
    ```
3.  **Commit and Verify:** Commit your new file and push it. Your CI workflow should run automatically and confirm that your code compiles. ✅

-----

### Phase 3: Building the Toolkit (Goal: v0.2)

With one result formalized, you'll start noticing repetitive patterns. Now is the time to build tools to automate them.

#### **Step 8: Abstract Reusable Lemmas**

Move general-purpose helper lemmas from your conjecture file into a shared library file, like `lean/Lib/MyHelpers.lean`. This follows the principle of creating small, composable proofs.

#### **Step 9: Develop a Custom Tactic**

If you find yourself using the same sequence of tactics repeatedly, create a custom tactic.

1.  **Identify a Pattern:** For example, a pattern of "introduce variables, apply a hypothesis, simplify, and close the goal."
2.  **Implement the Tactic:** In `lean/Tactics/MyTactic.lean`, write a simple `macro` to automate this sequence.
3.  **Test It:** Apply your new tactic to refactor one of your existing proofs to make it shorter and more readable.

#### **Step 10: Create a Data Generation Script**

For conjectures involving computational cases, write a Python script.

1.  **Write the Script:** Create `scripts/generate_instances.py`. This script could, for example, generate small graphs and save them in a standard format (like JSON or CSV) to the `data/` directory.
2.  **Ensure Reproducibility:** Use a fixed random seed (`random.seed(42)`) so the output is always the same. Document the data format in the script's docstring.

-----

### Phase 4: Scaling and Publishing (Goal: v0.3)

This phase focuses on formalizing a novel or significant result and preparing it for broader consumption.

#### **Step 11: Formalize a Non-Trivial Result**

Select a more ambitious target: a published result that has not yet been formalized, or an improvement on a known bound. This will leverage your entire setup: `mathlib`, custom tactics, and helper libraries.

#### **Step 12: Write a Comprehensive Write-up**

Document the result thoroughly in the `docs/` directory. This document should serve as the primary human-readable explanation of your formal proof and should be clear enough for someone unfamiliar with Lean to understand the accomplishment.

#### **Step 13: Tag a Release and Archive**

Once the result is fully proven and documented:

1.  **Tag a Git Release:** Create a tag like `v0.3.0`.
2.  **Archive (Optional but Recommended):** Create a snapshot of the repository on a service like [Zenodo](https://zenodo.org/) to get a persistent Digital Object Identifier (DOI). This makes your work citable in academic papers.

-----

### Phase 5: Fostering Community and Long-Term Maintenance

A successful project is a living one. The final phase is about encouraging collaboration and keeping the repository healthy.

#### **Step 14: Onboard Contributors**

Actively solicit contributions by creating issues with the `good first issue` label. These could be:

  * "Formalize this simple lemma from Paper X."
  * "Refactor this proof using the new `my_tactic`."
  * "Add a script to visualize data from the `data/` directory."

#### **Step 15: Ongoing Maintenance**

Periodically update dependencies (especially `mathlib`) by running `lake update` and fix any breaking changes. This ensures the project remains usable and up-to-date with the latest developments in the Lean ecosystem.
