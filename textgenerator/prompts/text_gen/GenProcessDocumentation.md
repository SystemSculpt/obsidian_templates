---
PromptInfo:
  promptId: GenProcessDocumentation
  name: Generate Internal Process Documentation
  description: Creates detailed documentation for a specific internal business process based on user-provided context.
  author: JDLanctot
  tags: documentation, process, business, internal
  version: 0.0.1
bodyParams:
  max_tokens: 1200
---

# GenInternalProcessDoc

## Prompt

### Instructions
- Provide a brief overview of the process you want to document.
- Detail the specific steps involved in the process.
- Identify the roles and responsibilities associated with the process.
- Mention any tools, software, or resources required.
- Include any best practices, tips, or common pitfalls to avoid.

### Context
- **Process Overview**: {{process_overview}}
- **Process Steps**: {{process_steps}}
- **Roles and Responsibilities**: {{roles_responsibilities}}
- **Tools and Resources**: {{tools_resources}}
- **Best Practices and Tips**: {{best_practices_tips}}

### Response
<!-- The generated process documentation will appear below -->

