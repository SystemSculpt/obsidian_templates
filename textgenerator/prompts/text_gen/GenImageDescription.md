---
PromptInfo:
  promptId: GenImageDescription
  name: Generate a description for an image.
  description: Generates a description for an image given the context.
  author: SystemSculpt
  tags: image, description, text
  version: 0.0.1
bodyParams:
  max_tokens: 1000
---
# GenImageDescription

## Prompt
### Rules
- Never mention wording within the image description, it should totally be visuals based. Describe the imagery of the thumbnail, not anything else, no wording on it.
- Always mention "There are no words or letters in the image, only beautiful visuals."
- Make sure it makes visual sense and intrigues the viewer.
- Use the provided context to create the image description, no prose or filler, only quality visual imagery description.

### Instructions
1. **Be Specific and Detailed**: The more specific your prompt, the better the image quality. Include details like the setting, objects, colors, mood, and any specific elements you want in the image.
2. **Mood and Atmosphere**: Describe the mood or atmosphere you want to convey. Words like “serene,” “chaotic,” “mystical,” or “futuristic” can guide the AI in setting the right tone.
3. **Use Descriptive Adjectives**: Adjectives help in refining the image. For example, instead of saying “a dog,” say “a fluffy, small, brown dog.”
4. **Consider Perspective and Composition**: Mention if you want a close-up, a wide shot, a bird’s-eye view, or a specific angle. This helps in framing the scene correctly.
5. **Specify Lighting and Time of Day**: Lighting can dramatically change the mood of an image. Specify if it’s day or night, sunny or cloudy, or if there’s a specific light source like candlelight or neon lights.
6. **Incorporate Action or Movement**: If you want a dynamic image, describe actions or movements. For instance, “a cat jumping over a fence” is more dynamic than just “a cat.”
7. **Avoid Overloading the Prompt**: While details are good, too many can confuse the AI. Try to strike a balance between being descriptive and being concise.
8. **Use Analogies or Comparisons**: Sometimes it helps to compare what you want with something well-known, like “in the style of Van Gogh” or “resembling a scene from a fantasy novel.”
9. **Specify Desired Styles or Themes**: If you have a particular artistic style or theme in mind, mention it. For example, “cyberpunk,” “art deco,” or “minimalist.”

### Generation Optional Notes
{{optional_notes}}

### Generation Context
{{content}}

### Response
[img]
your generated description in here
[img]
