# gog CLI Skill for Claude

A Claude skill that provides command reference and workflows for [gog](https://github.com/steipete/gogcli) - a fast, script-friendly CLI for Google Workspace services.

## What is gog?

[gog](https://github.com/steipete/gogcli) by [@steipete](https://github.com/steipete) is a CLI for Gmail, Calendar, Drive, Chat, Tasks, Sheets, Docs, Slides, Contacts, People, Groups, Classroom, and Keep. It features JSON-first output, multiple account support, and least-privilege authentication.

**This skill requires gog CLI to be installed.** See [gog installation instructions](https://github.com/steipete/gogcli#installation).

## What This Skill Does

Teaches Claude how to use gog CLI commands for Google Workspace operations:

- **Gmail** - Search, read, send, reply, manage labels, batch operations
- **Calendar** - List events, create meetings, check availability, respond to invites
- **Drive** - Upload, download, search, share files
- **Tasks** - Create and manage task lists and tasks
- **Sheets** - Read, write, format spreadsheets
- **Docs/Slides** - Export, create, copy documents
- **Contacts** - Search and manage contacts
- **Chat** - Send messages, manage spaces (Workspace only)
- **And more** - Groups, Classroom, People, Keep

## Installation

### Claude Code (Recommended)

```
/plugin marketplace add mjfork/gog-cli-skill
/plugin install gog-cli@mjfork
```

### Claude.ai

Upload the `skills/gog/SKILL.md` file directly to Claude.ai's skill interface.

## Prerequisites

1. **Install gog CLI**:
   ```bash
   brew install steipete/tap/gogcli
   ```

2. **Set up OAuth credentials** (see [gog Quick Start](https://github.com/steipete/gogcli#quick-start))

3. **Authorize your account**:
   ```bash
   gog auth add you@gmail.com
   ```

## Usage Examples

Once the skill is loaded, Claude can help with:

```
"Check my unread emails from the last week"
→ gog gmail search 'is:unread newer_than:7d'

"What's on my calendar today?"
→ gog calendar events --today

"Send an email to john@example.com about the meeting"
→ gog gmail send --to="john@example.com" --subject="..." --body="..."

"Download the Q4 report from Drive"
→ gog drive search "Q4 report" && gog drive download <fileId>

"Add a task to review the PR"
→ gog tasks add <listId> --title="Review PR"
```

## Skill Contents

```
gog-cli-skill/
├── SKILL.md              # Main skill (loaded when triggered)
├── README.md             # This file
├── LICENSE               # Apache 2.0
├── examples/
│   └── workflows.md      # Detailed workflow examples
└── references/
    └── commands.md       # Complete command reference
```

## When This Skill Triggers

The skill activates when you ask Claude to:
- Check/read/send email
- View/create calendar events
- Search/upload/download Drive files
- Manage tasks
- Work with Sheets/Docs/Slides
- Search contacts
- Send chat messages
- Any Google Workspace CLI operation

## Attribution

This skill provides a Claude interface for **[gog CLI](https://github.com/steipete/gogcli)** by **[@steipete](https://github.com/steipete)**.

gog is licensed under the MIT License. This skill is a reference guide and does not include gog source code.

## Contributing

Issues and PRs welcome! Please ensure:
- Examples are tested with current gog version
- New commands are documented in both SKILL.md and references/commands.md
- Workflows are practical and based on real use cases

## License

Apache 2.0 - See [LICENSE](LICENSE)

## Related

- [gog CLI](https://github.com/steipete/gogcli) - The CLI this skill documents
- [awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) - Curated list of Claude skills
