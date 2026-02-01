# gog CLI Complete Command Reference

## Global Flags (All Commands)

```
--account=STRING      Account email for API commands
--client=STRING       OAuth client name (default: "think-simple")
--json                Output JSON to stdout
--plain               Output TSV (no colors)
--force               Skip confirmations
--no-input            Never prompt; fail instead
--verbose             Enable verbose logging
--color=auto|always|never
```

---

## auth - Authentication

```bash
gog auth list                    # List stored accounts
gog auth add <email>             # Authorize new account
gog auth remove <email>          # Remove stored token
gog auth status                  # Show auth config
gog auth services                # List supported services/scopes
gog auth credentials add         # Add OAuth client credentials
gog auth alias set <alias> <email>  # Set account alias
gog auth keyring [backend]       # Configure keyring backend
```

---

## gmail - Email

### Read Operations

```bash
# Search threads
gog gmail search <query>
    --max=10              # Max results
    --oldest              # Show first message date instead of last
    --timezone=STRING     # Output timezone (IANA name)

# Get message
gog gmail get <messageId>
    --format=full|metadata|raw
    --headers=STRING      # For metadata format

# Get thread with all messages
gog gmail thread get <threadId>
    --download            # Download attachments
    --format=full|metadata|raw

# List attachments in thread
gog gmail thread attachments <threadId>

# Download attachment
gog gmail attachment <messageId> <attachmentId>
    --output=STRING       # Output path

# Get web URL
gog gmail url <threadId> ...
```

### Write Operations

```bash
# Send email
gog gmail send
    --to=STRING           # Recipients (comma-separated, required)
    --cc=STRING           # CC recipients
    --bcc=STRING          # BCC recipients
    --subject=STRING      # Subject (required)
    --body=STRING         # Body text (required unless --body-html)
    --body-file=STRING    # Body from file ('-' for stdin)
    --body-html=STRING    # HTML body
    --attach=PATH,...     # Attachments (repeatable)
    --reply-to-message-id=STRING  # Reply to message
    --thread-id=STRING    # Reply in thread
    --reply-all           # Auto-populate recipients from original
    --from=STRING         # Send-as alias
    --track               # Enable open tracking
```

### Organize Operations

```bash
# Modify thread labels
gog gmail thread modify <threadId>
    --add-labels=STRING
    --remove-labels=STRING

# Label operations
gog gmail labels list
gog gmail labels get <labelId>
gog gmail labels create <name>
gog gmail labels delete <labelId>

# Batch operations
gog gmail batch modify <query>
    --add-labels=STRING
    --remove-labels=STRING
```

---

## calendar - Events

### List/Read

```bash
# List calendars
gog calendar calendars

# List events
gog calendar events [calendarId]
    --from=STRING         # Start time (RFC3339, date, or relative)
    --to=STRING           # End time
    --today               # Today only
    --tomorrow            # Tomorrow only
    --week                # This week
    --days=N              # Next N days
    --all                 # All calendars
    --max=10              # Max results
    --query=STRING        # Free text search
    --weekday             # Include day-of-week columns

# Get single event
gog calendar event <calendarId> <eventId>

# Search events
gog calendar search <query>

# Free/busy
gog calendar freebusy <calendarIds>
    --from=STRING
    --to=STRING

# Find conflicts
gog calendar conflicts
```

### Create/Update

```bash
# Create event
gog calendar create <calendarId>
    --summary=STRING      # Title (required)
    --from=STRING         # Start time (required)
    --to=STRING           # End time (required)
    --description=STRING
    --location=STRING
    --attendees=STRING    # Comma-separated emails
    --all-day             # All-day event
    --with-meet           # Add Google Meet
    --reminder=METHOD:DURATION  # e.g., popup:30m, email:1d
    --visibility=default|public|private|confidential
    --transparency=opaque|transparent|busy|free
    --send-updates=all|externalOnly|none
    --guests-can-invite
    --guests-can-modify
    --rrule=STRING        # Recurrence rule

# Update event
gog calendar update <calendarId> <eventId>
    # Same flags as create

# Delete event
gog calendar delete <calendarId> <eventId>

# Respond to invitation
gog calendar respond <calendarId> <eventId>
    --status=accepted|declined|tentative
    --comment=STRING
```

### Special Event Types

```bash
# Focus Time
gog calendar focus-time --from=STRING --to=STRING [calendarId]
    --focus-auto-decline=none|all|new
    --focus-decline-message=STRING
    --focus-chat-status=available|doNotDisturb

# Out of Office
gog calendar out-of-office --from=STRING --to=STRING [calendarId]
    --ooo-auto-decline=none|all|new
    --ooo-decline-message=STRING

# Working Location
gog calendar working-location --from=STRING --to=STRING --type=home|office|custom
```

---

## drive - Files

