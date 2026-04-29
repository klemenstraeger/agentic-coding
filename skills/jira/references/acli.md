# ACLI (Atlassian CLI) Reference

Complete reference for `acli jira` commands.

> **Terminology:** `acli` uses **"workitem"** instead of "issue" throughout.

---

## Viewing Workitems

```bash
# View single workitem
acli jira workitem view KEY-123

# View as JSON
acli jira workitem view KEY-123 --json

# View specific fields
acli jira workitem view KEY-123 --fields summary,status,comment

# View all fields
acli jira workitem view KEY-123 --fields '*all'

# View navigable fields except comments
acli jira workitem view KEY-123 --fields '*navigable,-comment'

# Open in browser
acli jira workitem view KEY-123 --web
```

---

## Searching Workitems

```bash
# Search with JQL
acli jira workitem search --jql "project = PROJ"

# My tickets ordered by update
acli jira workitem search --jql "assignee = currentUser() ORDER BY updated DESC"

# My in-progress tickets
acli jira workitem search --jql "assignee = currentUser() AND status = 'In Progress'"

# Limit results
acli jira workitem search --jql "project = PROJ" --limit 50

# Paginate all results
acli jira workitem search --jql "project = PROJ" --paginate

# Count matching issues
acli jira workitem search --jql "project = PROJ" --count

# CSV output
acli jira workitem search --jql "project = PROJ" --csv

# JSON output
acli jira workitem search --jql "project = PROJ" --json

# Custom fields in output
acli jira workitem search --jql "project = PROJ" --fields "key,summary,assignee,status"

# Search by filter ID
acli jira workitem search --filter 10001

# Open search results in browser
acli jira workitem search --filter 10001 --web
```

### Common JQL Patterns

```sql
-- My open tickets
assignee = currentUser() AND status != Done ORDER BY updated DESC

-- High priority bugs in project
project = PROJ AND type = Bug AND priority = High

-- Updated in last 7 days
project = PROJ AND updated >= -7d

-- Created this sprint
project = PROJ AND sprint in openSprints()

-- Unassigned tickets
project = PROJ AND assignee is EMPTY

-- Text search
project = PROJ AND text ~ "search term"

-- Multiple statuses
status IN ("To Do", "In Progress") AND project = PROJ
```

---

## Creating Workitems

```bash
# Basic creation
acli jira workitem create -p PROJECT -t Task -s "Summary"

# With description
acli jira workitem create -p PROJECT -t Task -s "Summary" -d "Description text"

# With description from file
acli jira workitem create -p PROJECT -t Task -s "Summary" --description-file desc.md

# Assign to self on creation
acli jira workitem create -p PROJECT -t Task -s "Summary" -a @me

# With labels
acli jira workitem create -p PROJECT -t Bug -s "Summary" -l "bug,urgent"

# With parent (subtask)
acli jira workitem create -p PROJECT -t Sub-task -s "Summary" --parent KEY-123

# From JSON file
acli jira workitem create --from-json workitem.json

# Generate JSON template
acli jira workitem create --generate-json

# Open editor for summary/description
acli jira workitem create -p PROJECT -t Task --editor

# From text file (summary + description)
acli jira workitem create -p PROJECT -t Task --from-file workitem.txt

# Output as JSON
acli jira workitem create -p PROJECT -t Task -s "Summary" --json
```

**Multi-line descriptions:** Use `--description-file` to read from a file:

```bash
cat > /tmp/jira_desc.md <<'EOF'
## Description
User needs ability to export data...

## Acceptance Criteria
- Export works for CSV
- Export works for JSON
EOF

acli jira workitem create -p PROJ -t Story -s "Add export" --description-file /tmp/jira_desc.md
```

---

## Editing Workitems

```bash
# Edit summary
acli jira workitem edit --key KEY-123 -s "New Summary"

# Edit description
acli jira workitem edit --key KEY-123 -d "New description"

# Edit description from file
acli jira workitem edit --key KEY-123 --description-file new_desc.md

# Edit assignee
acli jira workitem edit --key KEY-123 -a user@example.com

# Edit labels
acli jira workitem edit --key KEY-123 -l "label1,label2"

# Remove labels
acli jira workitem edit --key KEY-123 --remove-labels "old-label"

# Edit type
acli jira workitem edit --key KEY-123 -t Bug

# Remove assignee
acli jira workitem edit --key KEY-123 --remove-assignee

# Edit multiple workitems
acli jira workitem edit --key "KEY-1,KEY-2" -s "Updated Summary"

# Edit via JQL
acli jira workitem edit --jql "project = PROJ AND status = 'To Do'" -a @me

# Edit via filter
acli jira workitem edit --filter 10001 -s "Updated" --yes

# From JSON
acli jira workitem edit --from-json workitem.json

# Skip confirmation
acli jira workitem edit --key KEY-123 -s "New Summary" --yes
```

