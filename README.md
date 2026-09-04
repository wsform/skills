# WS Form Skills

Official agent skills for building, managing, and integrating forms with WS Form.

These skills give AI coding agents WS Form-specific knowledge, workflows, architectural guidance, and best practices. They teach an agent how WS Form works and when to use native functionality instead of custom PHP or JavaScript.

They complement the WS Form Abilities API and MCP Server:

- **Skills** provide knowledge and workflow guidance.
- **Abilities and MCP** provide executable capabilities.

The skill is not a replacement for MCP or the Abilities API.

## Available Skills

### WS Form

The `ws-form` skill helps agents:

- Build and modify forms using native WS Form functionality
- Select appropriate field types
- Work with sections, tabs, and layouts
- Configure Conditional Logic
- Work with calculations
- Configure actions and integrations
- Work with submissions
- Use validation correctly
- Use spam protection correctly
- Style forms using supported WS Form mechanisms
- Troubleshoot forms and submissions
- Use WS Form Abilities and MCP effectively
- Avoid unnecessary custom PHP and JavaScript

## Installation

Install with the Agent Skills CLI:

```bash
npx skills add ws-form/skills --skill ws-form
```

To list skills in this repository without installing:

```bash
npx skills add ws-form/skills --list
```

## Changelog

See [changelog.txt](changelog.txt).

## Documentation

- [Abilities](https://wsform.com/knowledgebase/abilities/)
- [MCP Server](https://wsform.com/knowledgebase/mcp-server/)
- [Knowledge Base](https://wsform.com/knowledgebase/)
- [WS Form](https://wsform.com/)

## License

GNU General Public License v3.0 or later. See [LICENSE](LICENSE).

Copyright (C) Westguard Solutions.