```bash
# List files
gog drive ls
    --folder=STRING       # Folder ID
    --max=100
    --trashed             # Include trashed

# Search
gog drive search <query>
    --max=100

# Get metadata
gog drive get <fileId>

# Download
gog drive download <fileId>
    --output=STRING       # Output path
    --format=STRING       # Export format for Google Docs

# Upload
gog drive upload <localPath>
    --folder=STRING       # Parent folder ID
    --name=STRING         # Custom name

# Create folder
gog drive mkdir <name>
    --parent=STRING

# Move/rename/delete
gog drive move <fileId> --to=<folderId>
gog drive rename <fileId> <newName>
gog drive delete <fileId>

# Copy
gog drive copy <fileId> <name>
    --folder=STRING

# Sharing
gog drive share <fileId>
    --email=STRING
    --role=reader|writer|commenter|owner
    --type=user|group|domain|anyone

gog drive unshare <fileId> <permissionId>
gog drive permissions <fileId>

# Comments
gog drive comments list <fileId>
gog drive comments add <fileId> --content=STRING
```

---

## tasks - Task Lists

```bash
# List task lists
gog tasks lists
gog tasks lists create <title>
gog tasks lists delete <tasklistId>

# List tasks
gog tasks list <tasklistId>
    --show-completed
    --show-hidden

# Task operations
gog tasks get <tasklistId> <taskId>

gog tasks add <tasklistId>
    --title=STRING
    --notes=STRING
    --due=STRING

gog tasks update <tasklistId> <taskId>
    --title=STRING
    --notes=STRING
    --due=STRING

gog tasks done <tasklistId> <taskId>
gog tasks undo <tasklistId> <taskId>
gog tasks delete <tasklistId> <taskId>
gog tasks clear <tasklistId>    # Clear completed
```

---

## sheets - Spreadsheets

```bash
# Read
gog sheets get <spreadsheetId> <range>
    --value-render=FORMATTED_VALUE|UNFORMATTED_VALUE|FORMULA

gog sheets metadata <spreadsheetId>

# Write
gog sheets update <spreadsheetId> <range> [values...]
    --value-input=RAW|USER_ENTERED

gog sheets append <spreadsheetId> <range> [values...]
    --value-input=RAW|USER_ENTERED

gog sheets clear <spreadsheetId> <range>

# Format
gog sheets format <spreadsheetId> <range>
    --bold
    --italic
    --bg-color=STRING
    --text-color=STRING

# Create/copy/export
gog sheets create <title>
gog sheets copy <spreadsheetId> <title>
gog sheets export <spreadsheetId>
    --format=pdf|xlsx|csv
```

---

## docs - Documents

```bash
gog docs cat <docId>             # Print as plain text
gog docs info <docId>            # Get metadata
gog docs export <docId>
    --format=pdf|docx|txt
gog docs create <title>
gog docs copy <docId> <title>
```

---

## slides - Presentations

```bash
gog slides info <presentationId>
gog slides export <presentationId>
    --format=pdf|pptx
gog slides create <title>
gog slides copy <presentationId> <title>
```

---

## contacts - Contacts

```bash
gog contacts search <query>
    --max=10

gog contacts list
    --max=100

gog contacts get <resourceName>

gog contacts create
    --given-name=STRING
    --family-name=STRING
    --email=STRING
    --phone=STRING

gog contacts update <resourceName>
    # Same flags as create

gog contacts delete <resourceName>

# Directory contacts (Workspace)
gog contacts directory list
gog contacts directory search <query>
```

---

## chat - Google Chat

```bash
# Spaces
gog chat spaces list
gog chat spaces get <spaceId>

# Messages
gog chat messages list <spaceId>
    --max=100
gog chat messages get <spaceId> <messageId>
gog chat messages send <spaceId> --text=STRING
gog chat messages delete <spaceId> <messageId>

# Threads
gog chat threads list <spaceId>
gog chat threads get <spaceId> <threadId>

# Direct messages
gog chat dm list
gog chat dm send <userId> --text=STRING
```

---

## people - User Profiles

```bash
gog people me                    # Your profile
gog people get <userId>          # User profile by ID
gog people search <query>        # Search Workspace directory
gog people relations [userId]    # Get user relations
```

---

## groups - Google Groups

```bash
gog groups list                  # Groups you belong to
gog groups members <groupEmail>  # List members
```

---

## classroom - Google Classroom

```bash
# Courses
gog classroom courses list
gog classroom courses get <courseId>

# Roster
gog classroom roster <courseId>
gog classroom students list <courseId>
gog classroom teachers list <courseId>

# Coursework
gog classroom coursework list <courseId>
gog classroom coursework get <courseId> <courseworkId>

# Submissions
gog classroom submissions list <courseId> <courseworkId>

# Announcements
gog classroom announcements list <courseId>
```

---

## config - Configuration

```bash
gog config show                  # Show config
gog config set <key> <value>     # Set config value
gog config unset <key>           # Unset config value
```

---

## time - Time Utilities

```bash
gog time now                     # Current time
gog time parse <timestring>      # Parse time string
```
