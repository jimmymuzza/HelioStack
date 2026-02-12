# GitHub Best Practices - HelioStack

This document outlines the GitHub workflows and best practices implemented in the HelioStack repository.

## Overview

HelioStack uses automated workflows to maintain code quality, ensure consistent versioning, and streamline the development process. All workflows are configured in `.github/workflows/` and run automatically on pull requests.

## 1. Auto-Versioning

**File:** `.github/workflows/auto-version.yml`

### What It Does

Automatically creates semantic version tags when pull requests are merged to `main`, based on the source branch name.

### Branch Naming Convention

| Branch Pattern | Version Bump | Example | Use Case |
|---------------|--------------|---------|----------|
| `release/*` | **Major** (X.0.0) | v1.0.0 → v2.0.0 | Breaking changes |
| `feature/*` | **Minor** (x.Y.0) | v1.0.0 → v1.1.0 | New features |
| `fix/*` | **Patch** (x.y.Z) | v1.0.0 → v1.0.1 | Bug fixes |
| Other | No version | - | Skipped |

### Tags Created

When a PR is merged, two tags are created:

1. **Full semantic version:** `v{major}.{minor}.{patch}` (e.g., `v1.2.3`)
2. **Major version shorthand:** `v{major}` (e.g., `v1`) - force-updated to latest

The shorthand tag allows users to reference "latest v1" without pinning to a specific minor/patch version.

### Initial Version

If no tags exist, starts from `v0.0.0` and applies the first bump.

### Example Workflow

```bash
# Create a feature branch
git checkout -b feature/add-user-authentication
git push origin feature/add-user-authentication

# Create and merge PR to main
# Auto-versioning creates: v0.1.0 and v0

# Create a bug fix
git checkout -b fix/login-validation
git push origin fix/login-validation

# Merge PR
# Auto-versioning creates: v0.1.1 and v0

# Create a breaking change
git checkout -b release/new-api-structure
git push origin release/new-api-structure

# Merge PR
# Auto-versioning creates: v1.0.0 and v1
```

### Verify Tags

```bash
git fetch --tags
git tag -l
```

---

## 2. Branch Name Validation

**File:** `.github/workflows/validate.yml` (Job: `validate-branch-name`)

### What It Does

Enforces branch naming conventions to ensure auto-versioning works correctly. PRs with invalid branch names cannot be merged.

### Valid Branch Names

- ✅ `release/breaking-api-changes`
- ✅ `feature/add-dashboard`
- ✅ `fix/memory-leak`
- ❌ `update-readme` (invalid - no prefix)
- ❌ `testing` (invalid - no prefix)

### Error Message

If you create a PR from an invalid branch, you'll see:

```
❌ Invalid branch name: update-readme

Branch must start with one of:
  - release/  (major version bump)
  - feature/  (minor version bump)
  - fix/      (patch version bump)

Example: feature/add-user-auth
```

### When It Runs

- On every pull request to `main`
- Blocks PR merge if validation fails

---

## 3. Code Quality Validation

**File:** `.github/workflows/validate.yml`

### What It Does

Automatically validates code quality and security on every pull request. All checks must pass before merging.

### Validation Jobs

#### Documentation Validation (Always Runs)

**YAML Linting:**
- Validates GitHub Actions workflow files
- Checks syntax and formatting
- Ensures consistent style

**Markdown Linting:**
- Validates README and documentation files
- Checks formatting and structure
- Ensures consistent documentation style

#### Go Code Validation (Conditional - Only if `.go` files exist)

**Formatting Check:**
- Verifies code is formatted with `go fmt`
- Fails if unformatted code is found
- Developer must run `go fmt ./...` locally

**Static Analysis:**
- `go vet` - Catches common Go mistakes
- Detects suspicious constructs
- Reports potential bugs

**Build Verification:**
- `go build -v ./...`
- Ensures code compiles successfully
- Catches compilation errors early

**Security Scanning (Free):**
- `gosec` - Scans for security vulnerabilities
- `govulncheck` - Checks dependencies for known vulnerabilities
- No cost, runs on every PR

### How to Fix Issues Locally

**Go formatting:**
```bash
go fmt ./...
```

**Run all Go checks:**
```bash
go fmt ./...
go vet ./...
go build -v ./...
gosec ./...
govulncheck ./...
```

**YAML validation:**
```bash
pip install yamllint
yamllint .
```

**Markdown validation:**
```bash
npm install -g markdownlint-cli
markdownlint '**/*.md'
```

### When It Runs

- On every pull request to `main`
- All jobs run in parallel for speed
- PR cannot be merged until all checks pass

---

## 4. CODEOWNERS

**File:** `.github/CODEOWNERS`

### What It Does

Automatically assigns code reviewers when pull requests are opened.

### Current Configuration

```
# HelioStack Code Reviewers
* @jimmymuzza
```

All PRs automatically request review from `@jimmymuzza`.

### Adding More Reviewers

Edit `.github/CODEOWNERS` and add usernames:

```
# HelioStack Code Reviewers
* @jimmymuzza @teammate1 @teammate2
```

### Future: GitHub Teams

If you convert to a GitHub organisation, you can use teams:

```
# HelioStack Code Reviewers
* @YourOrg/heliostack-team
```

Then manage team membership in GitHub settings instead of editing this file.

---

## 5. Pull Request Template

**File:** `.github/PULL_REQUEST_TEMPLATE.md`

### What It Does

Provides a standardised structure when creating pull requests. Appears automatically in the PR description box.

### Template Content

```markdown
## What does this PR do?

<!-- Brief description of your changes -->
```

Simple and lightweight - just describe what changed. Branch naming and code quality are enforced automatically by workflows.

### When You'll See It

- Automatically appears when creating a new PR
- Pre-fills the PR description field
- Can be edited or expanded as needed

---

## Creating a Pull Request - Complete Workflow

### 1. Create a Branch (with valid naming)

```bash
# For new features
git checkout -b feature/description-of-feature

# For bug fixes
git checkout -b fix/description-of-fix

# For breaking changes
git checkout -b release/description-of-change
```

### 2. Make Your Changes

```bash
# Edit files
vim main.go

# Format Go code
go fmt ./...

# Verify locally (optional but recommended)
go vet ./...
go build -v ./...
```

### 3. Commit and Push

```bash
git add .
git commit -m "Add feature description"
git push origin feature/description-of-feature
```

### 4. Open Pull Request on GitHub

- Navigate to repository on GitHub
- Click "Compare & pull request"
- PR template appears with description field
- You're automatically assigned as reviewer
- Fill in description and create PR

### 5. Automated Checks Run

Three validation jobs execute automatically:

- ✅ Branch name validation
- ✅ Documentation linting (YAML, Markdown)
- ✅ Go code validation (if `.go` files exist)

### 6. Review and Merge

- All checks must pass (green ticks)
- Approve the PR (or wait for team review)
- Merge to `main`

### 7. Auto-Versioning Creates Tags

Based on your branch name:
- `feature/*` → Minor version bump (e.g., v0.1.0)
- `fix/*` → Patch version bump (e.g., v0.1.1)
- `release/*` → Major version bump (e.g., v1.0.0)

Tags appear automatically:
```bash
git fetch --tags
git tag -l
```

---

## Troubleshooting

### PR Checks Failing

**"Invalid branch name"**
- Solution: Rename branch to use `release/`, `feature/`, or `fix/` prefix
- Example: `git branch -m new-name feature/new-name`

**"Go files are not formatted"**
- Solution: Run `go fmt ./...` and push changes
- Example:
  ```bash
  go fmt ./...
  git add .
  git commit -m "Format Go code"
  git push
  ```

**"gosec security issues found"**
- Review gosec output in workflow logs
- Fix security vulnerabilities in code
- Push fixes and workflow re-runs automatically

**"govulncheck found vulnerabilities"**
- Update vulnerable dependencies
- Run `go get -u` to update modules
- Push updated `go.mod` and `go.sum`

### No Version Tag Created After Merge

**Cause:** Branch name didn't match required patterns

**Solution:** Check merged branch name - must be `release/*`, `feature/*`, or `fix/*`

**Note:** This is now prevented by branch name validation, but if you bypass checks, versioning won't trigger.

### CODEOWNERS Not Working

**Cause:** Username incorrect or repository is private without GitHub Team/Enterprise

**Solution:**
- Verify username is correct in `.github/CODEOWNERS`
- For personal repos, use `@username` format
- For organisations, can use `@org/team-name`

---

## GitHub Repository Settings (Recommended)

These settings complement the automated workflows. Configure in GitHub repository settings:

### Branch Protection Rules (Settings → Branches → Add rule)

Protect the `main` branch:

- ✅ **Require pull request reviews before merging**
  - Required approvals: 1
  - Allow self-approval (for solo development)

- ✅ **Require status checks to pass before merging**
  - Select: `validate-branch-name`, `validate-docs`, `validate-go`
  - ✅ Require branches to be up to date before merging

- ✅ **Do not allow bypassing the above settings**
  - Optional: Allow administrators to bypass

- ❌ **Do not require signed commits** (optional security enhancement)

- ❌ **Do not allow force pushes**

- ❌ **Do not allow deletions**

### Settings Summary

These branch protection rules ensure:
- PRs required for all changes to `main`
- Validation workflows must pass
- Code review required (can approve your own initially)
- Prevents accidental force pushes or branch deletion

---

## Summary

HelioStack implements four key GitHub best practices:

1. **Auto-Versioning** - Semantic version tags created automatically on merge
2. **Branch Name Validation** - Enforces naming conventions for versioning
3. **Code Quality Validation** - Automated linting, formatting, security scanning
4. **CODEOWNERS** - Automatic reviewer assignment
5. **PR Template** - Standardised pull request structure

All workflows are lightweight, run automatically, and require no manual intervention. They ensure code quality, consistent versioning, and streamlined development process.

---

## References

- Auto-versioning workflow: `.github/workflows/auto-version.yml`
- Validation workflow: `.github/workflows/validate.yml`
- CODEOWNERS: `.github/CODEOWNERS`
- PR template: `.github/PULL_REQUEST_TEMPLATE.md`
- Semantic Versioning: https://semver.org/
- GitHub Actions: https://docs.github.com/en/actions
