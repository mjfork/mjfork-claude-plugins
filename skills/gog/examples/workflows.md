# gog CLI Workflow Examples

## Email Triage

```bash
# Check unread emails from the last week
gog gmail search 'is:unread newer_than:7d' --max 20

# Search for emails with attachments
gog gmail search 'has:attachment newer_than:7d' --max 10

# Read a specific thread with all messages
gog gmail thread get <threadId>

# Download attachments from a thread
gog gmail thread get <threadId> --download
gog gmail thread get <threadId> --download --out-dir ./attachments

# Reply to a thread
gog gmail send --thread-id=<threadId> --reply-all --subject="Re: Topic" --body="Thanks for the update."

# Archive thread (remove from inbox)
gog gmail thread modify <threadId> --remove INBOX

# Star and archive
gog gmail thread modify <threadId> --add STARRED --remove INBOX
```

## Batch Email Processing

```bash
# Mark all emails from a sender as read
gog --json gmail search 'from:noreply@example.com is:unread' --max 200 | \
  jq -r '.threads[].id' | \
  xargs -n 50 gog gmail batch modify --remove UNREAD

# Archive old emails (older than 1 year)
gog --json gmail search 'older_than:1y in:inbox' --max 200 | \
  jq -r '.threads[].id' | \
  xargs -n 50 gog gmail batch modify --remove INBOX

# Label important emails
gog --json gmail search 'from:boss@example.com' --max 200 | \
  jq -r '.threads[].id' | \
  xargs -n 50 gog gmail batch modify --add IMPORTANT

# Delete spam/promotional (careful!)
gog --json gmail search 'category:promotions older_than:30d' --max 100 | \
  jq -r '.threads[].id' | \
  xargs -n 50 gog gmail batch delete
```

## Sending Emails

```bash
# Simple email
gog gmail send --to="user@example.com" --subject="Hello" --body="Message body"

# With CC and attachments
gog gmail send \
  --to="primary@example.com" \
  --cc="copy@example.com" \
  --subject="Q4 Report" \
  --body="Please find the report attached." \
  --attach=./report.pdf \
  --attach=./data.xlsx

# HTML email with plain text fallback
gog gmail send \
  --to="user@example.com" \
  --subject="Newsletter" \
  --body="Plain text version for email clients that don't support HTML" \
  --body-html="<h1>Newsletter</h1><p>Rich HTML content here</p>"

# Body from file
gog gmail send --to="user@example.com" --subject="Update" --body-file=./message.txt

# Body from stdin (pipe content)
echo "Dynamic content here" | gog gmail send --to="user@example.com" --subject="Update" --body-file -
```

## Calendar Management

```bash
# View today's schedule
gog calendar events --today

# View the week ahead
gog calendar events --week
gog calendar events --days 7

# Search for meetings
gog calendar search "standup" --days 30
gog calendar search "1:1" --from today --to "next month"

# Check for conflicts
gog calendar conflicts --today
gog calendar conflicts --from monday --to friday
```

## Scheduling Meetings

```bash
# Find free time slots
gog calendar freebusy --calendars="primary" --from="monday 9am" --to="monday 5pm"

# Check multiple people's availability
gog calendar freebusy \
  --calendars="primary,alice@company.com,bob@company.com" \
  --from="2025-02-01T09:00:00" \
  --to="2025-02-01T17:00:00"

# Create a meeting with Google Meet
gog calendar create primary \
  --summary="Team Standup" \
  --from="monday 10am" \
  --to="monday 10:30am" \
  --attendees="alice@example.com,bob@example.com" \
  --with-meet \
  --description="Daily sync meeting"

# Create recurring meeting
gog calendar create primary \
  --summary="Weekly Sync" \
  --from="monday 2pm" \
  --to="monday 3pm" \
  --attendees="team@company.com" \
  --rrule="RRULE:FREQ=WEEKLY;BYDAY=MO" \
  --with-meet

# Create meeting with custom reminders
gog calendar create primary \
  --summary="Important Meeting" \
  --from="tomorrow 3pm" \
  --to="tomorrow 4pm" \
  --reminder="email:1d" \
  --reminder="popup:30m" \
  --reminder="popup:10m"

# All-day event
gog calendar create primary \
  --summary="Company Holiday" \
  --from="2025-12-25" \
  --to="2025-12-26" \
  --all-day
```

## Responding to Invitations

