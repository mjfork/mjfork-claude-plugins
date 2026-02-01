---
name: gog
description: This skill should be used when the user asks to "check email", "read my mail", "send an email", "search gmail", "reply to email", "what's on my calendar", "schedule a meeting", "create an event", "find free time", "search drive", "download a file", "upload to drive", "share a file", "list my tasks", "add a task", "read a spreadsheet", "update a sheet", "search contacts", "send a chat message", "export document", or any Google Workspace operation. Provides gog CLI command reference for Gmail, Calendar, Drive, Chat, Tasks, Sheets, Docs, Slides, Contacts, People, Groups, Classroom, and Keep.
---

# gog CLI Reference

## Overview

[gog](https://github.com/steipete/gogcli) by [@steipete](https://github.com/steipete) is a CLI for Google Workspace: Gmail, Calendar, Drive, Chat, Tasks, Sheets, Docs, Slides, Contacts, People, Groups, Classroom, and Keep. JSON-first output, multiple accounts, least-privilege auth.

**Prerequisite:** Install gog via `brew install steipete/tap/gogcli` and authorize with `gog auth add you@gmail.com`.

## Global Flags

| Flag | Purpose |
|------|---------|
| `--account=EMAIL` | Select Google account (or alias) |
| `--json` | Output JSON to stdout (best for scripting) |
| `--plain` | Output TSV (parseable, no colors) |
| `--force` | Skip confirmations |
| `--no-input` | Never prompt; fail instead |
| `--verbose` | Enable verbose logging |

## Quick Reference

### Gmail

```bash
# Search (thread-level)
gog gmail search 'newer_than:7d'
gog gmail search 'from:boss@company.com is:unread' --max 20
gog gmail search 'has:attachment older_than:1y'

# Message-level search (one row per email)
gog gmail messages search 'newer_than:7d' --max 10
gog gmail messages search 'from:me' --include-body  # fetch body content

# Read thread with all messages
gog gmail thread get <threadId>
gog gmail thread get <threadId> --download  # with attachments
gog gmail thread get <threadId> --download --out-dir ./attachments

# Read single message
gog gmail get <messageId>
gog gmail get <messageId> --format raw  # full RFC822

# Send email
gog gmail send --to="user@example.com" --subject="Hello" --body="Message"
gog gmail send --to="a@x.com,b@x.com" --cc="c@x.com" --subject="Team" --body="Hi"
gog gmail send --to="user@x.com" --subject="Files" --body="See attached" --attach=file.pdf
gog gmail send --body-file=./message.txt --to="..." --subject="..."  # body from file
gog gmail send --body-file - --to="..." --subject="..."  # body from stdin

# Reply to thread
gog gmail send --thread-id=<threadId> --reply-all --subject="Re: Topic" --body="Reply"
gog gmail send --reply-to-message-id=<msgId> --subject="Re: Topic" --body="Reply"

# HTML email
gog gmail send --to="..." --subject="..." --body="Plain fallback" --body-html="<p>Hello</p>"

# Modify labels
gog gmail thread modify <threadId> --add STARRED --remove INBOX

# Drafts
gog gmail drafts list
gog gmail drafts create --to="..." --subject="Draft" --body="Body"
gog gmail drafts send <draftId>

# Labels
gog gmail labels list
gog gmail labels create "My Label"

# Batch operations
gog gmail batch modify <msgId1> <msgId2> --add STARRED --remove INBOX
```

### Calendar

```bash
# List events (timezone-aware)
gog calendar events                    # primary calendar
gog calendar events --today            # today only
gog calendar events --tomorrow         # tomorrow only
gog calendar events --week             # this week (Mon-Sun)
gog calendar events --days 7           # next 7 days
gog calendar events --from today --to friday
gog calendar events --all              # all calendars
gog calendar events --weekday          # include day-of-week columns

# Search events
gog calendar search "meeting" --today
gog calendar search "standup" --days 30

# Get single event
gog calendar event <calendarId> <eventId>
gog calendar event <calendarId> <eventId> --json  # includes timezone info

# Create event
gog calendar create primary --summary="Meeting" --from="2025-01-15T10:00:00" --to="2025-01-15T11:00:00"
gog calendar create primary --summary="Team Sync" --from="tomorrow 2pm" --to="tomorrow 3pm" \
  --attendees="alice@x.com,bob@x.com" --with-meet --location="Zoom"

# All-day event
gog calendar create primary --summary="Conference" --from="2025-03-01" --to="2025-03-03" --all-day

# Recurring event
gog calendar create primary --summary="Weekly Sync" --from="monday 10am" --to="monday 11am" \
  --rrule="RRULE:FREQ=WEEKLY;BYDAY=MO"

# With reminders
gog calendar create primary --summary="Payment" --from="..." --to="..." \
  --reminder="email:1d" --reminder="popup:30m"

# Update event
gog calendar update <calendarId> <eventId> --summary="New Title" --from="..." --to="..."
gog calendar update <calendarId> <eventId> --add-attendee="new@x.com"

# Delete event
gog calendar delete <calendarId> <eventId>

# Respond to invite
gog calendar respond <calendarId> <eventId> --status=accepted
gog calendar respond <calendarId> <eventId> --status=declined
gog calendar respond <calendarId> <eventId> --status=tentative

# Check availability
gog calendar freebusy --calendars="primary,coworker@x.com" --from="monday" --to="friday"
gog calendar conflicts --today

# Special event types
gog calendar focus-time --from="monday 9am" --to="monday 12pm"
gog calendar out-of-office --from="2025-01-20" --to="2025-01-22" --all-day
gog calendar working-location --type=office --office-label="HQ" --from="monday" --to="friday"

# Team calendar (Workspace)
gog calendar team <group-email> --today
gog calendar team <group-email> --week --freebusy
```

### Drive

```bash
# List/search
gog drive ls --max 20
gog drive ls --parent <folderId>
gog drive search "invoice" --max 20
gog drive search "invoice filetype:pdf"

# Get metadata / URL
gog drive get <fileId>
gog drive url <fileId>

# Download
gog drive download <fileId>
gog drive download <fileId> --out ./downloaded.pdf
gog drive download <fileId> --format pdf  # export Google Docs

# Upload
gog drive upload ./file.pdf
gog drive upload ./file.pdf --parent <folderId>

# Organize
gog drive mkdir "New Folder"
gog drive mkdir "Subfolder" --parent <parentId>
gog drive rename <fileId> "New Name"
gog drive move <fileId> --parent <destinationId>
gog drive copy <fileId> "Copy Name"
gog drive delete <fileId>

# Sharing
gog drive share <fileId> --email="user@x.com" --role=reader
gog drive share <fileId> --email="user@x.com" --role=writer
gog drive permissions <fileId>
gog drive unshare <fileId> --permission-id=<permId>

# Shared drives
gog drive drives
```

### Tasks

```bash
# Task lists
gog tasks lists
gog tasks lists create "My List"

# List tasks
gog tasks list <tasklistId>

# Create task
gog tasks add <tasklistId> --title="New task"
gog tasks add <tasklistId> --title="Due tomorrow" --due="2025-02-01"
gog tasks add <tasklistId> --title="Weekly sync" --due="2025-02-01" --repeat=weekly --repeat-count=4
gog tasks add <tasklistId> --title="Daily standup" --due="2025-02-01" --repeat=daily --repeat-until="2025-02-05"

# Update/complete
gog tasks update <tasklistId> <taskId> --title="Updated"
gog tasks done <tasklistId> <taskId>
gog tasks undo <tasklistId> <taskId>
gog tasks delete <tasklistId> <taskId>
gog tasks clear <tasklistId>  # clear completed
```

### Sheets

```bash
# Read
gog sheets get <spreadsheetId> 'Sheet1!A1:D10'
gog sheets metadata <spreadsheetId>

# Write (pipe-delimited for multiple cells)
gog sheets update <spreadsheetId> 'A1' 'val1|val2,val3|val4'
gog sheets update <spreadsheetId> 'A1' --values-json '[["a","b"],["c","d"]]'
gog sheets append <spreadsheetId> 'Sheet1!A:C' 'new|row|data'

# With validation copying
gog sheets update <spreadsheetId> 'Sheet1!A1:C1' 'new|row|data' --copy-validation-from='Sheet1!A2:C2'

gog sheets clear <spreadsheetId> 'Sheet1!A1:B10'

# Format
gog sheets format <spreadsheetId> 'Sheet1!A1:B2' --format-json='{"textFormat":{"bold":true}}' \
  --format-fields='userEnteredFormat.textFormat.bold'

# Create/copy/export
gog sheets create "New Sheet" --sheets="Sheet1,Sheet2"
gog sheets copy <spreadsheetId> "Copy Name"
gog sheets export <spreadsheetId> --format=pdf --out=./sheet.pdf
gog sheets export <spreadsheetId> --format=xlsx
gog sheets export <spreadsheetId> --format=csv
```

### Docs / Slides

```bash
# Docs
gog docs cat <docId>                   # print as plain text
gog docs cat <docId> --max-bytes=10000
gog docs info <docId>
gog docs create "New Document"
gog docs copy <docId> "Copy Title"
gog docs export <docId> --format=pdf --out=./doc.pdf
gog docs export <docId> --format=docx
gog docs export <docId> --format=txt

# Slides
gog slides info <presentationId>
gog slides create "New Deck"
gog slides copy <presentationId> "Copy Title"
gog slides export <presentationId> --format=pdf --out=./deck.pdf
gog slides export <presentationId> --format=pptx
```

### Contacts

```bash
# Search/list
gog contacts search "John"
gog contacts list --max 50
gog contacts get people/<resourceName>
gog contacts get user@example.com  # by email

# Other contacts (people you've interacted with)
gog contacts other list
gog contacts other search "Jane"

# Create/update
gog contacts create --given-name="John" --family-name="Doe" --email="john@x.com" --phone="+1234567890"
gog contacts update people/<resourceName> --email="new@x.com"
gog contacts delete people/<resourceName>

# Workspace directory
gog contacts directory list
gog contacts directory search "Jane"
```

### Chat (Workspace only)

```bash
# Spaces
gog chat spaces list
gog chat spaces find "Engineering"
gog chat spaces create "Team" --member="alice@x.com" --member="bob@x.com"

# Messages
gog chat messages list spaces/<spaceId>
gog chat messages list spaces/<spaceId> --unread
gog chat messages send spaces/<spaceId> --text="Hello!"

# Direct messages
gog chat dm space user@company.com
gog chat dm send user@company.com --text="ping"
```

### People / Groups

```bash
# People
gog people me
gog people get people/<userId>
gog people search "Ada Lovelace"
gog people relations  # manager/reports

# Groups (Workspace)
gog groups list
gog groups members engineering@company.com
```

## Gmail Query Syntax

| Operator | Example |
|----------|---------|
| `from:` | `from:boss@company.com` |
| `to:` | `to:team@company.com` |
| `subject:` | `subject:urgent` |
| `is:unread` | unread messages |
| `is:starred` | starred messages |
| `has:attachment` | messages with attachments |
| `newer_than:` | `newer_than:7d` (also `1m`, `1y`) |
| `older_than:` | `older_than:1y` |
| `after:` / `before:` | `after:2024/01/01` |
| `label:` | `label:important` |
| `in:inbox` | inbox messages |
| `filename:` | `filename:pdf` |

Combine: `from:boss is:unread OR is:starred`

## Account Discovery & Selection

**Always discover available accounts first:**

```bash
gog auth list                          # list all stored accounts
gog auth list --check                  # verify tokens are still valid
gog auth status                        # show current auth state
```

**Account selection order:**
1. `--account=EMAIL` flag (explicit)
2. `GOG_ACCOUNT` environment variable
3. `--account=auto` (picks default or single stored account)
4. Prompts if multiple accounts and none specified

**Using accounts:**

```bash
# Explicit account
gog gmail search "is:unread" --account=work@company.com
gog calendar events --today --account=personal@gmail.com

# Auto-select (uses default or single account)
gog gmail search "is:unread" --account=auto

# Set default for session
export GOG_ACCOUNT=work@company.com
gog gmail search "is:unread"           # uses work account

# Account aliases
gog auth alias set work work@company.com
gog auth alias set personal me@gmail.com
gog gmail search "is:unread" --account=work  # uses alias
```

**Best practice:** Run `gog auth list` first to see available accounts, then use `--account` explicitly or set `GOG_ACCOUNT`.

## Scripting with JSON

```bash
# Pipe to jq
gog --json gmail search 'newer_than:7d' | jq '.threads[].id'
gog --json drive ls | jq '.files[] | select(.mimeType=="application/pdf")'

# Batch process
gog --json gmail search 'from:noreply@example.com' --max 200 | \
  jq -r '.threads[].id' | xargs -n 50 gog gmail batch modify --remove UNREAD
```

## Additional Resources

For complete command reference: **`references/commands.md`**
For workflow examples: **`examples/workflows.md`**

---

**Credit:** This skill documents [gog CLI](https://github.com/steipete/gogcli) by [@steipete](https://github.com/steipete). gog is licensed under MIT.
