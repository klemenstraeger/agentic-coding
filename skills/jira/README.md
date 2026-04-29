# Jira Skill

Natural language interaction with Jira via `acli` (official Atlassian CLI) for managing work items, sprints, and workflows.

## Purpose

The Jira skill bridges the gap between natural language requests and Jira operations. Instead of remembering specific CLI commands, you can simply tell Claude what you want to do with your Jira tickets, and the skill handles the technical details.

Key benefits:
- **Conversational interface**: Ask questions like "What are my open tickets?" or "Move PROJ-123 to Done"
- **acli backend**: Uses the official Atlassian CLI (`acli`) for all operations
- **Safety-first approach**: Always fetches current state before modifications and requires approval for changes
- **Context-aware**: Detects issue keys (e.g., PROJ-123) in conversation and offers relevant actions

## When to Use

This skill activates when you:

- Mention Jira issue keys (e.g., "Show me PROJ-123", "What's the status of ABC-456?")
- Ask about tickets ("List my open tickets", "What's assigned to me?")
- Want to create issues ("Create a bug ticket for the login issue")
- Need to update tickets ("Move this to In Progress", "Assign PROJ-123 to me")
- Check sprint status ("What's in the current sprint?", "Show sprint backlog")
- Manage workflow ("Close this ticket", "Add a comment to PROJ-123")

**Trigger phrases include:**
- "create a jira ticket"
- "show me PROJ-123"
- "list my tickets"
- "move ticket to done"
- "what's in the current sprint"
- Any mention of "jira", "issue", "ticket", "sprint", "backlog", or "acli"

## How It Works

### 1. Backend

This skill uses `acli` (Atlassian CLI) exclusively. It must be installed and authenticated:

```bash
# Install
brew install atlassian/tap/acli

# Authenticate
acli auth
```

> **Key terminology:** `acli` uses **"workitem"** instead of "issue".

### 2. Operation Execution

1. **Read operations** (viewing, listing, searching): Execute immediately and display results
2. **Write operations** (create, update, transition):
   - Fetch current workitem state first
   - Show proposed changes
   - Request user approval
   - Execute the operation
   - Verify the result

### 3. Safety Checks

Before any modification, the skill:

- Fetches current workitem state (never assumes status or assignee)
- Checks who else might be affected (watchers, linked issues)
- Verifies the operation is reversible or warns if not
- Confirms correct identifiers (workitem keys, board/sprint IDs)

## Key Features

### Work Item Management
- **View workitems**: See full details including description, status, assignee, and comments
- **Search workitems**: Filter with JQL, by assignee, status, type, priority, or labels
- **Create workitems**: Create tickets with descriptions, labels, assignees, and parent links
- **Edit workitems**: Modify summary, description, labels, type, and assignee
- **Assign workitems**: Assign to yourself (`@me`), others (by email), or unassign

### Workflow Operations
- **Transition workitems**: Move tickets through workflow states
- **Add comments**: Document progress or decisions on tickets
- **Link workitems**: Create relationships between tickets (blocks, relates to, duplicates)

### Sprint & Board Management
- **Sprint workitems**: See what's in a specific sprint
- **Board search**: Find and browse boards
- **Board sprints**: List sprints for a board

### Search and Query
- **JQL search**: Use full Jira Query Language with `--jql` flag
- **Custom output**: Select fields, use CSV/JSON output, or count results
- **Filter-based search**: Use saved Jira filters by ID

## Usage Examples

### Viewing Workitems

```
"Show me PROJ-123"
"What's the status of ABC-456?"
"Display the details of that bug ticket"
```

### Searching Workitems

```
"List my open tickets"
"What issues are in progress?"
"Show me high priority bugs"
"Find all tickets updated this week"
```

### Creating Workitems

```
"Create a bug ticket for the login timeout issue"
"Make a new task for updating the documentation"
"Create a story for implementing the export feature"
```

### Updating Workitems

```
"Move PROJ-123 to In Progress"
"Assign this ticket to me"
"Mark the bug as done"
"Add a comment saying the fix is deployed"
```

### Sprint Operations

```
"What's in sprint 5 on board 6?"
"List sprints for my board"
```

## Prerequisites

### Install acli

```bash
# macOS
brew install atlassian/tap/acli

# Authenticate
acli auth
```

### Verify Setup

```bash
# Check installation
which acli

# Verify auth
acli auth
```

## Safety Guidelines

The skill follows strict safety protocols:

**Always:**
- Shows commands before executing
- Requests approval before modifications
- Preserves original content when editing
- Verifies updates after applying
- Surfaces authentication issues clearly

**Never:**
- Transitions without fetching current status first
- Edits descriptions without showing the original
- Assumes transition names are universal across projects
- Bulk-modifies without explicit approval
- Uses `--yes` flag without explicit user approval

## Troubleshooting

### Authentication Issues

```bash
# Re-authenticate
acli auth
```

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| "Issue not found" | Invalid key | Verify the workitem key is correct |
| "Transition not available" | Workflow constraint | Check available transitions first |
| "Permission denied" | Project access | Verify your Jira permissions |
| "Required field missing" | Project config | Check project's required fields |

### acli Not Available

```bash
brew install atlassian/tap/acli
acli auth
```

## Reference Files

For advanced usage, the skill includes a detailed reference:

- `references/acli.md`: Complete acli command reference with examples, JQL patterns, and all subcommands