```bash
# Accept an invitation
gog calendar respond primary <eventId> --status=accepted

# Decline with notification
gog calendar respond primary <eventId> --status=declined --send-updates=all

# Tentatively accept
gog calendar respond primary <eventId> --status=tentative

# Propose a new time (opens browser)
gog calendar propose-time primary <eventId> --open
gog calendar propose-time primary <eventId> --decline --comment="Can we do 5pm instead?"
```

## Focus Time and Out of Office

```bash
# Block focus time
gog calendar focus-time --from="monday 9am" --to="monday 12pm"

# Focus time with auto-decline
gog calendar create primary \
  --event-type=focus-time \
  --from="tuesday 2pm" \
  --to="tuesday 5pm" \
  --focus-auto-decline=all \
  --focus-decline-message="In deep work mode, will respond later"

# Out of office
gog calendar out-of-office --from="2025-01-20" --to="2025-01-24" --all-day

# With auto-decline
gog calendar create primary \
  --event-type=out-of-office \
  --from="2025-01-20" \
  --to="2025-01-24" \
  --all-day \
  --ooo-auto-decline=all \
  --ooo-decline-message="On vacation, back Jan 25"

# Set working location
gog calendar working-location --type=office --office-label="HQ" --from="monday" --to="friday"
gog calendar working-location --type=home --from="friday" --to="saturday"
```

## Drive File Management

```bash
# List files in root
gog drive ls --max 50

# List files in a specific folder
gog drive ls --parent <folderId>

# Search for files
gog drive search "quarterly report"
gog drive search "invoice filetype:pdf"
gog drive search "presentation 2024"

# Get file metadata
gog drive get <fileId>

# Get web URL
gog drive url <fileId>
```

## Downloading and Exporting

```bash
# Download a file
gog drive download <fileId>
gog drive download <fileId> --out ./local-copy.pdf

# Export Google Docs to different formats
gog drive download <docId> --format pdf --out ./document.pdf
gog drive download <docId> --format docx --out ./document.docx

# Export Google Sheets
gog sheets export <spreadsheetId> --format xlsx --out ./spreadsheet.xlsx
gog sheets export <spreadsheetId> --format csv --out ./data.csv
gog sheets export <spreadsheetId> --format pdf --out ./report.pdf

# Export Google Slides
gog slides export <presentationId> --format pptx --out ./deck.pptx
gog slides export <presentationId> --format pdf --out ./deck.pdf

# Read Google Doc as text
gog docs cat <docId>
```

## Uploading and Organizing

```bash
# Upload a file to root
gog drive upload ./report.pdf

# Upload to specific folder
gog drive upload ./report.pdf --parent <folderId>

# Create folder structure
gog drive mkdir "2025 Reports"
gog drive mkdir "Q1" --parent <2025ReportsFolderId>

# Move file to folder
gog drive move <fileId> --parent <folderId>

# Rename file
gog drive rename <fileId> "Q1 2025 Financial Report.pdf"

# Copy file
gog drive copy <fileId> "Report Copy"
```

## Sharing Files

```bash
# Share with specific person (read-only)
gog drive share <fileId> --email="colleague@company.com" --role=reader

# Share with edit access
gog drive share <fileId> --email="collaborator@company.com" --role=writer

# Share with a group
gog drive share <fileId> --email="team@company.com" --role=reader

# View current permissions
gog drive permissions <fileId>

# Remove sharing
gog drive unshare <fileId> --permission-id=<permissionId>
```

## Batch Download PDFs from Drive

```bash
# Find and download all PDFs matching a search
gog --json drive search "invoice filetype:pdf" --max 50 | \
  jq -r '.files[] | .id' | \
  while read fileId; do
    gog drive download "$fileId" --out "./invoices/"
  done
```

## Task Management

```bash
# List all task lists
gog tasks lists

# Create a new task list
gog tasks lists create "Work Projects"

# View tasks in a list
gog tasks list <tasklistId>

# Add tasks
gog tasks add <tasklistId> --title="Review PR #123"
gog tasks add <tasklistId> --title="Prepare presentation" --due="friday"

# Add recurring task
gog tasks add <tasklistId> --title="Weekly report" --due="monday" --repeat=weekly --repeat-count=12

# Complete a task
gog tasks done <tasklistId> <taskId>

# Clear completed tasks
gog tasks clear <tasklistId>
```

## Spreadsheet Operations

