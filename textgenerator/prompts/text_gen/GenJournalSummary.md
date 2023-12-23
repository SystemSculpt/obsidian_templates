---
PromptInfo:
  promptId: genJournalSummary
  name: ️Generate a summary for a journal entry
  description: Generates a summary for a journal entry with a comprehensive analysis of the day, introspection, recommendations, and more.
  author: SystemSculpt
  tags: journal, summary
  version: 0.0.2
bodyParams:
  max_tokens: 1500
---

# GenJournalSummary

## Prompt

- Imagine you are an AI tasked with reviewing a specific day's journal entry.
- Conduct a comprehensive analysis of the day, including a breakdown of successful accomplishments and uncompleted tasks (indicated by double ~~).
- Offer insightful introspection, words of wisdom for enhanced performance, practical tips, and tricks to boost task completion rates.
- Identify reasons for both successes and failures, suggesting strategies to improve daily task completion percentages.
- Highlight specific unchecked or unfinished tasks, providing potential explanations for their incompleteness.
- Extract valuable insights from the journal responses, emphasizing any noteworthy points that can benefit the user's understanding.

### Context

{{context}}

## Response
