---
description: Cursor reference guide for hardware verification engineers — Rules, Commands, Skills, Agents tailored to spec reading, testbench code, variable initialization, and CSR verification.
alwaysApply: false
---

# Cursor for Verification Engineers

> All examples are drawn from hardware verification workflows: SystemVerilog/UVM testbenches, specification reading, CSR verification, coverage closure, and simulation automation.

---

## The Mental Model (Verification Context)

> **Rules guide. Skills do. Commands trigger. Agents specialize.**

In verification terms:
- **Rules** → your team's coding standards, naming conventions, and methodology guidelines — always active in the background
- **Skills** → verification procedures you activate: "run CSR compliance check," "extract test plan from spec," "generate UVM sequence"
- **Commands** → repeatable simulation workflows you trigger with a slash: `/run-sim`, `/check-coverage`, `/tb-checkpoint`
- **Agents** → specialist personas: SpecReader, CsrChecker, CoverageAnalyzer, TbReviewer

---

## The Four Artifact Types

| Type | Purpose | Invocation | When to Use |
|------|---------|------------|-------------|
| **Rules** | Persistent context and guardrails | Automatic or @mention | UVM coding standards, naming conventions, methodology policies |
| **Commands** | User-triggered workflows | `/command` — manual only | Run simulation, check coverage, commit waveform snapshot |
| **Skills** | Portable knowledge modules | Agent decides OR `/skill-name` | CSR verification, spec parsing, coverage closure workflows |
| **Agents** | Specialized AI personas | Spawned by main agent | Deep spec analysis, scoreboard review, CSR compliance audit |

### Activation Matrix

Commands are the only artifact type that is **always manual** — the agent will never call them automatically.

|  | User invokes | Agent decides | Always on | File/folder match |
|--|:---:|:---:|:---:|:---:|
| **Rules** | Yes | Yes | Yes | Yes |
| **Skills** | Yes | Yes | No | No |
| **Commands** | Yes | **No** | **No** | **No** |

### The Problem: Everything in Rules

Most verification teams start with rules and put everything there:

