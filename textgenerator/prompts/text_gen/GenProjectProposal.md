---
PromptInfo:
  promptId: GenProjectProposal
  name: Generate Project Proposal
  description: Generates a detailed project proposal based on user-provided context, including objectives, methodology, timeline, and budget.
  author: JDLanctot
  tags: project, proposal, planning, management
  version: 0.0.1
bodyParams:
  max_tokens: 1000
---

# GenProjectProposal

## Prompt

### Instructions
- Provide a clear and concise description of your project.
- Include specific objectives and goals.
- Describe the intended methodology or approach.
- Specify the desired timeline and key milestones.
- Outline an estimated budget, including major cost areas.

### Context
- **Project Description**: {{project_description}}
- **Objectives and Goals**: {{objectives_goals}}
- **Methodology/Approach**: {{methodology_approach}}
- **Timeline and Milestones**: {{timeline_milestones}}
- **Budget Overview**: {{budget_overview}}

### Response
<!-- The generated project proposal will appear below -->

