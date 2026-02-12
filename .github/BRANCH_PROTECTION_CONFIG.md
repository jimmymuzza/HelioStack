# Branch Protection Configuration

Current branch protection settings for the `main` branch in HelioStack repository.

**Last Updated:** 12 February 2026
**Status:** Temporarily simplified - approval requirements removed

---

## Current Configuration

### ✅ Enabled Settings

#### Pull Request Requirements
- **Require a pull request before merging:** ON
  - All changes to `main` must go through a pull request
  - Direct pushes to `main` are blocked

- **Required approvals:** OFF (temporarily disabled)
  - Approvals not currently required
  - Can merge PRs without approval
  - _Note: Was causing self-approval issues, to be re-enabled when resolved_

- **Require review from Code Owners:** OFF (temporarily disabled)
  - CODEOWNERS file still exists but not enforced
  - Will be re-enabled with approval requirements

#### Force Push Protection
- **Block force pushes:** ON
  - Prevents `git push --force` to `main` branch
  - Protects against accidental history rewrites

#### Merge Methods (All Enabled)
- **Allow merge commits:** ON
- **Allow squash merging:** ON
- **Allow rebase merging:** ON
- Flexibility to choose merge strategy per-PR

---

### ❌ Disabled Settings

#### Not Currently Required

- **Restrict creations:** OFF
  - Branch creation is unrestricted
  - Can create feature/fix/release branches freely

- **Restrict updates:** OFF
  - Can push commits to branches normally

- **Restrict deletions:** OFF
  - Branches can be deleted (including via PR merge)
  - Consider enabling for additional protection

- **Require linear history:** OFF
  - Merge commits are allowed
  - Not forcing rebase-only workflow

- **Require deployments to succeed:** OFF
  - No deployment gates configured

- **Require signed commits:** OFF
  - Commits don't need GPG signatures
  - Can enable later for enhanced security

- **Dismiss stale pull request approvals:** OFF
  - Approvals persist after new commits
  - Reduces friction for solo development

- **Require approval of most recent push:** OFF
  - Allows self-approval
  - Would require another person to approve

- **Require conversation resolution:** OFF
  - Comments don't need to be resolved before merge
  - Good for solo development

#### Status Checks (To Be Enabled)

- **Require status checks to pass before merging:** OFF
  - Validation workflows run but don't block merge
  - Will be enabled once check names appear in GitHub

**Missing checks:**
- `validate-branch-name` - Branch naming convention enforcement
- `validate-docs` - YAML and Markdown linting
- `validate-go` - Go formatting, vetting, building, security scanning

#### Not Applicable

- **Require review from specific teams:** Not configured
  - Requires GitHub organisation with teams
  - Using CODEOWNERS instead

- **Require code scanning results:** OFF
  - No GitHub Advanced Security configured

- **Require code quality results:** OFF
  - No code quality tools configured

- **Automatically request Copilot code review:** OFF
  - Optional feature

---

## What This Configuration Does

### You Can:
- ✅ Create branches with any name
- ✅ Push commits to feature/fix/release branches
- ✅ Create pull requests to `main`
- ✅ Merge PRs immediately (no approval required)
- ✅ Choose merge method (merge/squash/rebase) per-PR

### You Cannot:
- ❌ Push directly to `main` branch
- ❌ Force push to `main` branch

### Validation Workflows
- ✅ Run automatically on every PR
- ✅ Show pass/fail status
- ❌ Don't block merging (yet)
- Results visible in PR checks section

---

## Workflow Example

### Creating and Merging a PR

```bash
# 1. Create feature branch
git checkout -b feature/new-feature

# 2. Make changes
# ... edit files ...

# 3. Commit and push
git add .
git commit -m "Add new feature"
git push origin feature/new-feature

# 4. On GitHub:
# - Create PR to main
# - Validation checks run automatically:
#   - validate-branch-name
#   - validate-docs
#   - validate-go (if .go files exist)

# 5. Review (optional)
# - Review the changes
# - Check validation results (informational)

# 6. Merge
# - Choose merge method (merge/squash/rebase)
# - Merge PR immediately (no approval needed)
# - Auto-versioning creates tags based on branch name
```

---

## How to Enable Status Check Requirements

Once validation checks have run at least once, their names will appear in GitHub settings.

### Steps

1. **Ensure checks have run:**
   - Create a PR (any branch name starting with feature/fix/release)
   - Let validation checks complete
   - Check names now registered in GitHub

2. **Edit branch protection rule:**
   - Go to: Repository Settings → Branches → Edit rule for `main`
   - Find: "Require status checks to pass before merging"
   - Enable the setting

3. **Select required checks:**
   - Search for and select:
     - ✅ `validate-branch-name`
     - ✅ `validate-docs`
     - ✅ `validate-go`
   - Enable: "Require branches to be up to date before merging"

4. **Save changes**

5. **Test:**
   - Create new PR
   - Merge button should be disabled until checks pass + approval

### After Enabling Status Checks

PRs will require:
1. ✅ Branch name matches pattern (release/*, feature/*, fix/*)
2. ✅ YAML and Markdown files are valid
3. ✅ Go code formatted, vetted, builds successfully, no security issues
4. ✅ All checks passed

Only then can the PR be merged.

---

## Future Enhancements

### Consider Enabling Later

**Restrict deletions:**
- Prevents accidental branch deletion
- Good safety net

**Require conversation resolution:**
- When working with team members
- Ensures all feedback is addressed

**Require signed commits:**
- If security policy requires it
- Adds cryptographic verification

**Dismiss stale approvals:**
- When working with team
- Requires re-review after changes

**Require approval of most recent push:**
- When you have team members
- Prevents self-approval

**Re-enable approval requirements:**
- Once self-approval issue is resolved
- Require code owner review

### Advanced Features (Requires Setup)

**Code scanning:**
- GitHub Advanced Security
- Automated vulnerability detection

**Deployments:**
- Require successful deployment to staging
- Before merging to main

**Linear history:**
- Force rebase/squash only
- Prevents merge commits

---

## Related Documentation

- Auto-versioning workflow: `.github/workflows/auto-version.yml`
- Validation workflow: `.github/workflows/validate.yml`
- Code owners: `.github/CODEOWNERS`
- PR template: `.github/PULL_REQUEST_TEMPLATE.md`
- Best practices guide: `.github/GITHUB_BEST_PRACTICES.md`
- Account switching: `.github/SWITCHING_GITHUB_ACCOUNTS.md`

---

## Configuration History

| Date | Change | Reason |
|------|--------|--------|
| 12 Feb 2026 | Initial configuration | Set up branch protection with PR requirements, code owner approval, and force push blocking |
| 12 Feb 2026 | Status checks: Disabled | Waiting for check names to appear after first run |
| 12 Feb 2026 | Approval requirements: Disabled | Removed due to self-approval issues; temporarily simplified configuration |

---

## Quick Reference

**GitHub Settings Location:**
- Repository Settings → Branches → Branch protection rules
- Direct link: `https://github.com/jimmymuzza/HelioStack/settings/branches`

**Current Protection Level:** Light
- ✅ PR workflow enforced
- ❌ Code owner approval: Disabled (temporarily)
- ✅ Force push blocked
- ⚠️ Status checks run but don't block (to be enabled)

**Status:** Active and working (simplified)
**Next Steps:**
1. Enable status check requirements after they appear in GitHub
2. Re-enable approval requirements when self-approval issue is resolved