- UVM naming conventions → Rule ✓
- Simulation run workflow → Rule (awkward)
- CSR compliance verification procedure → Rule (wrong tool)
- Reusable coverage closure knowledge → Rule (won't scale)

The fix: Use the right artifact for the job.

### Decision Flowchart

```mermaid
flowchart TB
  Q{"What do you need?"}:::primary
  Q --> RULE["RULE\nstandards & policies"]:::accent
  Q --> CMD["COMMAND\nsim workflow trigger"]:::accent
  Q --> SKILL["SKILL\nverification procedure"]:::accent
  Q --> AGENT["AGENT\nspec / coverage expert"]:::accent
```

### Quick Reference

| You want... | Use |
|-------------|-----|
| "Always use `_e` suffix for enum types" | Rule |
| "Never drive interface signals from monitor" | Rule |
| "Run simulation with coverage, then check holes" | Command |
| "Generate test plan from this spec section" | Skill |
| "Verify all CSR registers against spec" | Skill |
| "Deep analysis of uncovered assertions" | Agent |
| "Review my scoreboard for functional correctness" | Agent |

### Directory Structure

```
.cursor/
├── rules/
│   ├── uvm-coding-standard/RULE.md
│   ├── csr-access-policy/RULE.md
│   ├── naming-convention/RULE.md
│   └── autonomous-workflows/RULE.md
├── commands/
│   ├── run-sim.md
│   ├── check-coverage.md
│   ├── tb-checkpoint.md
│   └── generate-csr-test.md
├── agents/
│   ├── spec-reader.md
│   ├── csr-checker.md
│   ├── coverage-analyzer.md
│   └── tb-reviewer.md
└── skills/
    ├── csr-verification/
    │   └── SKILL.md
    ├── coverage-closure/
    │   └── SKILL.md
    └── spec-extraction/
        └── SKILL.md

# Global skills (cross-project)
~/.cursor/skills/
├── uvm-patterns/SKILL.md
└── protocol-verification/SKILL.md
```

---

# Part 1: Rules

## What Rules Are

Rules are **passive**. They shape how the agent responds when working in your verification environment. They are injected into the model context before every conversation — like your team's methodology handbook always being open on the desk.

"Rule contents are included at the start of the model context." — Cursor docs

Rules don't run simulations. They don't invoke tools. They sit in the background, ensuring every suggestion the agent makes is consistent with your verification methodology.

### Four Activation Modes

| Activation Mode | How It Works |
|----------------|--------------|
| **Always Apply** | Active in every conversation. Core verification standards. |
| **Apply Intelligently** | Agent reads the rule's description and decides if it's relevant. |
| **Apply to Specific Files** | Activates when working with files matching a pattern (e.g., `tb/**`, `*.sv`). |
| **Apply Manually** | Only included when you explicitly reference it with `@rule-name`. |

### What Belongs in Rules

- UVM class naming conventions (`_env`, `_agent`, `_mon`, `_scb`, `_seq`, `_drv`)
- SystemVerilog coding standards (signal naming, always block style, clocking blocks)
- Interface and signal driving policies ("monitors are passive — never drive")
- CSR access methodology ("always access registers through `uvm_reg`, never direct backdoor unless explicitly required")
- Assertion naming conventions (`assert_property_<module>_<condition>`)
- Coverage group naming and bin policies
- Tool invocation conventions (simulator flags, seed policy)
- Things the agent gets wrong repeatedly about your verification environment

### What Does NOT Belong in Rules

- Multi-step simulation and coverage workflows (that's a skill)
- One-off test generation you run occasionally (that's a command)
- Detailed CSR verification procedures with reference register maps (that's a skill)

> **Rule of thumb:** If it tells the agent *how to write* verification code, it's a rule. If it tells the agent *how to run* a verification procedure, it's a skill.

### Example Rule: UVM Coding Standard

```yaml
---
description: "UVM component naming, coding conventions, and methodology standards"
globs: ["tb/**/*.sv", "tb/**/*.svh", "**/uvm_*.sv"]
alwaysApply: false
---

## UVM Naming Conventions

### Class Suffixes
- Environment:    `<dut>_env`
- Agent:          `<dut>_agent`
- Monitor:        `<dut>_monitor` (passive — never drives)
- Scoreboard:     `<dut>_scoreboard`
- Sequence:       `<name>_seq`
- Driver:         `<dut>_driver`
- Sequencer:      `<dut>_sequencer`
- Transaction:    `<dut>_transaction` or `<dut>_item`
- Interface:      `<dut>_if`

### Signal Naming
- Clocks:     `clk_<domain>`
- Resets:     `rst_n_<domain>` (active low)
- DUT ports:  exact match to RTL specification
- TB signals: `tb_<signal_name>`

### Coding Standards
- All `always` blocks must use `always_ff`, `always_comb`, or `always_latch`
- Interface signals driven only from driver — never monitor
- Use `uvm_info`, `uvm_warning`, `uvm_error`, `uvm_fatal` — no `$display`
- All phases must call `super.<phase_name>(phase)`

## CSR Access Policy
- Use `uvm_reg` for all CSR read/write operations
- Backdoor access only permitted in initialization sequences with comment `// BACKDOOR: <justification>`
- Always check `uvm_status_e` return status after register operations
```

### Example Rule: CSR Access Policy

```yaml
---
description: "CSR register access and initialization policies"
globs: ["**/csr/**", "**/reg_model/**", "**/*_reg_block*"]
alwaysApply: false
---

## Register Access Rules

All CSR accesses must go through the register model:

```sv
// CORRECT — through uvm_reg
uvm_status_e status;
reg_block.ctrl_reg.write(status, wr_data, UVM_FRONTDOOR);
assert(status == UVM_IS_OK);

// INCORRECT — direct signal assignment outside of init sequence
force dut.ctrl_reg = wr_data;
```

## Initialization Sequence
- All register block initialization must use `reset_reg_model()` before tests
- Default values must match specification Table 3.x reset values
- Reserved fields must be written as 0 unless spec states otherwise

## Field Access Width
- Never write a full register word if only one field is being configured
- Use field-level API: `reg_block.ctrl_reg.enable_field.write(...)`
```

---

## The alwaysApply Tax in Verification

A mature verification environment can accumulate dozens of rules, all with `alwaysApply: true`. Every token loaded on every conversation — including simple RTL questions — is wasted context.

### The 2+2 Test

> Ask: "If someone asks 'what does this SystemVerilog syntax mean?', does this rule need to be loaded?"
> - If yes → `alwaysApply`
> - If no → globs or skill

### Rules That Should Stay alwaysApply

| Rule | Why |
|------|-----|
| `core-uvm-methodology` | Fundamental verification approach |
| `core-security` (secrets, keys in tests) | Never commit test keys/passwords |
| `core-no-display` | Never use `$display` — use UVM macros |
| `core-passive-monitor` | Monitors must never drive — universal |
| `core-phase-super` | Always call `super.phase()` — universal |

### Rules That Should Use Globs

| Rule | Current | Should Be |
|------|---------|-----------|
| `csr-access-policy` | alwaysApply | `globs: ["**/csr/**", "**/reg_model/**"]` |
| `coverage-naming` | alwaysApply | `globs: ["**/*_cg.sv", "**/coverage/**"]` |
| `assertion-style` | alwaysApply | `globs: ["**/*_sva.sv", "**/assertions/**"]` |
| `interface-naming` | alwaysApply | `globs: ["**/*_if.sv"]` |
| `sequence-coding` | alwaysApply | `globs: ["**/sequences/**"]` |

### Size Guidelines

| Artifact | Target | Max |
|---------|--------|-----|
| Rule (alwaysApply) | < 50 lines | 100 lines |
| Rule (glob-triggered) | < 100 lines | 200 lines |
| Skill SKILL.md | < 150 lines | 300 lines |
| Skill references/ | Unlimited | — |

### Audit Your Setup

```bash
# Count alwaysApply rules
grep -l "alwaysApply: true" .cursor/rules/**/*.md | wc -l

# Total lines loaded in every conversation
grep -l "alwaysApply: true" .cursor/rules/**/*.md | xargs wc -l
```

---

# Part 2: Commands

## What Commands Are

Commands are **saved prompts** — plain Markdown files triggered by typing `/` in chat. In verification, they are pre-written simulation and workflow procedures you invoke manually.

- No automatic activation
- No progressive loading
- **Always manual — the agent will never call a command on its own**

Instead of typing "run the regression with coverage enabled, collect the coverage database, then summarize uncovered bins" every time, you save it as `/run-regression`.

### Rules vs Commands

| Aspect | Rules | Commands |
|--------|-------|---------|
| Frontmatter | Required (YAML) | **None** |
| Invocation | Automatic or @mention | User types `/command-name` |
| Purpose | Standards context injection | Simulation action execution |
| Location | `.cursor/rules/` | `.cursor/commands/` |

**Critical mistake:** Do not put YAML frontmatter in commands.

### Command Structure

```markdown
# /command-name - Brief Description

One-line summary of what this command does.

## Instructions

When the user invokes `/command-name`, do the following:

1. First step
2. Second step
3. Third step

### Default Behavior

What happens with no arguments.

## Variants

### `/command-name --flag`
What this variant does differently.

## Output Format

Expected output structure

## Examples

### Basic Usage
User: /command-name
Output: [what happens]
```

---

### Example: The /run-sim Command

```markdown
# /run-sim - Run Simulation with Coverage

Compile, elaborate, and run the testbench with functional and code coverage enabled.

## Instructions

When the user invokes `/run-sim`:

1. **Compile**
   - Run `vlog` (or tool equivalent) on all `.sv` and `.svh` sources
   - Include `+define+UVM_NO_DEPRECATED`
   - Report compile errors — do not proceed on error

2. **Elaborate**
   - Run `vsim -c <tb_top> -do "..."`
   - Enable all coverage types: `+cover=bcesf`
   - Load the UVM register model if CSR test detected

3. **Simulate**
   - Run the test specified by `UVM_TESTNAME` (default: `base_test`)
   - Collect coverage database to `cov_work/<testname>/`
   - On `UVM_FATAL`: stop and report

4. **Post-Sim Summary**
   - Show pass/fail count from UVM report
   - Show coverage percentage if available
   - List any `UVM_ERROR` or `UVM_WARNING` messages

### Default Behavior

Runs `base_test` with seed 1. Compiles from scratch.

## Variants

### `/run-sim --test <testname>`
Run a specific UVM test by name.

### `/run-sim --seed <N>`
Run with specific seed for reproducibility.

### `/run-sim --no-compile`
Skip compilation — use previous elaboration.

### `/run-sim --regress`
Run all tests in regression list `tests/regression.list`.

## Output Format

## Simulation Summary

**Test**: `<testname>`
**Seed**: `<N>`
**Result**: PASS / FAIL

### UVM Report
- UVM_INFO:    [count]
- UVM_WARNING: [count] — [list any non-trivial ones]
- UVM_ERROR:   [count] — [list all]
- UVM_FATAL:   [count] — [list all]

### Coverage
- Functional coverage: [X]%
- Code coverage:        [X]%
- Coverage DB: `cov_work/<testname>/`
```

---

### Example: The /check-coverage Command

```markdown
# /check-coverage - Analyze Coverage Holes

Merge coverage databases and report uncovered bins, uncovered assertions, and toggle holes.

## Instructions

When the user invokes `/check-coverage`:

1. **Merge Databases**
   - Run `vcover merge cov_work/merged.ucdb cov_work/*/`
   - Report how many individual runs were merged

2. **Functional Coverage**
   - List all covergroups with < 100% coverage
   - For each: show uncovered bins and their cross-conditions
   - Flag any covergroup below threshold (default: 90%)

3. **Code Coverage**
   - Identify uncovered lines, branches, and FSM states
   - Highlight RTL blocks with 0% branch coverage

4. **Assertion Coverage**
   - List all assertions that never fired
   - List assertions that fired but never passed (only vacuously true)

5. **Recommendation**
   - Suggest new sequences or test scenarios to close each hole
   - Prioritize by: (1) spec-required coverage, (2) corner cases, (3) random hits

### Default Behavior

Merges all databases in `cov_work/` and generates full report.

## Variants

### `/check-coverage --module <module_name>`
Restrict analysis to a specific DUT module.

### `/check-coverage --cg <covergroup_name>`
Focus on a specific covergroup.

### `/check-coverage --threshold <N>`
Flag covergroups below N% (default: 90).

## Output Format

## Coverage Analysis Report

**Merged runs**: [N]
**Overall functional coverage**: [X]%
**Overall code coverage**: [X]%

### ⚠️ Uncovered Functional Bins

| Covergroup | Bin | Current | Required | Suggested Test |
|-----------|-----|---------|---------|----------------|
| `apb_trans_cg` | `wr_after_rd` | 0% | 100% | `directed_wr_after_rd_seq` |

### ⚠️ Uncovered Code

| Module | Line | Type | Notes |
|--------|------|------|-------|
| `ctrl_fsm` | 142 | Branch | `ERROR` state never entered |

### 🔴 Assertions Never Fired

| Assertion | Location | Condition |
|-----------|---------|-----------|
| `assert_no_back2back_wr` | `csr_if.sv:88` | Never triggered |
```

---

### Example: The /tb-checkpoint Command

```markdown
# /tb-checkpoint - Save Testbench Work State

Clean up testbench code, validate, and commit with a descriptive message.

## Instructions

When the user invokes `/tb-checkpoint`:

1. **Clean up**
   - Remove debug `$display` statements (replace with `uvm_info` if needed)
   - Remove commented-out dead code blocks
   - Fix obvious formatting (indentation, trailing whitespace)
   - Remove temporary `force`/`release` not marked with `// BACKDOOR:` comment

2. **Validate**
   - Check all modified `.sv` files compile cleanly (`vlog` lint)
   - Verify `uvm_reg` status checks are present after every register write
   - Confirm no monitor is driving any interface signal

3. **Commit**
   - Stage only testbench source files (not waveform dumps, not coverage DBs)
   - Generate commit message: `tb: <what changed>` (conventional TB commit style)
   - Show message for approval before committing

### Default Behavior

Processes all modified `.sv`, `.svh`, `.v` files.

## Variants

### `/tb-checkpoint --message "specific message"`
Use provided message instead of generating one.

### `/tb-checkpoint --lint-only`
Run lint validation only — do not commit.

## Output Format

## TB Checkpoint Summary

### Cleaned
- Removed [N] `$display` statements
- Fixed [N] formatting issues

### Validated
- ✓ Compile: clean
- ✓ No monitors driving signals
- ✓ Register writes have status checks

### Committed
Message: "tb: add APB write-after-read directed test"
Files: [N] changed (+[lines], -[lines])
```

---

### Example: The /generate-csr-test Command

```markdown
# /generate-csr-test - Generate CSR Read/Write Test

Generate a UVM test that walks all registers in a block, performing reset-value check, read/write/read, and field accessibility tests.

## Instructions

When the user invokes `/generate-csr-test`:

1. **Identify Register Block**
   - Ask: "Which register block? (e.g., `ctrl_reg_block`)"
   - Or infer from context if a reg model file is open

2. **Generate Reset Value Test**
   - For every register: read after reset, compare to spec-defined reset value
   - Flag mismatches as `UVM_ERROR`

3. **Generate RW Accessibility Test**
   - For each RW field: write walking-ones pattern, read back, verify
   - For each RO field: attempt write, verify value did not change
   - For each WO field: write, verify write completed without error
   - For reserved fields: write 0, verify no side effect

4. **Generate CSR Aliasing Test**
   - If spec defines aliased addresses, verify both addresses reach same register

5. **Output Files**
   - `tests/csr_reset_test.sv` — reset value checks
   - `tests/csr_rw_test.sv` — read/write accessibility
   - `tests/csr_alias_test.sv` — aliasing checks (if applicable)

## Variants

### `/generate-csr-test --reg <reg_name>`
Generate tests for a single register only.

### `/generate-csr-test --field <field_name>`
Generate tests for a single field only.
```

---

## Command Coalescing (Verification Workflows)

### Problem: Overlapping Commands

```
User: "/run-sim and /tb-checkpoint then push to regression"
```

Without coalescing:
```
1. /run-sim   → compiles, runs sim
2. /tb-checkpoint → compiles again, validates, commits
3. push       → pushes

/tb-checkpoint already includes compile validation!
```

### Command Subsumption

```mermaid
flowchart TD
    subgraph tbcheckpoint["/tb-checkpoint"]
        CP_lint["/lint-tb"]
        CP_commit["git commit"]
    end

    subgraph analyze["/analyze-tb"]
        AN_review["/review-tb"]
        AN_critique["/tb-critique (partial)"]
    end

    subgraph regression["/run-regression"]
        RG_sim["/run-sim (all seeds)"]
        RG_cov["/check-coverage"]
    end
```

| If Requested | Skip | Because |
|-------------|------|---------|
| /analyze-tb + /review-tb | /review-tb | analyze includes review |
| /tb-checkpoint + /lint-tb | /lint-tb | checkpoint includes lint |
| /run-regression + /run-sim | /run-sim | regression subsumes single run |

### Command Ordering (Verification)

| Commands | Correct Order | Reason |
|---------|--------------|--------|
| /review-tb + /tb-checkpoint | review → checkpoint | Review before committing |
| /run-sim + /check-coverage | run-sim → check-coverage | Must have a DB before checking |
| /generate-csr-test + /run-sim | generate → run-sim | Create test before running it |
| /analyze-tb + /fix-tb | analyze → fix | Understand before changing |

---

## Autonomous Actions: The Verification Autonomy Spectrum

```mermaid
flowchart LR
    subgraph spectrum["Verification Autonomy Spectrum"]
        direction LR
        S["SUGGEST\n'Consider running reset test'"]
        A["ASK\n'Should I compile now?'"]
        C["CONFIRM\n'Push to shared regression? OK?'"]
        E["EXECUTE\n[compiles silently]"]
    end
    S --> A --> C --> E
```

| Level | Behavior | For Actions That Are... |
|-------|---------|------------------------|
| **4: Execute** | Do silently | Reversible, local, no side effects |
| **3: Inform** | Do and report | Reversible, persistent, minor effects |
| **2: Confirm** | Propose and wait | Affects shared regression or team state |
| **1: Suggest** | Mention only | Destructive or irreversible |

### Pre-Authorized Actions (Execute Silently)

| Action | When | Notes |
|--------|------|-------|
| Read `.sv`, `.svh`, spec files | Always | Core capability |
| Search testbench codebase | Always | Core capability |
| Lint/compile check | After edit | Flag errors only |
| Remove `$display` debug statements | Before commit | Cleanup |
| Fix indentation/trailing whitespace | During edit | Non-functional |

### Inform After (Do and Report)

| Action | When | Report Format |
|--------|------|--------------|
| Run single simulation | After test creation | "✓ PASS — 0 UVM_ERROR, cov: 73%" |
| Local commit | After completing TB component | Show commit message |
| Generate CSR test file | When register block identified | "Generated: tests/csr_rw_test.sv" |
| Install missing Python libs for coverage analysis | When script missing dep | "Installed: pyuvm@2.4" |

### Requires Confirmation

| Action | Prompt |
|--------|--------|
| Push to shared regression server | "Push to origin/main regression branch? [Y/n]" |
| Delete waveform databases | "Delete 3 .wlf files? [Y/n]" |
| Overwrite team coverage database | "Merge and overwrite cov_work/team_merged.ucdb? [Y/n]" |
| Modify CSR register model (reg_block.sv) | "Modify shared register model? [Y/n]" |

### Never Without Explicit Request

- Force push to shared regression branch
- Drop/truncate shared coverage database
- Modify RTL source files (DUT is read-only for verification)
- Overwrite golden reference waveforms

---

### Continuous Verification Work Loop

```mermaid
flowchart TD
    Analyze[ANALYZE\nspec / code] --> Write[WRITE\nsequence / scoreboard] --> Checkpoint[TB-CHECKPOINT]
    Checkpoint --> Simulate[RUN-SIM]
    Simulate --> Coverage{Coverage\nOK?}
    Coverage -->|No| Analyze
    Coverage -->|Yes| Done[Done / PR]
```

### Autonomous Session Example

```mermaid
flowchart TD
    User["User: Add APB write burst test"]

    subgraph Autonomous["Autonomous Actions"]
        Analyze["ANALYZE spec section"]
        Write["WRITE apb_burst_seq.sv"]
        Review["REVIEW sequence code"]
    end

    subgraph Inform["Inform After"]
        Checkpoint["TB-CHECKPOINT"]
        Sim["RUN-SIM (inform result)"]
    end

    subgraph Confirm["Requires Confirmation"]
        Push["Push to regression? Y/n"]
    end

    User --> Analyze --> Write --> Review --> Checkpoint --> Sim --> Push
```

| Phase | Action | Details |
|-------|--------|---------|
| **Analyze** | Read spec | APB burst write section 4.3 — max 4-beat burst, OKAY/SLVERR response |
| **Write** | Created sequence | `apb_burst_wr_seq.sv` — 1, 2, 4-beat variants, error injection |
| **Review** | Self-checked | All burst lengths covered, status checked, no monitor driving |
| **Checkpoint** | Committed | `tb: add APB write burst sequence` |
| **Sim** | **Reported** | PASS — 0 UVM_ERROR, func cov 81% |
| **Push** | **Awaiting** | Requires confirmation (affects shared regression) |

---

# Part 3: Skills

## Rules vs Skills for Verification

| Artifact | Purpose | Content Type | Activation |
|---------|---------|-------------|-----------|
| **Rules** | What and When | Passive reference | Automatic |
| **Skills** | How | Active procedure | Explicit invocation |

- **Rule:** "CSR reserved fields must be written as 0" ← passive, shapes all edits
- **Skill:** "To verify CSR registers: 1. Read spec table, 2. Generate reset-value test, 3. Run, 4. Compare..." ← active workflow

### Content Type Mapping

| Content Type | Belongs In | Verification Example |
|-------------|-----------|---------------------|
| Coding standards | Rule | "All `always` blocks must use `always_ff`" |
| Naming conventions | Rule | "Monitor suffix must be `_monitor`" |
| Access policies | Rule | "Always use `uvm_reg` for CSR access" |
| Multi-step verification procedures | Skill | "CSR compliance: read spec → generate test → run → compare" |
| Spec parsing workflows | Skill | "Extract test plan from specification document" |
| Coverage closure procedures | Skill | "Identify uncovered bins → write directed sequence → re-run" |

---

## Agent Skills: The Open Standard

[Agent Skills](https://agentskills.io/) is an open standard. Skills you write for Cursor also work in Claude Code, VS Code, Gemini CLI, and others — your UVM knowledge packages are portable.

| Trait | What It Means for Verification |
|-------|-------------------------------|
| **Portable** | UVM skill written once, works across all tool setups |
| **Version-controlled** | Stored as files alongside your testbench — tracked in Git |
| **Executable** | Can include Python scripts for coverage analysis or reg-model generation |
| **Progressive** | Spec reference tables load on demand — SKILL.md stays lean |

---

## Where Skills Live

| Location | Scope |
|---------|-------|
| `.cursor/skills/` | Project-level (this DUT/TB) |
| `~/.cursor/skills/` | User-level (all your verification projects) |
| `.claude/skills/` | Cross-tool compatibility |

Global skills for cross-project knowledge: UVM methodology, protocol verification patterns, common CSR idioms.

### Skill Directory Structure

```
.cursor/skills/csr-verification/
├── SKILL.md                    # Required
├── scripts/
│   ├── parse_reg_map.py        # Parse Excel/CSV register map
│   └── gen_csr_test.py         # Generate CSR test from reg model
├── references/
│   ├── CSR_CODING_GUIDE.md     # Detailed CSR verification methodology
│   └── COMMON_CSR_BUGS.md      # Known CSR bug patterns
└── assets/
    └── csr_test_template.sv    # UVM test template
```

| Directory | Purpose | When Loaded |
|-----------|---------|------------|
| `scripts/` | Executable: reg-map parsers, test generators | When skill executes |
| `references/` | Spec tables, methodology docs | On demand (progressive) |
| `assets/` | UVM templates, config files | When referenced |

---

## The SKILL.md Frontmatter

| Field | Required | Description |
|-------|---------|------------|
| `name` | Yes | Skill identifier. Lowercase, hyphens. Must match folder name. |
| `description` | Yes | What the skill does and when to use it. **How the agent decides relevance.** |
| `compatibility` | No | Tool requirements (Python version, simulator, etc.) |
| `metadata` | No | Category, team ownership, compliance tags. |
| `disable-model-invocation` | No | When `true`, only invoked via `/skill-name`. |

---

## Real Skill Examples

### The CSR Verification Skill

```yaml
---
name: csr-verification
description: |
  Verify CSR (Control and Status Register) registers against specification.
  Use when user asks to: verify registers, check CSR compliance, test register
  map, run CSR tests, validate reset values, check field accessibility.
  Proactively apply when: register model files are open, spec section mentions
  CSR map, user opens *_reg_block.sv or *_reg_map.sv files.
  Triggers: "verify CSRs", "check register values", "CSR compliance",
  "reset value check", "register accessibility", "field RW test",
  "CSR aliasing", "register map verification"
compatibility: Requires Python 3.8+ for register map parsing scripts
metadata:
  category: verification
  compliance: [functional-coverage, reg-model]
---

# CSR Verification Skill

## Scope

Verify all registers in a register block against the specification-defined:
- Reset values
- Field accessibility (RW, RO, WO, W1C, etc.)
- Field widths and positions
- Reserved field behavior
- Aliased address handling

## Process

### 1. Parse Specification
- Read the register map section from the spec (PDF, Excel, or inline table)
- Extract: register name, address offset, field name, width, access type, reset value
- Flag any registers in spec not present in `uvm_reg` model

### 2. Validate Register Model
- Cross-check `uvm_reg` model fields against spec table
- Report mismatches in: field width, access type, reset value

### 3. Generate Tests
- `csr_reset_test.sv`: Read all registers after reset, compare to spec reset value
- `csr_rw_test.sv`: Walking-ones for RW fields, verify RO fields are read-only
- `csr_reserved_test.sv`: Reserved fields always return 0 on read

### 4. Run and Report
- Run all CSR tests
- Report pass/fail per register
- Flag registers that failed with expected vs actual values

## Output Format

## CSR Compliance Report

### Summary
- Registers checked: [N]
- PASS: [N]
- FAIL: [N]
- MISMATCH (model vs spec): [N]

### Failures

| Register | Field | Expected | Got | Type |
|---------|-------|----------|-----|------|
| `CTRL_REG` | `enable` (reset) | `0x0` | `0x1` | Reset value mismatch |

### Model vs Spec Mismatches

| Register | Issue |
|---------|-------|
| `STATUS_REG.busy_flag` | Spec says RO, model says RW |

## Constraints
- Never modify RTL source files
- Flag mismatches as errors — do not auto-correct spec
- If spec is ambiguous, flag for human review
```

---

### The Coverage Closure Skill

```yaml
---
name: coverage-closure
description: |
  Analyze coverage holes and generate directed tests to close them.
  Use when user asks to: close coverage, fix coverage holes, improve
  functional coverage, write tests for uncovered bins, coverage closure.
  Proactively apply when: coverage report shows < 100% on any covergroup,
  after running /check-coverage command.
  Triggers: "coverage hole", "uncovered bin", "close coverage",
  "improve coverage", "100% coverage", "coverage miss", "uncovered assertion"
compatibility: Requires merged .ucdb or .vdb coverage database
metadata:
  category: verification
---

# Coverage Closure Skill

## Process

### 1. Parse Coverage Report
- Read merged coverage database or report file
- Identify all bins below threshold (default: < 100% functional, < 90% code)

### 2. Classify Holes
- **Reachable**: Bin is architecturally possible — needs a directed test
- **Unreachable**: Bin requires an impossible state combination — flag for exclusion
- **Spec-required**: Bin maps directly to a spec requirement — highest priority

### 3. Generate Directed Sequences
For each reachable hole:
- Identify the testbench stimulus path needed to hit the bin
- Write a directed `uvm_sequence` that targets that condition
- Add the sequence to the regression list

### 4. Verify Closure
- Re-run with new sequences
- Confirm bin is now covered
- Update coverage exclusion file for unreachable bins

## Output Format

## Coverage Closure Plan

### Holes to Close

| Covergroup | Bin | Priority | Directed Sequence |
|-----------|-----|---------|-------------------|
| `apb_trans_cg.rsp_type` | `SLVERR` | HIGH | `apb_slverr_inj_seq` |
| `ctrl_reg_cg.mode` | `LOOPBACK` | MED | `ctrl_loopback_seq` |

### Exclusions (Unreachable)

| Covergroup | Bin | Reason |
|-----------|-----|--------|
| `rx_cg.pkt_size` | `SIZE_0` | Spec prohibits zero-length packets |
```

---

### The Spec Extraction Skill

```yaml
---
name: spec-extraction
description: |
  Read a hardware specification and extract a structured verification test plan.
  Use when user asks to: read spec, extract requirements, create test plan from
  spec, parse specification, identify test scenarios from document.
  Proactively apply when: a PDF, DOCX, or Markdown spec file is attached or open.
  Triggers: "read the spec", "extract test plan", "what does the spec say about",
  "derive tests from spec", "spec requirements", "what should I test",
  "testplan from specification"
metadata:
  category: planning
---

# Spec Extraction Skill

## Process

### 1. Identify Spec Sections Relevant to Verification
- Functional description sections
- Register/CSR tables
- Protocol timing diagrams
- Error handling sections
- Corner cases and constraints listed in spec

### 2. Extract Verification Requirements
For each spec statement containing a requirement (shall, must, shall not):
- Extract the requirement text
- Classify: functional, performance, CSR, protocol, error handling
- Assign a test scenario name

### 3. Generate Test Plan Table

| Req ID | Spec Section | Requirement | Test Scenario | Priority |
|--------|------------|-------------|--------------|---------|
| REQ-001 | 3.2 | "FIFO shall never overflow when flow control is enabled" | `fifo_no_overflow_fc_test` | HIGH |

### 4. Identify Missing Stimulus
- List scenarios required by spec that have no existing sequence
- Suggest sequence names and brief implementation notes

## Constraints
- Never paraphrase requirements — quote directly from spec
- Flag ambiguous requirements for human review
- Do not invent requirements not stated in spec
```

---

### The Variable Initialization Skill

```yaml
---
name: var-initialization
description: |
  Generate correct variable initialization for UVM testbench components based
  on design spec and interface parameters.
  Use when user asks to: initialize testbench variables, set up interface
  parameters, configure DUT interface, initialize UVM config database,
  set up clocking blocks, configure virtual interface.
  Triggers: "initialize variables", "set up tb config", "configure interface",
  "uvm_config_db setup", "virtual interface init", "clocking block config",
  "testbench initialization", "init sequence"
metadata:
  category: setup
---

# Variable Initialization Skill

## Process

### 1. Identify Interfaces and Parameters
- List all DUT interfaces with direction and signal list
- Extract clock domains and reset polarities from spec
- Identify interface parameters (data width, address width, burst length)

### 2. Generate Parameter Initialization

```sv
// Interface parameters — match RTL spec Table 2.1
parameter int DATA_WIDTH  = 32;   // Spec §2.1: AXI data bus width
parameter int ADDR_WIDTH  = 32;   // Spec §2.1: AXI address bus width
parameter int ID_WIDTH    = 4;    // Spec §2.1: AXI transaction ID width
parameter int BURST_LEN   = 255;  // Spec §3.4: max burst length

// Clock periods (ns) — from timing spec Table 5.2
parameter real CLK_PERIOD_SYS  = 10.0;  // 100 MHz system clock
parameter real CLK_PERIOD_AHB  = 20.0;  // 50 MHz AHB clock
```

### 3. Generate uvm_config_db Initialization

```sv
// In top-level module or test base class build_phase:

// Virtual interface binding — must be set before run_phase
uvm_config_db #(virtual apb_if)::set(
  null, "uvm_test_top.env.apb_agent.*",
  "vif", apb_vif
);

// DUT parameters accessible from testbench
uvm_config_db #(int)::set(
  null, "uvm_test_top.*",
  "data_width", DATA_WIDTH
);
```

### 4. Generate Clocking Block Initialization

```sv
// Clocking block — aligns driver with clock edge per spec §5.1
clocking apb_drv_cb @(posedge clk);
  default input #1step output #1ns;
  output  PSEL, PENABLE, PWRITE, PADDR, PWDATA;
  input   PREADY, PRDATA, PSLVERR;
endclocking
```

### 5. CSR Register Block Initialization

```sv
// Register model initialization — call in test base build_phase
function void build_phase(uvm_phase phase);
  super.build_phase(phase);
  reg_model = ctrl_reg_block::type_id::create("reg_model", this);
  reg_model.build();
  reg_model.lock_model();
  reg_model.reset();  // Sets all fields to spec-defined reset values
  // Map to physical interface adapter
  reg_adapter = apb_reg_adapter::type_id::create("reg_adapter");
  reg_model.default_map.set_sequencer(
    env.apb_agent.sequencer, reg_adapter
  );
endfunction
```

## Output
Generate complete initialization file: `tb/tb_init_pkg.sv` with all parameters, `uvm_config_db` setup, and clocking block definitions.
```

---

## The Description Field: Make or Break

The `description` field is how the agent decides whether your skill is relevant.

### Weak vs Strong Descriptions

```yaml
# BAD: Invisible to discovery
description: "Helps with verification stuff"

# BAD: Jargon only
description: "Executes UVM register model compliance verification flow"

# GOOD: Natural language + triggers + proactive conditions
description: |
  Verify CSR (Control and Status Register) registers against specification.
  Use when user asks to: verify registers, check CSR compliance, test register
  map, run CSR tests, validate reset values, check field accessibility.
  Proactively apply when: register model files are open, spec section mentions
  CSR map.
  Triggers: "verify CSRs", "check register values", "CSR compliance",
  "reset value check", "register accessibility"
```

### Write in Third Person

```yaml
# ✅ Good
description: "Analyzes coverage holes and generates directed UVM sequences to close them"

# ❌ Bad
description: "I can help you close coverage holes"
```

### The Anatomy of a Good Verification Description

| Part | Example |
|------|---------|
| What it does | "Verify CSR registers against specification." |
| When to use | "Use when user asks to: verify registers, check CSR compliance" |
| Proactive triggers | "Proactively apply when: register model files are open" |
| Trigger phrases | "Triggers: 'verify CSRs', 'reset value check', 'field accessibility'" |

---

## Progressive Disclosure: Keep SKILL.md Lean

Main `SKILL.md`: the procedure (under 150 lines).  
`references/`: the detailed spec tables, register maps, methodology documents.  
`scripts/`: Python tools for parsing, generating, analyzing.

```markdown
# CSR Verification

## Quick Start
[Core procedure — 50 lines]

## Additional Resources
- Detailed CSR methodology: [references/CSR_CODING_GUIDE.md](references/CSR_CODING_GUIDE.md)
- Common CSR bugs: [references/COMMON_CSR_BUGS.md](references/COMMON_CSR_BUGS.md)
- Register map parser: [scripts/parse_reg_map.py](scripts/parse_reg_map.py)
```

---

## Testing Your Skills

**Test 1: Direct Invocation**
```
/csr-verification
```

**Test 2: Intent Matching — say the thing without the skill name**
```
"I need to verify all the registers in my APB block against the spec"
→ Should trigger csr-verification
```

**Test 3: Natural Language Triggers**
```
"There are coverage holes in the APB transaction covergroup" → Should trigger coverage-closure
"Can you read this spec section and tell me what tests I need?" → Should trigger spec-extraction
"How do I initialize the virtual interface in my testbench?" → Should trigger var-initialization
```

---

## Debugging Checklist

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Never activates | Description uses internal jargon only | Add natural trigger phrases |
| Works direct, not natural | No intent matching | Improve description with user language |
| Doesn't appear in settings | Invalid structure | Check folder/name match |
| Wrong skill activates | Conflicting descriptions | Make descriptions more specific |

---

# Part 4: Agents (Subagents)

## What Agents Are for Verification

Agents are specialized AI personas with their own context window. In verification, they are the "specialist colleague" you hand work to:

| Agent | Role | When to Spawn |
|-------|------|--------------|
| **SpecReader** | Reads spec sections, extracts requirements | "Analyze spec section 4.2" |
| **CsrChecker** | Validates register model against spec | "CSR compliance audit" |
| **CoverageAnalyzer** | Analyzes coverage reports, suggests new tests | "Why is coverage stuck at 73%?" |
| **TbReviewer** | Reviews TB code for UVM compliance | "Review my scoreboard" |
| **Critic** | Challenges testplan assumptions | "Challenge this test approach" |

The key insight: **agents start fresh**. After hours of debugging a protocol issue, a fresh CsrChecker has no anchoring on your wrong hypothesis — it looks at the register model with clean eyes.

---

## The Two Models: Persona Lens vs True Subagent

### Model 1: The Persona Lens (Same Context)

When you mention an agent in conversation:

```mermaid
flowchart TB
    subgraph SAME["SAME CONTEXT WINDOW"]
        User["'Ask the CsrChecker to review this'"]
        Context["Full TB conversation history"]
        Lens["CsrChecker Persona Applied"]
        LLM["Claude (Base Model)"]
        Output["Register compliance analysis"]
    end
    User --> Context --> Lens --> LLM --> Output
```

Fast, stateful, but anchored on your existing conversation. Good for quick consultations.

### Model 2: True Subagent (Isolated Context)

When the main agent delegates via Task tool:

```mermaid
flowchart TB
    subgraph PARENT["MAIN AGENT"]
        Main["Coordinating agent"]
    end

    subgraph SUB1["CSRCHECKER (Fresh Context)"]
        P1["Spec + Reg Model + Task"]
        C1["Claude Instance"]
        R1["CSR Compliance Report"]
        P1 --> C1 --> R1
    end

    subgraph SUB2["COVERAGEANALYZER (Fresh Context)"]
        P2["Coverage DB + Task"]
        C2["Claude Instance"]
        R2["Coverage Closure Plan"]
        P2 --> C2 --> R2
    end

    Main -->|"Audit CSRs"| SUB1
    Main -->|"Close coverage"| SUB2
    R1 -->|"Summary"| Main
    R2 -->|"Summary"| Main
```

Fresh context. No anchoring. Parallel-capable. Good for deep audits and independent verification.

---

## Designing Verification Agent Personas: Five Elements

### Element 1: Role

**Bad:**
```
You are a helpful verification assistant.
```

**Good:**
```
You are a senior hardware verification engineer specializing in CSR
register compliance. You read register specifications the way an auditor
reads financial statements — looking for inconsistencies, missing
accessibility modes, and spec-model mismatches.
```

### Element 2: Expertise

**Bad:**
```
You know about UVM and registers.
```

**Good:**
```
## Expertise
- UVM register model (`uvm_reg`, `uvm_reg_block`, `uvm_reg_map`)
- CSR field access types: RW, RO, WO, W1C, W1S, RSVD
- Register aliasing and shadowing patterns
- APB/AHB/AXI register access protocols
- Reset value verification and POR behavior
- Common CSR implementation bugs (reset-on-read, write-clear mismatches)
```

Specify what is NOT included: RTL design, analog IP, DFT. The agent has boundaries.

### Element 3: Process

**Bad:**
```
Analyze the register model carefully.
```

**Good:**
```
## Process

### 1. Parse Specification Table
- Extract all register entries: name, offset, fields, access type, reset value
- Flag registers in spec missing from model and vice versa

### 2. Validate Model Fields
- For each register: cross-check field width, bit position, access type, reset value
- Report exact mismatches with spec reference

### 3. Accessibility Check
- RW fields: confirm write + read-back works
- RO fields: confirm write has no effect
- W1C fields: confirm write-1-to-clear behavior
- RSVD fields: confirm reads return 0, writes have no effect

### 4. Generate Compliance Report
- Pass/fail per register
- Severity: CRITICAL (spec mismatch), WARNING (ambiguous), INFO (confirmed)
```

### Element 4: Output

**Bad:**
```
Provide a report.
```

**Good:**
```
## Output Format

## CSR Compliance Report: [Block Name]

### Summary
- Registers checked: [N] | PASS: [N] | FAIL: [N]
- Spec-model mismatches: [N]
- Critical issues: [N]

### Critical Issues
1. **[REG_NAME].[FIELD]**
   - Spec: [access_type], reset=0x[value]
   - Model: [access_type], reset=0x[value]
   - Impact: [test will fail / DUT may behave incorrectly]
   - Fix: [exact change needed in reg model]

### Warnings
[Same format, lower severity]

### Clean Registers
[N] registers match specification exactly.
```

### Element 5: Constraints

**Bad:**
```
Be careful with your findings.
```

**Good:**
```
## Constraints
- Never modify RTL source — CsrChecker is read-only
- Never assume a spec is correct when model and spec disagree — flag both
- If spec is ambiguous (e.g., says "cleared on read" but doesn't specify), flag for human review
- Do not generate test code unless explicitly asked
- Prioritize findings that will cause simulation failures before style issues
```

---

## Complete Agent Definitions

### SpecReader Agent

```yaml
# .cursor/agents/spec-reader.md
---
name: SpecReader
model: claude-sonnet-4-20250514
description: |
  # Specification Reader

  You are a senior verification engineer who reads hardware specifications
  with precision. You extract verification requirements, identify testable
  conditions, and generate structured test plans.

  ## Role
  Extract what needs to be verified from prose, tables, and diagrams
  in a hardware specification. Think like a verification lead reviewing
  a spec before writing a testplan.

  ## Expertise
  - Hardware specification interpretation (PDF, DOCX, Markdown)
  - Functional requirement extraction (shall/must/should statements)
  - Protocol specification reading (APB, AHB, AXI, I2C, SPI, UART)
  - CSR/register map table interpretation
  - Timing constraint extraction

  ## Process

  ### 1. Identify Scope
  - Which section(s) of the spec are relevant?
  - What is the DUT boundary?

  ### 2. Extract Requirements
  - Find all "shall", "must", "shall not", "must not" statements
  - Classify: functional, protocol timing, CSR, error handling, performance

  ### 3. Build Test Plan Table

  | Req ID | Spec Ref | Requirement (verbatim) | Test Scenario | Priority |
  |--------|---------|----------------------|--------------|---------|
  | REQ-001 | §3.2 | "FIFO shall never overflow when..." | `fifo_no_overflow_fc_test` | HIGH |

  ### 4. Identify Gaps
  - List requirements with no proposed test scenario
  - Flag ambiguous requirements for clarification

  ## Output Format

  ## Verification Test Plan: [Module Name]

  ### Requirements Summary
  - Total requirements extracted: [N]
  - HIGH priority: [N]
  - MED priority: [N]
  - Ambiguous (need clarification): [N]

  ### Test Plan Table
  [Full table as above]

  ### Open Questions
  1. [Ambiguous requirement] — need clarification on [specific point]

  ## Constraints
  - Quote requirements verbatim — never paraphrase
  - Do not invent requirements not in the spec
  - Flag ambiguous text rather than interpreting it
  - Priority = HIGH if requirement uses "shall" or "must"
---
```

---

### CsrChecker Agent

```yaml
# .cursor/agents/csr-checker.md
---
name: CsrChecker
model: claude-sonnet-4-20250514
readonly: true
description: |
  # CSR Compliance Checker

  You are a senior hardware verification engineer specializing in register
  model compliance. You compare UVM register models against hardware
  specifications with the precision of an auditor.

  ## Role
  Find every mismatch between the register specification and the UVM
  register model implementation. You think about what will cause
  simulation failures — not just what looks different.

  ## Expertise
  - UVM register model: uvm_reg, uvm_reg_block, uvm_reg_map, uvm_reg_field
  - CSR field access types: RW, RO, WO, W1C, W1S, RC, RS, WARL, WLRL, RSVD
  - Reset value verification and POR (Power-On Reset) behavior
  - Register aliasing and mirrored value tracking
  - Common implementation bugs: reset-on-read, write-clear vs write-set mismatch

  ## Process

  1. Parse spec register table — extract: name, offset, field, bits, access, reset
  2. Parse UVM model — extract same fields from uvm_reg definitions
  3. Cross-check field by field — flag every mismatch
  4. Accessibility audit — verify access type behavior matches spec
  5. Reserved field audit — verify RSVD fields are properly constrained
  6. Generate compliance report

  ## Output Format

  ## CSR Compliance Report: [reg_block_name]

  ### Summary
  Registers checked: [N] | PASS: [N] | FAIL: [N] | CRITICAL: [N]

  ### Critical Issues (Simulation Will Fail)
  1. **[REG].[FIELD]**
     - Spec: [type], reset=0x[val]
     - Model: [type], reset=0x[val]
     - Fix: [exact code change in uvm_reg definition]

  ### Warnings (Behavior May Differ)
  [Same format]

  ## Constraints
  - Read-only — never modify RTL or register model
  - If spec is ambiguous, flag for human review — do not interpret
  - Always cite the spec table row/column for every finding
  - Prioritize findings that cause UVM_ERROR in simulation
---
```

---

### CoverageAnalyzer Agent

```yaml
# .cursor/agents/coverage-analyzer.md
---
name: CoverageAnalyzer
model: claude-sonnet-4-20250514
description: |
  # Coverage Analyzer

  You analyze functional and code coverage reports to identify holes,
  propose directed tests, and recommend exclusions for unreachable bins.

  ## Role
  Act as the verification engineer who owns coverage closure. Find
  every uncovered bin, understand why it's uncovered, and propose
  the minimum set of directed tests to close it.

  ## Expertise
  - UVM functional coverage: covergroup, coverpoint, cross, bins
  - SystemVerilog code coverage: line, branch, toggle, FSM state
  - Coverage exclusion methodology
  - Directed test stimulus design for coverage closure
  - Recognizing unreachable bins (architectural impossibilities)

  ## Process

  1. Parse coverage report — identify all bins below threshold
  2. Classify each hole: reachable, unreachable, spec-required
  3. For reachable holes: design directed stimulus sequence
  4. For unreachable holes: write exclusion with justification
  5. Prioritize: spec-required > corner cases > random

  ## Output Format

  ## Coverage Closure Plan

  ### Hole Classification

  | Covergroup.Bin | Current | Type | Action |
  |---------------|---------|------|--------|
  | `apb_trans_cg.rsp.SLVERR` | 0% | Reachable | Add `apb_slverr_inj_seq` |
  | `rx_cg.len.SIZE_0` | 0% | Unreachable | Exclude — spec §2.1 prohibits zero-length |

  ### Directed Sequences to Write

  | Sequence Name | Covers | Priority |
  |--------------|--------|---------|
  | `apb_slverr_inj_seq` | APB SLVERR response | HIGH |

  ## Constraints
  - Never mark a bin as unreachable without citing the spec
  - Unreachable exclusions must have a comment matching the pattern:
    `// EXCLUDE: <spec_reference> — <reason>`
  - Propose minimum sequences to close maximum bins
---
```

---

### TbReviewer Agent

```yaml
# .cursor/agents/tb-reviewer.md
---
name: TbReviewer
model: claude-sonnet-4-20250514
description: |
  # Testbench Code Reviewer

  You review SystemVerilog UVM testbench code for correctness, methodology
  compliance, and coverage completeness.

  ## Role
  Review testbench code the way a senior verification engineer reviews a
  colleague's work before a project milestone. Find bugs, methodology
  violations, and missing coverage — before simulation does.

  ## Expertise
  - UVM methodology (OVM/UVM 1.2): phases, factory, config_db, TLM ports
  - SystemVerilog: interfaces, clocking blocks, modports, assertions
  - Scoreboard design: reference models, in-order vs out-of-order checking
  - Functional coverage: covergroup placement and bin completeness
  - Common TB bugs: race conditions, driver-monitor coupling, phase ordering

  ## Process

  1. Identify TB component type (env, agent, driver, monitor, scoreboard, sequence, test)
  2. Check structural correctness: factory registration, phase calls, port connections
  3. Check methodology compliance: passive monitor, config_db usage, UVM macros
  4. Check coverage completeness: are all interesting conditions covered?
  5. Check for common bugs: time-zero races, missing uvm_status checks, unbounded loops

  ## Output Format

  ## TB Code Review: [filename]

  ### Summary
  Component type: [env/agent/driver/monitor/scoreboard/sequence/test]
  Overall: ✅ Clean | ⚠️ Warnings | ❌ Errors

  ### ❌ Errors (Will Cause Simulation Failure)
  1. **[Issue]** — Line [N]
     - Problem: [what is wrong]
     - Fix: [exact code change]

  ### ⚠️ Warnings (Methodology Violations)
  [Same format]

  ### 💡 Suggestions (Coverage / Style)
  [Same format]

  ## Constraints
  - Cite line numbers for every finding
  - Distinguish between "will cause failure" and "style issue"
  - Do not suggest changes to RTL/DUT
  - If unsure about intent, ask rather than assume
---
```

---

## Persona Patterns for Verification

### The Specialist (CsrChecker, CoverageAnalyzer)
Deep expertise in one domain. Stays in lane.
```
Role: Senior CSR verification engineer
Expertise: Deep — register models, CSR access types, reset behavior
Process: Systematic audit: spec → model → compliance report
Output: Findings table with exact spec references
Constraints: Read-only. Cites spec. Flags ambiguity.
```

### The Investigator (Debugger, SpecReader)
Gathers evidence, forms hypotheses. Never guesses.
```
Role: Senior verification engineer reading spec like a detective
Expertise: Requirement extraction, protocol understanding
Process: Observe spec → extract requirements → classify → propose tests
Output: Structured test plan with evidence for every requirement
Constraints: Quote verbatim. Never paraphrase. Flag ambiguity.
```

### The Contrarian (Critic)
Challenges testplan assumptions before they become expensive bugs.
```
Role: Devil's advocate for testplan review
Expertise: Failure mode recognition, missed corner cases
Process: Steelman → challenge → stress-test → improve
Output: List of unchallenged assumptions and missing test scenarios
Constraints: Constructive — every challenge must come with a suggestion
```

### The Producer (TestGenerator, Documenter)
Creates artifacts: sequences, coverage groups, test plans.
```
Role: Senior verification engineer generating reusable TB components
Expertise: UVM sequence/coverage patterns, spec interpretation
Process: Gather requirements → draft → refine → validate
Output: Complete .sv files ready for integration
Constraints: Matches existing TB coding style. No assumptions about DUT.
```

---

## Common Persona Mistakes in Verification Context

**1. Too Broad**
```
# Bad: Does everything
You are an expert at UVM, SystemVerilog, coverage, CSR verification,
protocol analysis, formal verification, and power analysis.
```
Fix: Pick one domain per agent. Spawn CsrChecker and CoverageAnalyzer separately.

**2. No Process**
```
# Bad: How does it work?
Analyze the testbench thoroughly and provide feedback.
```
Fix: Define numbered steps — "1. Identify component type. 2. Check factory registration. 3. Check phase calls..."

**3. Vague Output**
```
# Bad: What does the output look like?
Provide a detailed review.
```
Fix: Show the exact markdown template with required sections (Errors, Warnings, Suggestions).

**4. Missing Constraints for Read-Only Agents**
```
# Bad: No boundaries
Review the register model for issues.
```
Fix: Explicitly add `- Never modify RTL source — CsrChecker is read-only`

---

# Part 5: Smart Routing for Verification

## The Routing Rule

```yaml
# .cursor/rules/agent-routing/RULE.md
---
description: "Routes verification tasks to appropriate specialized agents based on task patterns"
alwaysApply: true
---

# Verification Agent Routing

When a user request matches one of these patterns, spawn the appropriate agent.

## Agent Selection Guide

| Task Pattern | Agent | When to Spawn |
|-------------|-------|---------------|
| "Read this spec section" | SpecReader | Specification parsing |
| "Extract tests from spec" | SpecReader | Test plan creation |
| "Verify CSR registers" | CsrChecker | CSR compliance audit |
| "Check reset values" | CsrChecker | Register reset verification |
| "Why is coverage stuck?" | CoverageAnalyzer | Coverage closure |
| "What bins are uncovered?" | CoverageAnalyzer | Coverage analysis |
| "Review my scoreboard" | TbReviewer | TB code review |
| "Check this sequence" | TbReviewer | Methodology compliance |
| "Challenge this testplan" | Critic | Testplan validation |
| "Debug this UVM_ERROR" | Debugger | Error investigation |
| "Why is this assertion failing?" | Debugger | Assertion debug |
| "Generate coverage groups" | TestGenerator | Coverage creation |
| "Write a directed test" | TestGenerator | Test authoring |

## Multi-Pattern Detection

### CSR + Coverage (Sequential)
Patterns: "verify CSRs" + "improve coverage"
Action: CsrChecker first (find mismatches), then CoverageAnalyzer (close remaining holes)

### Spec + Implementation (Sequential)
Patterns: "read spec then write test"
Action: SpecReader first (extract requirements), then main agent implements

### Full Review (Parallel)
Patterns: "review everything before tape-in"
Action: Spawn TbReviewer + CoverageAnalyzer + CsrChecker in parallel

## Spawn Behavior

- Provide relevant context: spec section, register block, coverage report path
- Let specialist complete analysis
- Synthesize findings back to user with priority ordering
```

---

## Pattern Matching in Practice

```
User Input                                        → Agent Selected
─────────────────────────────────────────────────────────────────────
"read section 4.2 of the spec and tell me what to test"  → SpecReader
"the CTRL_REG reset value looks wrong"                   → CsrChecker
"coverage is stuck at 73% on the APB covergroup"         → CoverageAnalyzer
"review my APB scoreboard for correctness"               → TbReviewer
"I think we're missing burst tests"                      → Critic
"UVM_ERROR from status check in csr_rw_test"             → Debugger
"write a directed sequence to hit the SLVERR bin"        → TestGenerator
```

### Compound Patterns

**Full pre-tapeout review:**
```
User: "Review everything before we tape in"

Detection:
- "review" + "everything" → multi-agent review needed

Action: Parallel spawn of TbReviewer + CoverageAnalyzer + CsrChecker
```

**Spec → test implementation:**
```
User: "Read spec section 5.3 and then write the test"

Detection:
- "Read spec" → SpecReader
- "write the test" → Implementation task

Action: Sequential — SpecReader first, then main agent implements
```

---

## Spawn Patterns

### Sequential: Spec → Implementation

```mermaid
flowchart LR
  SR["SpecReader\nextract requirements"]:::primary
  M["Main Agent\nwrite sequences"]:::primary
  TR["TbReviewer\nvalidate TB code"]:::primary

  SR --> M --> TR
```

### Parallel: Full Review

```mermaid
flowchart TB
  M1["Main Agent\ncoordinates review"]:::primary

  TB["TbReviewer\ncode quality"]:::agent
  CSR["CsrChecker\nregister compliance"]:::agent
  COV["CoverageAnalyzer\ncoverage holes"]:::agent

  M2["Main Agent\nsynthesized findings"]:::primary

  M1 --> TB & CSR & COV
  TB & CSR & COV --> M2
```

### Background: Long Coverage Analysis

```mermaid
flowchart TB
  CA["CoverageAnalyzer\nruns in background"]:::agent
  F["Coverage closure plan ready\nnotifies when done"]:::accent
  CA --> F
```

---

## Natural Language Triggers

```
Phrase                                    → Implied Agent/Command
─────────────────────────────────────────────────────────────────
"commit this testbench work"             → /tb-checkpoint
"the sim is showing a UVM_ERROR"         → Debugger
"can you take a look at my scoreboard?"  → TbReviewer
"make sure the CSRs are correct"         → CsrChecker
"I'm not sure we're testing this right"  → Critic
"what would break if we change the FSM?" → Critic
"walk me through this spec section"      → SpecReader
"get this ready for regression"          → TbReviewer + /tb-checkpoint
```

---

# Part 6: Testing Verification Artifacts

## The Artifact Testing Pyramid

```mermaid
flowchart TB
    subgraph pyramid["Artifact Testing Pyramid"]
        BT["Behavioral Tests\nDoes the agent produce correct CSR findings?"]
        CT["Content Tests\nAre process steps actionable?"]
        ST["Structural Tests\nIs format correct?"]
    end
    BT --> CT --> ST
```

---

## Structural Tests

### For Rules (Verification Context)
```
✓ Has YAML frontmatter
✓ Has 'description' field (non-empty)
✓ Has 'globs' or 'alwaysApply'
✓ Globs match the right file types (*.sv, *.svh, *_reg_block*)
✓ Markdown body with at least one heading
```

### For Commands (Verification Context)
```
✓ NO YAML frontmatter
✓ Title: "# /command-name - Description"
✓ Has "## Instructions" section
✓ Default behavior documented (which test? which seed?)
✓ Has variants for common options (--test, --seed, --no-compile)
```

### For Agents (Verification Context)
```
✓ Has YAML frontmatter with name and model
✓ Description has Role section
✓ Description has Expertise section (specific — not generic "knows about UVM")
✓ Description has Process section with numbered steps
✓ Description has Output Format section with explicit template
✓ Description has Constraints section (includes read-only constraints)
```

---

## Content Tests

### Actionable Instructions
```
✓ Process steps contain verification verbs:
  "parse", "extract", "compare", "flag", "generate", "classify"
✗ No vague phrases:
  "analyze carefully", "be thorough", "check everything"
✓ Steps reference specific UVM classes or spec artifacts
✓ Examples use realistic register names, field names, signal names
```

### Description Quality for Verification Skills
```
✓ Includes natural language trigger phrases users actually say
✓ References specific protocols/standards (APB, UVM, CSR)
✓ Specifies tool requirements in 'compatibility' field
✓ Includes proactive trigger conditions (e.g., "when reg model file is open")
```

---

## Behavioral (Golden) Tests

### Golden Test: CsrChecker Agent

```yaml
# .cursor/tests/csr-checker.golden.yaml
artifact: .cursor/agents/csr-checker.md
scenarios:
  - name: "reset_value_mismatch"
    input: |
      Check this register model against the spec:
      Spec: CTRL_REG.enable, RW, reset=0x0
      Model: `uvm_field_int(enable, UVM_ALL_ON)` with reset value 0x1
    expected_contains:
      - "CTRL_REG"
      - "reset"
      - "mismatch"
      - "0x0"
      - "0x1"
    expected_not_contains:
      - "modify"
      - "change the RTL"

  - name: "ro_field_marked_rw"
    input: |
      Spec says STATUS_REG.busy is RO (read-only).
      Model defines it as uvm_reg_field with UVM_RW access.
    expected_contains:
      - "STATUS_REG"
      - "busy"
      - "RO"
      - "RW"
      - "mismatch"
    expected_format:
      has_sections:
        - "CSR Compliance Report"
        - "Critical Issues"

  - name: "all_clean"
    input: |
      Spec: DATA_REG.payload, RW, 32-bit, reset=0x0
      Model: `uvm_field_int(payload, UVM_ALL_ON)` 32-bit, reset=0x0
    expected_contains:
      - "match"
    expected_not_contains:
      - "CRITICAL"
      - "mismatch"
```

### Golden Test: Coverage Closure Skill

```yaml
# .cursor/tests/coverage-closure.golden.yaml
artifact: .cursor/skills/coverage-closure/SKILL.md
scenarios:
  - name: "uncovered_bin"
    input: |
      Coverage report shows: apb_trans_cg.response_type bin SLVERR: 0/1 (0%)
    expected_contains:
      - "SLVERR"
      - "directed"
      - "sequence"
    expected_not_contains:
      - "exclude"   # SLVERR is reachable, should not be excluded

  - name: "unreachable_bin"
    input: |
      Coverage report shows: rx_cg.pkt_len bin SIZE_0: 0/1 (0%)
      Note: Spec section 2.1 states zero-length packets are prohibited.
    expected_contains:
      - "unreachable"
      - "exclude"
      - "spec"
    expected_not_contains:
      - "directed test"  # unreachable bins should not get directed tests
```

---

## Validation Checklists

### Rules (Verification)
- [ ] YAML frontmatter is valid
- [ ] Globs target correct file patterns (`*.sv`, `**/tb/**`)
- [ ] Instructions contain verification-specific verbs
- [ ] At least one concrete SystemVerilog example
- [ ] No vague phrases ("write clean code", "be careful")
- [ ] No conflicting guidance with other TB rules

### Commands (Verification)
- [ ] NO frontmatter
- [ ] Title: `# /command-name - Description`
- [ ] Has `## Instructions` section
- [ ] Default behavior states: which test, which seed, which scope
- [ ] Variants cover --test, --seed, --no-compile as appropriate
- [ ] Output format shows UVM pass/fail + coverage percentage

### Agents (Verification)
- [ ] Role is specific: "senior CSR verification engineer" not "helpful assistant"
- [ ] Expertise lists specific standards: UVM, OWASP is irrelevant — use AMBA, RFC, STRIDE per domain
- [ ] Process has numbered steps referencing verification artifacts
- [ ] Output template has a table with columns relevant to verification (Register, Field, Expected, Got)
- [ ] Constraints include read-only if agent should not modify DUT
- [ ] Constraints specify when to flag for human review vs auto-report

---

# Part 7: Meta-Learning for Verification

## The Learning Loop

```mermaid
flowchart LR
    subgraph LOOP["Continuous Improvement Loop"]
        O["OBSERVE\nwhat's repeated"]:::primary
        P["PATTERN\nwhat should be automated"]:::secondary
        PR["PROPOSE\nnew command/skill/rule"]:::secondary
        T["TEST\ndoes it help?"]:::secondary
        D["DEPLOY\napply and monitor"]:::accent
    end
    O --> P --> PR --> T --> D
    D --> O
```

---

## What to Observe in Verification Workflows

### 1. Repeated Manual Simulation Steps

```
Manual Action Tracking:
  "vlog +define+UVM_NO_DEPRECATED tb/*.sv": 8 times
  "vsim -c -do 'coverage save; quit'": 6 times
  "manually checking reset values in register model": 5 times

Patterns:
  - Manual compile → automate into /run-sim
  - Manual coverage save → add to /run-sim default behavior
  - Manual reset value check → CSR verification skill needed
```

### 2. Repeated Questions About Spec

```
Question Tracking:
  "what is the reset value of CTRL_REG.enable?": 3 times
  "is STATUS_REG.busy read-only or read-write?": 4 times
  "what does the spec say about back-to-back writes?": 2 times

Patterns:
  - Repeated register questions → create spec-extraction skill
  - Repeated access-type questions → CSR rule with register table reference
  - Protocol questions → create protocol reference in skill references/
```

### 3. Coverage Analysis Patterns

```
Command Tracking:
  /run-sim: 15 uses, 2 failures (missing test name argument)
  /check-coverage: 7 uses, 0 failures
  /generate-csr-test: 1 use (underused despite CSR-heavy project)

Patterns:
  - /run-sim failures → improve error handling for missing UVM_TESTNAME
  - /generate-csr-test underused → improve skill discoverability or routing
```

### 4. Agent Effectiveness

```
Agent Tracking:
  CsrChecker:
    spawned: 6 times
    helpful: 6 times
    verdict: KEEP

  SpecReader:
    spawned: 1 time
    should_have_spawned: 4 times (user manually read spec and asked questions)
    verdict: IMPROVE ROUTING (add "read this section" trigger)

  CoverageAnalyzer:
    spawned: 0 times
    should_have_spawned: 3 times (user described coverage holes manually)
    verdict: LOWER TRIGGER THRESHOLD — add "stuck at X%" trigger
```

---

## Pattern Thresholds

```
manual_action_repeated:
  threshold: 3+ times
  action: Propose command or automation rule

spec_question_repeated:
  threshold: 2+ times
  action: Propose spec-extraction skill or CSR rule

agent_not_spawned_when_useful:
  threshold: 2+ times
  action: Adjust routing patterns or lower trigger threshold

rule_always_overridden:
  threshold: 3+ times
  action: Review rule — coding standard may be wrong for this project

command_failure_repeated:
  threshold: 2+ times
  action: Improve command error handling or default argument handling
```

---

## Session Analysis Example

**User:** `/analyze-session --propose`

MetaAnalyzer examines the session and produces:

| # | Opportunity | Evidence | Proposal | Impact |
|---|------------|---------|---------|--------|
| 1 | Auto-compile rule (IMMEDIATE) | `vlog` run manually 8 times | Add auto-compile to autonomous-workflows | ~16 manual commands saved per session |
| 2 | Spec extraction skill trigger (HIGH) | SpecReader not spawned 4 times when reading spec manually | Add "what does the spec say" routing trigger | Automated spec reading |
| 3 | CSR coverage — missing generated tests (MED) | /generate-csr-test used 1×, 3× CSR bugs found in review | Improve /generate-csr-test discoverability | Catch CSR bugs earlier |
| 4 | CoverageAnalyzer routing threshold (MED) | 3 times user described coverage holes without spawning agent | Lower routing trigger — add "stuck at" pattern | Automated coverage analysis |

---

## Automated vs Manual Learning for Verification

| Type | Examples |
|------|---------|
| **Auto-Apply (Safe)** | Update observation counters, flag repeated patterns |
| **Propose and Wait (Default)** | New routing rules, new command variants, updated skill descriptions |
| **Manual Only (Risky)** | Delete CSR rules, modify security/access policies, change golden reference values |

---

# Appendix: Quick Reference

## Full Comparison Table

| | Rules | Skills | Commands | Agents |
|--|-------|--------|---------|-------|
| **What it is** | Passive guidance | Active verification procedure | Saved simulation workflow | Specialized verification persona |
| **Purpose** | TB coding standards, access policies | CSR verification, coverage closure, spec extraction | `/run-sim`, `/check-coverage`, `/tb-checkpoint` | SpecReader, CsrChecker, CoverageAnalyzer, TbReviewer |
| **Activation** | Always on or glob-triggered | Agent decides or `/skill-name` | **User only — always manual** | Spawned by main agent |
| **Context loading** | Loaded every matching conversation | Progressive — description first, full contents on demand | Injected when triggered | Fresh isolated context window |
| **Best for** | Naming conventions, methodology enforcement | Multi-step verification procedures | Repeatable simulation tasks | Deep audits requiring fresh unbiased perspective |
| **Think of it as** | Your methodology handbook | Your verification procedure library | Your simulation shortcuts | Your specialist colleagues |

## The Three Questions

1. **Does it tell the agent how to write verification code?** → Rule
2. **Does it tell the agent how to run a verification procedure?** → Skill
3. **Is it a simulation workflow you're tired of typing?** → Command
4. **Does it require deep focused expertise or a fresh unbiased perspective?** → Agent

## Key Verification Principles

| Principle | Application |
|-----------|------------|
| Rules guide, Skills do, Commands trigger | Use rules for standards; skills for CSR/coverage workflows; commands for /run-sim |
| The 2+2 test | If "what does this SV syntax mean?" doesn't need the CSR access rule, don't alwaysApply it |
| Description is discovery | Skills are only as findable as their descriptions — include natural phrases like "what could go wrong" |
| Monitors are passive | Never drive interface signals from a monitor — this is a rule, not a skill |
| Fresh context is a superpower | CsrChecker started fresh has no anchoring on your wrong hypothesis about a reset value |
| Read-only agents protect the DUT | CsrChecker, CoverageAnalyzer — mark `readonly: true` — verification engineers never edit RTL |
| Quote specs verbatim | SpecReader must quote, not paraphrase — ambiguity gets flagged, not interpreted |
| Propose, don't auto-apply | MetaAnalyzer proposes routing improvements — human accepts before they change behavior |

## Verification Skill Categories

| Category | Skills |
|---------|--------|
| `verification` | csr-verification, coverage-closure, assertion-analysis |
| `planning` | spec-extraction, testplan-generation, risk-assessment |
| `setup` | var-initialization, tb-config, reg-model-init |
| `workflow` | run-regression, coverage-merge, waveform-analysis |

---

*Tailored for hardware verification engineers working with SystemVerilog/UVM testbenches, CSR register verification, specification reading, and coverage closure. Concepts sourced from agenticthinking.ai and adapted with verification-domain examples.*
