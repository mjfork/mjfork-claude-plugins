# PR Template for awesome-claude-skills

## PR Title
Add gog CLI skill

## PR Description

### What problem does this skill solve?
Provides Claude with command reference for [gog CLI](https://github.com/steipete/gogcli) - a fast, script-friendly CLI for Google Workspace services (Gmail, Calendar, Drive, Chat, Tasks, Sheets, Docs, Slides, Contacts, People, Groups, Classroom, Keep).

### Who uses this workflow?
- Developers and power users who prefer CLI over web UI
- Users managing multiple Google accounts
- Automation/scripting workflows with Google Workspace
- Anyone using gog CLI who wants Claude assistance with commands

### Attribution
**Credit:** [gog CLI](https://github.com/steipete/gogcli) by [@steipete](https://github.com/steipete)

### Example usage
```
User: "Check my unread emails from the last week"
Claude: gog gmail search 'is:unread newer_than:7d'

User: "What's on my calendar today?"
Claude: gog calendar events --today

User: "Send an email to john@example.com about the meeting"
Claude: gog gmail send --to="john@example.com" --subject="Meeting" --body="..."
```

### Difference from existing google-workspace-skills
The existing google-workspace-skills by @sanjay3290 provides API-based OAuth integration. This skill is for users of the **gog CLI tool** - a different approach using a local command-line interface.

---

## README.md Entry

Add to **Collaboration & Project Management** section (alphabetically):

```markdown
- [gog CLI](https://github.com/mjfork/gog-cli-skill) - Command reference for gog CLI, a fast CLI for Google Workspace (Gmail, Calendar, Drive, Chat, Tasks, Sheets, Docs, Slides). *Credit: [gog](https://github.com/steipete/gogcli) by [@steipete](https://github.com/steipete)*
```

---

## Next Steps

1. Create GitHub repo: `gh repo create gog-cli-skill --public --source=. --push`
2. Fork awesome-claude-skills
3. Add entry to README.md
4. Submit PR with description above
