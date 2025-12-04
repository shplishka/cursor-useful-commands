# /linear-from-prompts - Create Linear Issues from Cursor Prompt History

## Command
`/linear-from-prompts` or `create linear issues from prompts` or `linear issues from chat history`

## Purpose
Automatically create Linear issues from Cursor chat/prompt history, converting conversation prompts into properly structured Linear issues with appropriate priority, labels, and descriptions.

## When to Use
- After working on tasks via Cursor chat without creating Linear issues first
- When you want to retroactively track work discussed in Cursor conversations
- To document completed work from chat sessions for project tracking
- To create issues from a series of related prompts/conversations

## Usage

### Basic Usage
```
/linear-from-prompts
```

### With Options
```
/linear-from-prompts --since=2025-12-01
/linear-from-prompts --count=10
/linear-from-prompts --workspace=current
/linear-from-prompts --session=recent
```

## Execution Flow

1. **Read Cursor Chat History**
   - Locate Cursor chat sessions directory: `~/Library/Application Support/Cursor/User/workspaceStorage/{workspace-id}/chatSessions`
   - Get chat sessions from specified range (default: last 10 sessions)
   - Parse chat messages and extract user prompts
   - Extract prompt content, context, and timestamps

2. **Parse Chat Prompts**
   - Extract user prompts (not AI responses)
   - Identify task-oriented prompts (commands, feature requests, bug reports)
   - Extract context from conversation flow
   - Identify related issues (BRAIN-XXX, RITE-XXX patterns)
   - Filter out casual conversation and clarification prompts

3. **Create Linear Issues**
   - For each significant prompt:
     - Determine issue type (Bug/Feature/Improvement)
     - Set priority based on prompt content and urgency indicators
     - Generate issue title from prompt
     - Create issue description from prompt and conversation context
     - Assign to current user
     - Set appropriate project based on prompt content
     - Add relevant labels

4. **Link Context**
   - Add chat session timestamp to issue description
   - Reference related prompts if part of same conversation
   - Link to any code changes made during the session

## Prompt Type Mapping

### Issue Type Mapping
- Prompts containing "fix", "bug", "error", "broken" → **Bug** label
- Prompts containing "add", "create", "implement", "feature" → **Feature** label
- Prompts containing "improve", "refactor", "optimize", "enhance" → **Improvement** label
- Prompts containing "update", "upgrade", "modify" → **Improvement** label

### Priority Mapping
- Prompts with urgency indicators ("urgent", "critical", "asap", "blocking") → **High (2)** or **Urgent (1)**
- Feature requests → **Normal (3)**
- Improvements and optimizations → **Normal (3)**
- Documentation and minor updates → **Low (4)**

### Project Mapping
- Prompts mentioning "opentelemetry", "tracing", "metrics", "logging" → **Infra & Devops & Code Quality & Tech Debts**
- Prompts mentioning "cache", "llm", "agent" → **Infra & Devops & Code Quality & Tech Debts**
- Prompts mentioning "detector", "algorithm", "mesh", "rebar" → **Algorithm** projects
- Prompts mentioning "api", "worker", "endpoint" → **Platform**
- Prompts mentioning "database", "migration", "schema" → **Data**
- Default → **Infra & Devops & Code Quality & Tech Debts**

## Issue Structure

### Title Generation
- Extract main action from prompt
- Convert to action-oriented title: `{Action} {Subject}`
- Examples:
  - `"fix the OpenTelemetry warnings"` → `Fix OpenTelemetry deprecation warnings`
  - `"add caching for LLM responses"` → `Add caching mechanism for LLM responses`
  - `"improve the mesh detection algorithm"` → `Improve mesh detection algorithm accuracy`

### Description Template
```markdown
## Summary

{Main request from prompt}

## Context

{Relevant conversation context and follow-up questions}

## Chat Session Details

- **Session**: {Session ID or timestamp}
- **Date**: {Date}
- **Workspace**: {Workspace name}

## Related

{Related issues if mentioned in conversation}
```

## Example Output

### Input Prompt
```
User: "I need to fix the OpenTelemetry deprecation warnings that are showing up in tests. Can you help suppress those?"

AI: [Response with implementation]

User: "Great, also make sure it doesn't affect production logging"
```

### Generated Issue
- **Title**: `Fix OpenTelemetry deprecation warnings in tests`
- **Type**: Bug
- **Priority**: High (2)
- **Project**: Infra & Devops & Code Quality & Tech Debts
- **Description**: 
  ```markdown
  ## Summary
  
  Suppress OpenTelemetry deprecation warnings appearing in test output without affecting production logging.
  
  ## Context
  
  Deprecation warnings are triggered by OpenTelemetry's internal initialization code during tests. Need to suppress these warnings in test environment while maintaining production logging functionality.
  
  ## Chat Session Details
  
  - **Session**: 2025-12-04T10:30:00Z
  - **Date**: 2025-12-04
  - **Workspace**: brain
  
  ## Related
  
  - Related to test infrastructure improvements
  ```

## Options

### `--since=DATE`
- Filter prompts since specified date
- Format: `YYYY-MM-DD` or relative like `7 days ago`
- Example: `--since=2025-12-01`

### `--count=N`
- Number of chat sessions to process (default: 10)
- Example: `--count=5`

### `--workspace=WORKSPACE`
- Process prompts from specific workspace
- Use `current` for current workspace (default)
- Example: `--workspace=current`

### `--session=SESSION_ID`
- Process specific chat session
- Use `recent` for most recent session (default)
- Example: `--session=recent`

### `--dry-run`
- Preview issues without creating them
- Shows what would be created

### `--skip-existing`
- Skip prompts that already have Linear issues
- Checks for BRAIN-XXX or RITE-XXX in conversation

### `--min-length=N`
- Minimum prompt length to consider (default: 20 characters)
- Filters out very short prompts
- Example: `--min-length=50`

## Safety Features

- ✅ Validates prompts before creating issues
- ✅ Skips prompts that already reference Linear issues (BRAIN-XXX, RITE-XXX)
- ✅ Asks for confirmation before creating multiple issues
- ✅ Dry-run mode to preview before creating
- ✅ Handles API errors gracefully
- ✅ Prevents duplicate issues
- ✅ Filters out casual conversation and clarification prompts

## Best Practices

1. **Review Before Creating**: Use `--dry-run` first
2. **Filter Appropriately**: Use `--since` or `--count` to limit scope
3. **Skip Existing**: Use `--skip-existing` to avoid duplicates
4. **Batch Related Prompts**: Process related conversations together
5. **Review Generated Issues**: Check titles and descriptions after creation
6. **Focus on Action Items**: Only create issues for actionable prompts

## Limitations

- Cannot automatically determine all project assignments
- May need manual priority adjustment
- Requires well-structured prompts for best results
- Cannot link to code changes that weren't committed
- Chat history format may vary between Cursor versions

## Chat History Location

Cursor stores chat history in:
```
~/Library/Application Support/Cursor/User/workspaceStorage/{workspace-id}/chatSessions
```

The workspace ID is a hash unique to each workspace/project.

## Related Commands

- `/linear-from-commits` - Create Linear issues from git commits
- `/git-ship` - Create PR with Linear issue
- `/plan` - Create implementation plan