---

## Transitioning Workitems

```bash
# Transition by key
acli jira workitem transition --key KEY-123 --status "Done"

# Transition multiple
acli jira workitem transition --key "KEY-1,KEY-2" --status "In Progress"

# Transition by JQL
acli jira workitem transition --jql "project = PROJ AND status = 'To Do'" --status "In Progress"

# Transition by filter
acli jira workitem transition --filter 10001 --status "Done" --yes

# Skip confirmation
acli jira workitem transition --key KEY-123 --status "Done" --yes

# Ignore errors (useful for bulk)
acli jira workitem transition --jql "project = PROJ" --status "Done" --ignore-errors
```

---

## Assigning Workitems

```bash
# Assign to self
acli jira workitem assign --key KEY-123 --assignee @me

# Assign to user (email)
acli jira workitem assign --key KEY-123 --assignee user@example.com

# Assign to default
acli jira workitem assign --key KEY-123 --assignee default

# Remove assignee
acli jira workitem assign --key KEY-123 --remove-assignee

# Assign via JQL
acli jira workitem assign --jql "project = PROJ AND assignee is EMPTY" --assignee @me

# Assign via filter
acli jira workitem assign --filter 10001 --assignee @me

# Assign from file (comma/newline separated keys)
acli jira workitem assign --from-file issues.txt --assignee @me

# Skip confirmation
acli jira workitem assign --key KEY-123 --assignee @me --yes
```

---

## Comments

```bash
# Add comment
acli jira workitem comment create --key KEY-123 --body "Comment text"

# Comment from file
acli jira workitem comment create --key KEY-123 --body-file comment.md

# Comment via editor
acli jira workitem comment create --key KEY-123 --editor

# Comment on multiple (JQL)
acli jira workitem comment create --jql "project = PROJ" --body "Batch comment"

# Edit last own comment
acli jira workitem comment create --key KEY-123 --body "Updated" --edit-last

# List comments
acli jira workitem comment list KEY-123

# Update comment
acli jira workitem comment update --key KEY-123 --comment-id 12345 --body "Updated"

# Delete comment
acli jira workitem comment delete --key KEY-123 --comment-id 12345
```

---

## Linking Workitems

```bash
# Create link
acli jira workitem link create --out KEY-1 --in KEY-2 --type Blocks

# List links for a workitem
acli jira workitem link list KEY-123

# Available link types
acli jira workitem link type

# Delete link
acli jira workitem link delete --out KEY-1 --in KEY-2

# Link from JSON
acli jira workitem link create --from-json links.json

# Generate JSON template
acli jira workitem link create --generate-json
```

---

## Sprints

```bash
# List workitems in sprint
acli jira sprint list-workitems --sprint SPRINT_ID --board BOARD_ID

# With custom fields
acli jira sprint list-workitems --sprint 1 --board 6 --fields "key,summary,status,assignee"

# Filter sprint items with JQL
acli jira sprint list-workitems --sprint 1 --board 6 --jql "assignee = currentUser()"

# View sprint details
acli jira sprint view --sprint SPRINT_ID --board BOARD_ID

# Create sprint
acli jira sprint create --board BOARD_ID --name "Sprint 5"

# List sprints for a board
acli jira board list-sprints --board-id BOARD_ID
```

---

## Boards & Projects

```bash
# Search boards
acli jira board search

# Get board details
acli jira board get --board-id BOARD_ID

# List projects on board
acli jira board list-projects --board-id BOARD_ID

# List visible projects
acli jira project list

# View project details
acli jira project view --project PROJ
```

---

## Other Operations

```bash
# Archive workitem
acli jira workitem archive --key KEY-123

# Unarchive workitem
acli jira workitem unarchive --key KEY-123

# Clone workitem
acli jira workitem clone --key KEY-123

# Delete workitem
acli jira workitem delete --key KEY-123

# List watchers
acli jira workitem watcher list KEY-123

# Check auth
acli auth
```

---

## Output Formats

Most commands support these flags:
- `--json` -- JSON output (useful for parsing)
- `--csv` -- CSV output (search only)
- `--fields "key,summary,status"` -- Custom field selection
- `--web` -- Open in browser instead of CLI output
