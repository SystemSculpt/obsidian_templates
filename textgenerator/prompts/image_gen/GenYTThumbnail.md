---
PromptInfo:
  promptId: GenYouTubeThumbnail
  name: Generate a YouTube Thumbnail image.
  description: Generates an image given a image description.
  author: Mike at SystemSculpt
  tags: custom
  version: 0.0.1
disableProvider: true
---

```javascript
{{#script}}
const OPENAI_API_KEY = plugin.getApiKeys().openAIChat;

let revolvingNotice;

function startRevolvingNotice() {
    let dots = '';
    revolvingNotice = new Notice('Generating image' + dots, 0); // 0 duration for persistent notice
    const intervalId = setInterval(() => {
        if (dots.length < 3) {
            dots += '.';
        } else {
            dots = '';
        }
        revolvingNotice.setMessage('Generating image' + dots);
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

const revolvingNoticeIntervalId = startRevolvingNotice();

async function generateDalleImageAndSaveToVault(prompt) {
try {
// Set up the request headers and body
const requestOptions = {
method: 'POST',
headers: {
'Authorization': `Bearer ${OPENAI_API_KEY}`,
'Content-Type': 'application/json',
},
body: JSON.stringify({
model: 'dall-e-3',
prompt: prompt,
n: 1,
style: 'vivid',
quality: 'hd',
size: '1792x1024'
})
};

    // Make the request to OpenAI's API
    const response = await fetch('https://api.openai.com/v1/images/generations', requestOptions);

    if (!response.ok) {
      throw new Error(`OpenAI API request failed with status ${response.status}`);
    }

    // Get the image data from the response
    const imageData = await response.json();
    // Assuming the API returns a URL or binary data for the image
    const imageBinary = await requestUrl(imageData.data[0].url).then(res=>res.arrayBuffer)
    // Generate a random file name
    const randomFileName = `generated-image-${Math.random().toString(36).substring(2, 15)}.png`;
    const filePath = `images/${randomFileName}`; // Prepend the "images" directory to the file name

    // Use the Vault API to save the image file
    const vault = app.vault; // Assuming this script has access to `app` from the environment

    // Check if the "images" directory exists, if not, create it
    let imagesDir = vault.getAbstractFileByPath('images');
    if (!imagesDir) {
      await vault.createFolder('images');
    }

    // Check if file exists, if so, remove it before writing new content
    let existingFile = vault.getAbstractFileByPath(filePath);
    if (existingFile && existingFile instanceof TFile) {
      await vault.delete(existingFile);
    }

    await vault.createBinary(filePath, new Uint8Array(imageBinary));

    // Insert a link to the new image file into the current note and notify user
    const editor = app.workspace.getMostRecentLeaf().view.editor;
    const linkText = `![Generated Image](${filePath})\n`;
    editor.insertText(linkText);
    stopRevolvingNotice(revolvingNoticeIntervalId);
    new Notice('Your image has been processed.', 6000); // Show the success message for 6 seconds

} catch (error) {
stopRevolvingNotice(revolvingNoticeIntervalId);
new Notice(`Error: ${error.message}`, 6000); // Show the error message for 6 seconds
console.error('Error generating or saving image:', error);
}
}

// Call the function to generate an image and save it in the vault
const NOTE_CONTENT = app.workspace.activeLeaf.view.editor.getValue();
const IMAGE_DELIMITER = "[img]";
const parts = NOTE_CONTENT.split(IMAGE_DELIMITER);
let imagePrompt = NOTE_CONTENT;

// Check if the delimiter is present and there is text between the delimiters
if (parts.length >= 3) {
    // Extract the text between the first two delimiters
    imagePrompt = parts[1].trim();
}

await generateDalleImageAndSaveToVault(imagePrompt);

{{/script}}
```
