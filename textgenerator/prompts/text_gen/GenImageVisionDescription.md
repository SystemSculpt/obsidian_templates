---
PromptInfo:
  promptId: GenImageVisionDescription
  name: Generate a description of an image using GPT-4-Vision.
  description: Generates a description of an image using GPT-4-Vision given an image in context.
  author: Mike at SystemSculpt
  tags: custom
  version: 0.0.1
disableProvider: true
---

```javascript
{{#script}}
const OPENAI_API_KEY = plugin.getApiKeys().openAIChat;

async function imageFileToBase64(vault, imageFilePath) {
  const imageFile = vault.getAbstractFileByPath(imageFilePath);
  const arrayBuffer = await vault.readBinary(imageFile);
  const base64String = arrayBufferToBase64(arrayBuffer);
  return `data:image/jpeg;base64,${base64String}`;
}

function arrayBufferToBase64(buffer) {
  let binary = '';
  const bytes = new Uint8Array(buffer);
  const len = bytes.byteLength;
  for (let i = 0; i < len; i++) {
    binary += String.fromCharCode(bytes[i]);
  }
  return window.btoa(binary);
}

async function askGPT4WithImage(promptText, base64Image) {
  try {
    const requestOptions = {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${OPENAI_API_KEY}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        model: 'gpt-4-vision-preview',
        messages: [
          {
            role: 'user',
            content: [
              { type: 'text', text: promptText },
              { type: 'image_url', image_url: base64Image }
            ]
          }
        ],
        max_tokens: 300
      })
    };

    const response = await fetch('https://api.openai.com/v1/chat/completions', requestOptions);
    if (!response.ok) {
      throw new Error(`OpenAI API request failed with status ${response.status}`);
    }
    const chatCompletionData = await response.json();
    console.log(chatCompletionData);
    return chatCompletionData.choices[0].message.content;
  } catch (error) {
    console.error('Error in asking GPT-4 with an image:', error);
    throw error;
  }
}

async function resolveFullPathInVault(filename) {
  // Get all the markdown files in the vault.
  const files = app.vault.getFiles();

  // Find the file with the specified filename.
  const file = files.find(file => file.name === filename);

  // If the file is found, return its path.
  return file ? file.path : null;
}

const selection = this.tg_selection;
const filename = selection.match(/\[\[(.*?)\]\]/)[1]; // This regex extracts the filename from the selection
const fullPath = await resolveFullPathInVault(filename);

console.log({fullPath})

// Example usage:
const PROMPT_TEXT = "Describe what's inside of the image.";
const IMAGE_FILE_PATH = fullPath;

// Get the Base64 string for the image
const base64Image = await imageFileToBase64(app.vault, IMAGE_FILE_PATH);

// Use the Base64 string as an image URL to call the API
const content = await askGPT4WithImage(PROMPT_TEXT, base64Image);
const editor = app.workspace.getMostRecentLeaf().view.editor;
editor.insertText(content);

{{/script}}
```