```bash
# Read a range
gog sheets get <spreadsheetId> 'Sheet1!A1:D100'
gog sheets get <spreadsheetId> 'Sheet1!A1:D100' --json

# Get spreadsheet metadata (sheet names, etc.)
gog sheets metadata <spreadsheetId>

# Update cells (pipe-delimited values)
gog sheets update <spreadsheetId> 'Sheet1!A1' 'value1|value2|value3'

# Update with JSON (for complex data)
gog sheets update <spreadsheetId> 'Sheet1!A1' --values-json '[["Name","Email"],["John","john@x.com"]]'

# Append a row
gog sheets append <spreadsheetId> 'Sheet1!A:D' 'new|row|of|data'

# Clear a range
gog sheets clear <spreadsheetId> 'Sheet1!A1:D100'

# Create new spreadsheet
gog sheets create "Sales Data 2025" --sheets="Q1,Q2,Q3,Q4"

# Copy spreadsheet
gog sheets copy <spreadsheetId> "Sales Data 2025 - Backup"
```

## Update Sheet from CSV

```bash
# Convert CSV to pipe-delimited and update
cat data.csv | tr ',' '|' | gog sheets update <spreadsheetId> 'Sheet1!A1'

# Or use JSON for complex data
cat data.json | jq -c '[.[] | [.name, .email, .phone]]' | \
  xargs -I {} gog sheets update <spreadsheetId> 'Sheet1!A1' --values-json '{}'
```

## Multi-Account Workflows

```bash
# Set up account aliases
gog auth alias set work work@company.com
gog auth alias set personal me@gmail.com

# Check work email
gog gmail search 'is:unread' --account=work

# Check personal calendar
gog calendar events --today --account=personal

# Compare calendars
echo "=== Work ===" && gog calendar events --today --account=work
echo "=== Personal ===" && gog calendar events --today --account=personal

# Set default for session
export GOG_ACCOUNT=work
gog gmail search 'is:unread'  # uses work account
```

## Scripting with JSON Output

```bash
# Get thread IDs for processing
gog --json gmail search 'newer_than:1d' | jq -r '.threads[].id'

# Filter files by type
gog --json drive ls | jq '.files[] | select(.mimeType | contains("pdf"))'

# Get event details
gog --json calendar events --today | jq '.events[] | {summary, start: .start.dateTime}'

# Count unread by sender
gog --json gmail search 'is:unread' --max 100 | \
  jq -r '.threads[].messages[0].from' | sort | uniq -c | sort -rn

# Export contacts to CSV
gog --json contacts list --max 500 | \
  jq -r '.connections[] | [.names[0].displayName, .emailAddresses[0].value] | @csv'
```

## Environment Variables

```bash
# Set default account
export GOG_ACCOUNT=work@company.com

# Set default output format
export GOG_JSON=1        # always output JSON
export GOG_PLAIN=1       # always output TSV

# Set default timezone for calendar
export GOG_TIMEZONE=America/New_York

# Restrict available commands (for sandboxed/agent use)
export GOG_ENABLE_COMMANDS=calendar,tasks

# Set color mode
export GOG_COLOR=never   # for piping to files
```

## Chat Workflows (Workspace Only)

```bash
# List chat spaces
gog chat spaces list

# Find a space by name
gog chat spaces find "Engineering"

# Create a new space with members
gog chat spaces create "Project Alpha" --member="alice@company.com" --member="bob@company.com"

# Send a message to a space
gog chat messages send spaces/<spaceId> --text="Build complete!"

# Reply in a thread
gog chat messages send spaces/<spaceId> --text="Thanks!" --thread=spaces/<spaceId>/threads/<threadId>

# Send a DM
gog chat dm send user@company.com --text="Quick question..."

# List unread messages
gog chat messages list spaces/<spaceId> --unread
```

## Classroom Workflows (Education)

```bash
# List your courses
gog classroom courses list
gog classroom courses list --role teacher

# View course roster
gog classroom roster <courseId>

# Create coursework
gog classroom coursework create <courseId> \
  --title="Homework 1" \
  --type=ASSIGNMENT \
  --state=PUBLISHED

# Grade submissions
gog classroom submissions list <courseId> <courseworkId>
gog classroom submissions grade <courseId> <courseworkId> <submissionId> --grade=85
gog classroom submissions return <courseId> <courseworkId> <submissionId>

# Post announcement
gog classroom announcements create <courseId> --text="Class cancelled tomorrow"
```
