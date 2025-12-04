# /linear-from-commits - Create Linear Issues from Git Commit History

## Command
`/linear-from-commits` or `create linear issues from commits` or `linear issues from git history`

## Purpose
Automatically create Linear issues from git commit history, converting commits into properly structured Linear issues with appropriate priority, labels, and descriptions.

## When to Use
- After completing work without creating Linear issues first
- When you want to retroactively track work in Linear
- To document completed work for project tracking
- To create issues from a series of related commits

## Usage

### Basic Usage
```
/linear-from-commits
```

### With Options
```
/linear-from-commits --since=2025-12-01
/linear-from-commits --count=10
/linear-from-commits --branch=main
/linear-from-commits --author=me
```

## Execution Flow

1. **Read Git History**
   - Get commits from specified range (default: last 10 commits)
   - Parse commit messages and bodies
   - Extract commit type, description, and details

2. **Parse Commit Messages**
   - Extract conventional commit type (feat, fix, refactor, etc.)
   - Parse commit description
   - Extract commit body for issue description
   - Identify related issues (RITE-XXX patterns)

3. **Create Linear Issues**
   - For each commit:
     - Determine issue type (Bug/Feature/Improvement)
     - Set priority based on commit type
     - Generate issue title from commit message
     - Create issue description from commit body
     - Assign to commit author
     - Set appropriate project
     - Add relevant labels

4. **Link Commits**
   - Add commit SHA to issue description
   - Link PR if exists
   - Reference related issues

## Commit Type Mapping

### Issue Type Mapping
- `feat:` → **Feature** label
- `fix:` → **Bug** label
- `refactor:` → **Improvement** label
- `perf:` → **Improvement** label
- `docs:` → **Improvement** label
- `chore:` → **Improvement** label
- `test:` → **Improvement** label

### Priority Mapping
- `fix:` (critical bugs) → **High (2)**
- `feat:` (major features) → **Normal (3)**
- `refactor:` → **Normal (3)**
- `perf:` → **Normal (3)**
- `docs:` → **Low (4)**
- `chore:` → **Low (4)**
- `test:` → **Low (4)**

### Project Mapping
- Commits mentioning "opentelemetry", "tracing", "metrics", "logging" → **Infra & Devops & Code Quality & Tech Debts**
- Commits mentioning "cache", "llm", "agent" → **Infra & Devops & Code Quality & Tech Debts**
- Commits mentioning "detector", "algorithm" → **Algorithm** projects
- Commits mentioning "api", "worker" → **Platform**
- Default → **Infra & Devops & Code Quality & Tech Debts**

## Issue Structure

### Title Generation
- Extract from commit message: `{type}: {description}`
- Convert to action-oriented title: `{Action} {Subject}`
- Examples:
  - `fix: suppress OpenTelemetry warnings` → `Suppress OpenTelemetry deprecation warnings`
  - `refactor: streamline caching mechanism` → `Streamline caching mechanism and enhance performance tracking`
  - `feat: add mesh quantity reporting` → `Add mesh quantity reporting with comprehensive tests`

### Description Template
```markdown
## Summary

{Commit description from commit body or message}

## Changes Made

{Extracted from commit body, formatted as bullet points}

## Commit Details

- **Commit**: {SHA}
- **Author**: {Author}
- **Date**: {Date}
- **PR**: {PR link if exists}

## Related

{Related issues if mentioned in commit}
```

## Example Output

### Input Commit
```
f4d632ed fix: suppress OpenTelemetry deprecation warnings in tests

Add filterwarnings configuration to suppress deprecation warnings from
OpenTelemetry's internal initialization code. These warnings are triggered
by OpenTelemetry's internal _events module and are not caused by our code.

Related: https://github.com/open-telemetry/opentelemetry-python/issues/4783
```

### Generated Issue
- **Title**: `Suppress OpenTelemetry deprecation warnings in tests`
- **Type**: Bug
- **Priority**: High (2)
- **Project**: Infra & Devops & Code Quality & Tech Debts
- **Description**: 
  ```markdown
  ## Summary
  
  Add filterwarnings configuration to suppress deprecation warnings from
  OpenTelemetry's internal initialization code.
  
  ## Changes Made
  
  - Added filterwarnings configuration to pytest
  - Suppressed warnings from OpenTelemetry's internal _events module
  - These warnings are triggered by OpenTelemetry's internal code, not our code
  
  ## Commit Details
  
  - **Commit**: f4d632ed
  - **Author**: michael.cohen@rite.build
  - **Date**: 2025-12-04
  
  ## Related
  
  - https://github.com/open-telemetry/opentelemetry-python/issues/4783
  ```

## Options

### `--since=DATE`
- Filter commits since specified date
- Format: `YYYY-MM-DD` or relative like `7 days ago`
- Example: `--since=2025-12-01`

### `--count=N`
- Number of commits to process (default: 10)
- Example: `--count=5`

### `--branch=BRANCH`
- Process commits from specific branch (default: current branch)
- Example: `--branch=main`

### `--author=AUTHOR`
- Filter commits by author
- Example: `--author=me` or `--author=michael.cohen@rite.build`

### `--dry-run`
- Preview issues without creating them
- Shows what would be created

### `--skip-existing`
- Skip commits that already have Linear issues
- Checks for RITE-XXX in commit message

## Safety Features

- ✅ Validates commit messages before creating issues
- ✅ Skips commits that already reference Linear issues (RITE-XXX)
- ✅ Asks for confirmation before creating multiple issues
- ✅ Dry-run mode to preview before creating
- ✅ Handles API errors gracefully
- ✅ Prevents duplicate issues

## Best Practices

1. **Review Before Creating**: Use `--dry-run` first
2. **Filter Appropriately**: Use `--since` or `--count` to limit scope
3. **Skip Existing**: Use `--skip-existing` to avoid duplicates
4. **Batch Related Commits**: Process related commits together
5. **Review Generated Issues**: Check titles and descriptions after creation

## Limitations

- Cannot automatically determine all project assignments
- May need manual priority adjustment
- Commit messages must follow conventional format for best results
- Cannot link to PRs that don't exist yet

## Related Commands

- `/git-ship` - Create PR with Linear issue
- `/plan` - Create implementation plan
- `/review` - Review code changes

