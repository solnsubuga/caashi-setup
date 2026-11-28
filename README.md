# Caashi setup

Caashi observes every `run:` step in a GitHub Actions job and, once a
repository is in enforce mode, reuses results that a trusted run already
verified on identical inputs. New repositories start in **shadow mode**:
every step runs, and the job summary and the Caashi check say how much of
the run was identical to verified work. Caashi never fails your job.

```yaml
jobs:
  test:
    runs-on: ubuntu-24.04
    permissions:
      contents: read
      id-token: write        # lets the job prove which repository and branch it is
    steps:
      - uses: actions/checkout@v5
      - uses: solnsubuga/caashi-setup@<commit sha>   # everything after this line is observed
      - run: npm ci
      - run: npm test
```

Pin a commit SHA from this repository's history. It needs the Caashi GitHub
App installed on the repository and a Linux x64 or arm64 runner; anywhere
else it turns itself off with a warning and steps run as normal.

- **Inputs:** `mode` (`auto`, `shadow`, `off`), `secrets`
  (`${{ toJSON(secrets) }}`, used on the runner only), `server-url`,
  `upload-deadline-seconds`, and `agent-version`, `agent-sha256`,
  `agent-url`, `agent-path` to run another agent build. See `action.yml`.
- **Planner:** `solnsubuga/caashi-setup/plan@<commit sha>` tells later jobs
  which ones a pull request cannot reach.
- **Agent:** each release of the `caashi` agent is attached to this
  repository's `agent-v*` releases. The Action downloads the one it pins and
  refuses it unless its SHA-256 matches.

This repository is published automatically from Caashi's source on every
change; pull requests here are not merged. Commit `76b57364ea05bd121ccc823ba44add229783c1d3`.
