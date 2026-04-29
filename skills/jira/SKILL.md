---
name: jira
description: Use when the user mentions Jira issues (e.g., "PROJ-123"), asks about tickets, wants to create/view/update issues, check sprint status, or manage their Jira workflow. Triggers on keywords like "jira", "issue", "ticket", "sprint", "backlog", "acli", or issue key patterns.
---

# Jira

Natural language interaction with Jira via `acli` (official Atlassian CLI).

## Backend

This skill uses **`acli`** (Atlassian CLI) exclusively. Verify it's available:

```
Run: which acli
→ If found: proceed
→ If not found: brew install atlassian/tap/acli && acli auth
```

> **Key terminology:** `acli` uses **"workitem"** instead of "issue".

---

## Quick Reference

| Intent | Command |
|--------|---------|
| View issue | `acli jira workitem view KEY-123` |
| View in browser | `acli jira workitem view KEY-123 --web` |
| View as JSON | `acli jira workitem view KEY-123 --json` |
| View specific fields | `acli jira workitem view KEY-123 --fields summary,status,comment` |
| List my issues | `acli jira workitem search --jql "assignee = currentUser() ORDER BY updated DESC"` |
| My in-progress | `acli jira workitem search --jql "assignee = currentUser() AND status = 'In Progress'"` |
| Search with limit | `acli jira workitem search --jql "project = PROJ" --limit 20` |
| Search as CSV | `acli jira workitem search --jql "project = PROJ" --csv` |
| Count results | `acli jira workitem search --jql "project = PROJ" --count` |
| Create issue | `acli jira workitem create -p PROJECT -t Task -s "Summary"` |
| Create with desc | `acli jira workitem create -p PROJECT -t Task -s "Summary" -d "Description"` |
| Create from file | `acli jira workitem create -p PROJECT -t Task -s "Summary" --description-file desc.md` |
| Create & assign me | `acli jira workitem create -p PROJECT -t Task -s "Summary" -a @me` |
| Edit summary | `acli jira workitem edit --key KEY-123 -s "New Summary"` |
| Edit description | `acli jira workitem edit --key KEY-123 -d "New description"` |
| Edit labels | `acli jira workitem edit --key KEY-123 -l "label1,label2"` |
| Transition | `acli jira workitem transition --key KEY-123 --status "Done"` |
| Transition (no prompt) | `acli jira workitem transition --key KEY-123 --status "Done" --yes` |
| Assign to me | `acli jira workitem assign --key KEY-123 --assignee @me` |
| Assign to user | `acli jira workitem assign --key KEY-123 --assignee user@example.com` |
| Unassign | `acli jira workitem assign --key KEY-123 --remove-assignee` |
| Add comment | `acli jira workitem comment create --key KEY-123 --body "Comment text"` |
| Comment from file | `acli jira workitem comment create --key KEY-123 --body-file comment.md` |
| List comments | `acli jira workitem comment list KEY-123` |
| List projects | `acli jira project list` |
| Sprint workitems | `acli jira sprint list-workitems --sprint ID --board ID` |
| List boards | `acli jira board search` |
| Board sprints | `acli jira board list-sprints --board-id ID` |
| Link issues | `acli jira workitem link create --out KEY-1 --in KEY-2 --type Blocks` |
| List links | `acli jira workitem link list KEY-123` |
| Link types | `acli jira workitem link type` |
| Auth check | `acli auth` |

---

## Triggers

- "create a jira ticket"
- "show me PROJ-123"
- "list my tickets"
- "move ticket to done"
- "what's in the current sprint"
- "use acli"

---

## Issue Key Detection

Issue keys follow the pattern: `[A-Z]+-[0-9]+` (e.g., PROJ-123, ABC-1).

When a user mentions an issue key in conversation:
- `acli jira workitem view KEY` to view details
- `acli jira workitem view KEY --web` to open in browser

---

## Workflow

**Creating tickets:**
1. Research context if user references code/tickets/PRs
2. Draft ticket content
3. Review with user
4. Create with `acli jira workitem create`

**Updating tickets:**
1. Fetch issue details first with `acli jira workitem view KEY`
2. Check status (careful with in-progress tickets)
3. Show current vs proposed changes
4. Get approval before updating
5. Add comment explaining changes with `acli jira workitem comment create`

---

## Before Any Operation

Ask yourself:

1. **What's the current state?** -- Always fetch the issue first. Don't assume status, assignee, or fields are what user thinks they are.

2. **Who else is affected?** -- Check watchers, linked issues, parent epics. A "simple edit" might notify 10 people.

3. **Is this reversible?** -- Transitions may have one-way gates. Some workflows require intermediate states. Description edits have no undo.

4. **Do I have the right identifiers?** -- Issue keys, board/sprint IDs for sprint operations.

---

## NEVER

- **NEVER transition without fetching current status** -- Workflows may require intermediate states. "To Do" -> "Done" might fail silently if "In Progress" is required first.

- **NEVER edit description without showing original** -- Jira has no undo. User must see what they're replacing.

- **NEVER assume transition names are universal** -- "Done", "Closed", "Complete" vary by project. Always get available transitions first.

- **NEVER bulk-modify without explicit approval** -- Each ticket change notifies watchers. 10 edits = 10 notification storms.

- **NEVER use `--yes` flag without explicit user approval** -- This skips confirmation prompts. Let acli prompt the user unless they've asked to skip.

---

## Safety

- Always show the command before running it
- Always get approval before modifying tickets
- Preserve original information when editing
- Verify updates after applying
- Always surface authentication issues clearly so the user can resolve them

---

## Deep Dive

**LOAD reference when:**
- Creating issues with complex fields, JSON input, or multi-line content
- Building JQL queries beyond simple filters
- Troubleshooting errors or authentication issues
- Working with transitions, linking, or sprints
- Bulk operations (edit/transition/assign via JQL or filter)

**Do NOT load reference for:**
- Simple view/list/search operations (Quick Reference above is sufficient)
- Basic status checks
- Opening issues in browser

| Task | Load Reference? |
|------|-----------------|
| View single issue | No |
| List my tickets | No |
| Create with description | No -- use `-d` or `--description-file` |
| Create from JSON | **Yes** -- need `--generate-json` pattern |
| Transition issue | No -- but verify available statuses first |
| Complex JQL search | **Yes** -- for query syntax |
| Link issues | No |
| Bulk operations | **Yes** -- for JQL/filter patterns |

Reference:
- ACLI patterns: `references/acli.md`
