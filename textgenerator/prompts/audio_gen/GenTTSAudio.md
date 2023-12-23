---
PromptInfo:
  promptId: GenTTSAudio
  name: Generate an audio TTS file.
  description: Generates an audio TTS file given text within {{tts}} delimeters.
  author: Mike at SystemSculpt
  tags: tts, audio
  version: 0.0.2
disableProvider: true
---

To select a different voice for the TTS, locate the line in the script that specifies the voice parameter (e.g., `voice: 'onyx'`) and replace 'onyx' with one of the following available voice options: 'alloy', 'echo', 'fable', 'onyx', 'nova', or 'shimmer'.

```javascript
{{#script}}
const OPENAI_API_KEY = plugin.getApiKeys().openAIChat;

let revolvingNotice;

function startRevolvingNotice() {
    let dots = '';
    revolvingNotice = new Notice('Generating audio' + dots, 0); // 0 duration for persistent notice
    const intervalId = setInterval(() => {
        if (dots.length < 3) {
            dots += '.';
        } else {
            dots = '';
        }
        revolvingNotice.setMessage('Generating audio' + dots);
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

async function generateAudioAndSaveToVault(text) {
    try {
        // Set up the request headers and body
        const requestOptions = {
            method: 'POST',
            headers: {
                'Authorization': `Bearer ${OPENAI_API_KEY}`,
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({
                model: 'tts-1-hd-1106',
                input: text,
                voice: 'onyx'
            })
        };

        const revolvingNoticeIntervalId = startRevolvingNotice();

        // Make the request to OpenAI's API
        const response = await fetch('https://api.openai.com/v1/audio/speech', requestOptions);
        if (!response.ok) {
            stopRevolvingNotice(revolvingNoticeIntervalId);
            throw new Error(`OpenAI API request failed with status ${response.status}`);
        }

        // Get the audio buffer from the response
        const audioBuffer = await response.arrayBuffer();

        // Generate a random file name
        const randomFileName = `generated-audio-${Math.random().toString(36).substring(2, 15)}.mp3`;
        const filePath = `mp3/${randomFileName}`;

        // Use Obsidian's Vault API to save the audio file in MP3 format
        const vault = app.vault;

        // Check if the "mp3" directory exists, if not, create it
        let mp3Dir = vault.getAbstractFileByPath('mp3');
        if (!mp3Dir) {
            await vault.createFolder('mp3');
        }

        // Check if file exists, if so, remove it before writing new content
        let existingFile = vault.getAbstractFileByPath(filePath);
        if (existingFile && existingFile instanceof TFile) {
            await vault.delete(existingFile);
        }

        await vault.createBinary(filePath, audioBuffer);

        stopRevolvingNotice(revolvingNoticeIntervalId);
        // Insert a link to the new audio file at the cursor position in the current note
        const editor = app.workspace.activeLeaf.view.editor;
        const cursorPosition = editor.getCursor();
        const linkText = `![Generated Audio](${filePath})\n`;
        editor.replaceRange(linkText, cursorPosition);
        new Notice('Your audio has been processed.', 6000); // Show the success message for 6 seconds

    } catch (error) {
        stopRevolvingNotice(revolvingNoticeIntervalId);
        new Notice(`Error: ${error.message}`, 6000); // Show the error message for 6 seconds
        console.error('Error generating or saving audio:', error);
    }
}

// Call the function to generate audio and save it from the entire content of the note
const editor = app.workspace.activeLeaf.view.editor;
const NOTE_CONTENT = editor.getValue();
const TTS_DELIMITER = "{{tts}}";
const parts = NOTE_CONTENT.split(TTS_DELIMITER);
let textToProcess = NOTE_CONTENT;

// Check if the delimiter is present and there is text between the delimiters
if (parts.length >= 3) {
    // Extract the text between the first two delimiters
    textToProcess = parts[1].trim();
}

await generateAudioAndSaveToVault(textToProcess);
{{/script}}
```
