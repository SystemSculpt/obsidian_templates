---
PromptInfo:
  promptId: genRelations
  name: Generate relations (links) from context
  description: Generate suggestions for relations given the context from the currently active note.
  author: SystemSculpt
  tags: relations, obsidian, linking
  version: 0.0.2
bodyParams:
  max_tokens: 200
---

# GenRelations

## Prompt

- Suggest relation links in markdown format for my Obsidian Zettelkasten, using examples from various categories to illustrate naming conventions.
- Enclose relations within double brackets (e.g., [[Category Name]]).
- Ensure that relations are broad enough for potential future links, avoiding overly obscure or specific names.
- Keep relation names concise to create a well-connected and manageable system.
- Ensure that the relations pertain to the main overarching idea of the content.
- Avoid repeating relation links within the text if they have already been created.
- Limit the number of relations to a maximum of 10.
- Maintain relation names that are simple and general for better future linkage opportunities.

Example Response Structure Begin
relations:

- "[[Cognitive Biases]]"
- "[[Decision Making]]"
- "[[Social Perception]]"
- "[[Cognitive Psychology]]"
- "[[Behavioral Economics]]"
- "[[Japanese Philosophy]]"
- "[[Life Purpose]]"
- "[[Personal Development]]"
- "[[Work-Life Balance]]"
- "[[Happiness]]"
  Example Response Structure End

### Context

{{context}}

## Response
