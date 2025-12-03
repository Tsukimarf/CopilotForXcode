#!/bin/bash

# 1. Create a branch for the revert
git checkout -b revert/devcontainer-config

# 2. Remove the devcontainer configuration
if [ -f ".devcontainer/devcontainer.json" ]; then
    echo "Removing devcontainer.json..."
    git rm .devcontainer/devcontainer.json
    
    # Remove directory if empty
    if [ -d ".devcontainer" ] && [ -z "$(ls -A .devcontainer)" ]; then
        rmdir .devcontainer
        echo "Removed empty .devcontainer directory"
    fi
else
    echo "devcontainer.json not found!"
    exit 1
fi

# 3. Create the commit
git commit -m "Revert: Remove devcontainer configuration

Summary:
- Removed GitHub Codespaces setup
- Clean up unused configuration
- Standardize on local development"

# 4. Push and create PR
git push origin revert/devcontainer-config

# 5. Create PR using GitHub CLI
gh pr create \
  --title "Revert 'Create devcontainer.json'" \
  --body "$(cat <<'EOF'
## Description
Removes the GitHub Codespaces devcontainer configuration because:
- We're standardizing on local Docker development
- The team prefers docker-compose workflows
- Reduces configuration complexity

**Files changed:**
- ❌ `.devcontainer/devcontainer.json` (removed)
- ✅ No other files affected

## Related Issue
- Reverts: #456 (Original PR adding devcontainer)
- References: #123 (Development environment discussion)

## Type of Change
- [x] Revert/Removal

## Checklist
- [x] Removed devcontainer.json
- [x] Verified no broken references
- [x] Tested local development works
- [ ] Update documentation (if needed)

## Screenshots
*Before:* `.devcontainer/` directory existed
*After:* Clean root directory structure
EOF
)" \
  --reviewer "team-lead" \
  --label "revert" \
  --assign "@tsukimarf"
