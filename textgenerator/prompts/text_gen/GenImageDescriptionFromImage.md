---
PromptInfo:
  promptId: GenImageDescriptionFromImage
  name: Generate a description for an image from an image.
  description: Generates a detailed description for an image by sending it to the OpenAI API, which can later be used to generate a similar image.
  author: SystemSculpt
  tags: image, description, openai, vision
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

let revolvingNotice;

function startRevolvingNotice() {
    let dots = '';
    revolvingNotice = new Notice('Generating a description from your image' + dots, 0); // 0 duration for persistent notice
    const intervalId = setInterval(() => {
        if (dots.length < 3) {
            dots += '.';
        } else {
            dots = '';
        }
        revolvingNotice.setMessage('Generating a description from your image' + dots);
    }, 1000); // Update the message every second

    return intervalId; // Return the interval ID so it can be cleared later
}

function stopRevolvingNotice(intervalId) {
    clearInterval(intervalId); // Stop the revolving text
    if (revolvingNotice) {
        revolvingNotice.hide(); // Hide the notice
        revolvingNotice = null; // Clear the reference
    }
}

async function askGPT4WithImage(promptText, base64Image) {
    const revolvingNoticeIntervalId = startRevolvingNotice();
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
    stopRevolvingNotice(revolvingNoticeIntervalId);
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
const PROMPT_TEXT = "Provide a detailed list of keywords that describe the visual elements present in the image, focusing on objects, colors, textures, mood, and any notable features or themes. We are trying to replicate the style, composition, and mood of the image. The more detailed the description, the better the results will be. For example, if the image is of a sunset, you could say 'sunset, orange, red, purple, clouds, water, ocean, beach, waves, calm, peaceful, relaxing, beautiful, serene, tranquil, etc. After providing the comma-separated list of keywords, please provide a short description of the image. For example, 'A beautiful sunset over the ocean with purple and orange clouds.'"
const IMAGE_FILE_PATH = fullPath;

// Get the Base64 string for the image
const base64Image = await imageFileToBase64(app.vault, IMAGE_FILE_PATH);

// Use the Base64 string as an image URL to call the API
const content = await askGPT4WithImage(PROMPT_TEXT, base64Image);
const editor = app.workspace.getMostRecentLeaf().view.editor;
editor.insertText(content);

{{/script}}
```
