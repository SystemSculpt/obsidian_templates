---
PromptInfo:
  promptId: genWikiEntry
  name: Generate a Wiki Entry
  description: Generates a markdown wiki entry based on the context of the active note.
  author: SystemSculpt
  tags: wiki, markdown
  version: 0.0.2
bodyParams:
  max_tokens: 2000
---

# GenWikiEntry

## Prompt

- Craft an expert-level explanation of the primary topic presented in the context.
- Prioritize concise and valuable information without unnecessary filler.
- Utilize Markdown formatting, incorporating H1, H2, and H3 headings where appropriate (using Markdown syntax).
- Maintain clarity and avoid excessive prose or over-explanation.
- Extend your knowledge beyond the provided context, offering expert insights and enriching the topic with substantial and high-quality content.
- Enhance the context by not only reformatting but also adding valuable insights and expert perspectives.
- Context is the note's specific context - the entirety of what's currently inside of the note. Sometimes it's nothing more than a title, other times it's a lot of text for you to use as a reference and build further on.
- Additional context may mention what is expected from your generation, topics to explore and build on, things to mention, tonality, etc.

### Context

{{context}}

### Additional Context

{{additional_context}}

## Response
