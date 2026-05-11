# gha-workflows Repository Instructions

## Overview

This is a **reusable GitHub Actions workflows repository**. All workflows (except those marked as local) are designed to be called from other repositories using the `uses` keyword in workflow files.

## Critical Principles

### 1. Reusability Constraint

- **All workflows MUST work when called from external repositories**
- Assume workflows will be called from different repos with different structures, configurations, and contexts
- Avoid assumptions about directory structures, environment setup, or repo-specific configurations
- Always document required inputs, secrets, and context expectations

### 2. Local vs. Reusable Workflows

**Local workflows** (run in this repository only):

- Naming convention: `*-local.yml` suffix
- Examples: `automerge-prs-local.yml`
- Can use repo-specific configurations and assumptions
- Used for repository maintenance (CI/CD, automation, etc.)

**Reusable workflows** (called from other repositories):

- NO `-local` suffix in filename
- Examples: `automerge-prs.yml`, `build-and-push-image.yml`, `build-image-pr.yml`
- Must be self-contained and configurable via inputs/secrets
- Must work from any repository that calls them

### 3. Workflow Structure Requirements

For reusable workflows, ensure:

- Clear documentation of required `inputs` and `secrets`
- Sensible defaults where possible
- Explicit error handling and validation
- Logging that helps callers debug issues
- No hardcoded repo-specific paths or values

## Repository Structure

```
.github/
  workflows/
    *-local.yml          # Workflows for this repo only
    *.yml                # Reusable workflows
package.json             # Dependencies (semantic-release)
README.md                # Repository documentation
```

## Key Technologies

- **semantic-release**: Automated versioning and publishing (v25.0.3)
- **@semantic-release/github**: GitHub integration for releases (v12.0.8)

## Naming Conventions

1. **Workflow files**: kebab-case (e.g., `build-and-push-image.yml`)
2. **Local workflows**: Add `-local` suffix (e.g., `automerge-prs-local.yml`)
3. **Job names**: kebab-case
4. **Step names**: Clear, descriptive lowercase with hyphens

## Before Making Changes

### When Adding/Modifying a Reusable Workflow

- [ ] Verify all paths are relative or configurable (no hardcoded paths)
- [ ] Document all required `inputs` and `secrets` in YAML comments
- [ ] Include sensible defaults for optional inputs
- [ ] Test from another repository to ensure it works
- [ ] Add usage example comments to the workflow file
- [ ] Ensure error messages are clear for external callers

### When Adding/Modifying a Local Workflow

- [ ] Ensure filename includes `-local` suffix
- [ ] Repo-specific configurations are acceptable here
- [ ] Still maintain clear documentation for future reference

### When Modifying Existing Workflows

- [ ] Check if it's reusable or local (check filename)
- [ ] For reusable: Ensure changes don't break existing callers
- [ ] Consider backward compatibility with semantic versioning
- [ ] Update documentation of inputs/secrets if changed

## Testing Recommendations

For reusable workflows:

1. Test locally in this repo using a local workflow that calls it
2. Test calling from another test repository
3. Verify with different input variations
4. Test error scenarios

## Common Pitfalls to Avoid

- ❌ Hardcoding repository names or paths
- ❌ Assuming specific GitHub Actions secrets exist in caller repos
- ❌ Using context variables that don't exist for called workflows
- ❌ Forgetting to document required inputs/secrets
- ❌ Breaking changes without version management
- ❌ Mixing local repo logic with reusable workflow logic

## Documentation Template

Add this to the top of reusable workflows:

```yaml
# Reusable workflow: [Description]
#
# Usage in other repos:
#   - uses: tkhom3/gha-workflows/.github/workflows/[filename].yml@[version]
#     with:
#       [input1]: value
#     secrets:
#       [secret1]: value
#
# Inputs:
#   [input1]: [description]
#
# Secrets:
#   [secret1]: [description]
```

## Repository Maintenance

- Local workflows (e.g., `*-local.yml`) handle CI/CD for this repo itself
- Versioning: Managed by semantic-release based on commit messages
- Dependencies: Keep semantic-release and plugins updated
