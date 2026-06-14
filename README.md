<h1 align="center">gha.mq</h1>

A [GitHub Actions](https://docs.github.com/en/actions) workflow utility module implemented for [mq](https://github.com/harehare/mq).

## Features

- Parse workflow YAML files
- Inspect jobs, steps, triggers, permissions, and environment variables
- Find steps by action reference (`uses`)
- Extract `workflow_dispatch` inputs and matrix strategies
- Render workflow summaries as Markdown tables

## Installation

Copy `gha.mq` to your mq module directory, or reference it with `-L`.

```sh
cp gha.mq ~/.local/mq/config/
```

## Usage

```sh
mq -L /path/to/modules -I raw \
  'import "gha" | gha::gha_parse(.) | gha::gha_job_names(.)' workflow.yml
```

## API

### Parsing

#### `gha_parse(input)`

Parses a GitHub Actions workflow YAML string and returns the parsed structure.

### Workflow-level

| Function | Description |
|---|---|
| `gha_name(workflow)` | Workflow name |
| `gha_triggers(workflow)` | Trigger events (`on:`) |
| `gha_permissions(workflow)` | Top-level permissions dict |
| `gha_inputs(workflow)` | `workflow_dispatch` inputs dict |

### Jobs

| Function | Description |
|---|---|
| `gha_jobs(workflow)` | All jobs as a dict |
| `gha_job_names(workflow)` | Array of job names |
| `gha_job_needs(job)` | Upstream job dependencies |
| `gha_matrix(job)` | Matrix strategy dict, or None |
| `gha_permissions(job)` | Job-level permissions dict |
| `gha_env(job)` | Job-level env dict |

### Steps

| Function | Description |
|---|---|
| `gha_steps(job)` | Steps array for a job |
| `gha_uses(step)` | `uses` action reference |
| `gha_run(step)` | `run` shell command |
| `gha_step_name(step)` | Step name |
| `gha_step_with(step)` | `with` inputs dict |
| `gha_env(step)` | Step-level env dict |

### Serialization

| Function | Description |
|---|---|
| `gha_stringify(workflow)` | Convert a parsed workflow dict back to a YAML string |

### Search & Rendering

| Function | Description |
|---|---|
| `gha_find_steps_by_uses(workflow, pattern)` | Find steps whose `uses` matches a regex |
| `gha_to_markdown_table(workflow)` | Markdown table of all jobs |

## Example

Given `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: ["main"]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Test
        run: cargo test
```

```sh
# List job names
mq -L . -I raw 'import "gha" | gha::gha_parse(.) | gha::gha_job_names(.)' ci.yml
# => ["build"]

# Find all checkout steps
mq -L . -I raw 'import "gha" | gha::gha_parse(.) | gha::gha_find_steps_by_uses(., "actions/checkout")' ci.yml

# Render a Markdown summary
mq -L . -I raw 'import "gha" | gha::gha_parse(.) | gha::gha_to_markdown_table(.)' ci.yml
```

## License

MIT
