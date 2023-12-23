---
PromptInfo:
  promptId: RecordAndTranscribeAudio
  name: Record and Transcribe Audio
  description: Starts recording audio using Obsidian's built-in audio recorder and saves the recording to a file, later transcribing the audio using Whisper and inserting the transcription into the note.
  author: SystemSculpt
  tags: audio, recording, transcription, whisper, openai
  version: 0.0.1
disableProvider: true
---

```javascript
{{#script}}

// USER OPTIONS START

// Where to save the recording if saveRecording is true (relative to vault root)
const recordingDirectory = 'recordings';

// Key to press to start recording
const startRecordingKey = 'Shift';

// Set to false if you don't want to copy transcription to clipboard
const saveTranscriptionToClipboard = true;

// Set to false to create a temporary file and delete it after transcription
const saveRecording = true;

// Set to false if you don't want to paste the recording link into the note
const pasteRecordingLinkIntoNote = false;

// USER OPTIONS END

// USER - PLEASE READ - DO NOT TOUCH ANYTHING BELOW HERE!

const OPENAI_API_KEY = plugin.getApiKeys().openAIChat;
const vault = app.vault;

let revolvingNotice;
let mediaRecorder;
let revolvingNoticeIntervalId;
let audioChunks = [];

function startRevolvingNotice(message) {
    let dots = '';
    revolvingNotice = new Notice(message + dots, 0);
    revolvingNoticeIntervalId = setInterval(() => {
        dots = dots.length < 3 ? dots + '.' : '';
        revolvingNotice.setMessage(message + dots);
    }, 1000);

    return revolvingNoticeIntervalId;
}

function stopRevolvingNotice() {
    clearInterval(revolvingNoticeIntervalId);
    if (revolvingNotice) {
        revolvingNotice.hide();
        revolvingNotice = null;
    }
}

async function transcribeAudio(filePath) {
    startRevolvingNotice('Transcribing audio');
    try {
        const file = await vault.adapter.readBinary(filePath);
        const formData = new FormData();
        formData.append('file', new Blob([file]), filePath);
        formData.append('model', 'whisper-1');

        const requestOptions = {
            method: 'POST',
            headers: {
                'Authorization': `Bearer ${OPENAI_API_KEY}`
            },
            body: formData
        };

        const response = await fetch('https://api.openai.com/v1/audio/transcriptions', requestOptions);

        if (!response.ok) {
            throw new Error(`OpenAI API request failed with status ${response.status}`);
        }

        const result = await response.json();
        stopRevolvingNotice();
        return result.text;
    } catch (error) {
        stopRevolvingNotice();
        new Notice(`Error transcribing audio: ${error.message}`, 6000);
        console.error('Error transcribing audio:', error);
        return null;
    }
}

async function startRecordingAndSaveAudio() {
    try {
        if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
            new Notice('Audio recording is not supported in your environment.', 6000);
            return;
        }

        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        mediaRecorder = new MediaRecorder(stream);

        mediaRecorder.ondataavailable = function(event) {
            audioChunks.push(event.data);
        };

        mediaRecorder.onstop = async function() {
            stopRevolvingNotice();
            const audioBlob = new Blob(audioChunks, { type: 'audio/wav' });
            const audioBuffer = await audioBlob.arrayBuffer();

            let filePath;
            if (saveRecording) {
                // Save as YYYY-MM-DD-HH-MM-SS.wav, use current system time so the timezones match
                const dateFormat = 'YYYY-MM-DD HH[h]mm[m]ss[s]';
                const filePrefix = 'recording';
                const randomFileName = `${filePrefix} ${moment().format(dateFormat)}.wav`;
                filePath = `${recordingDirectory}/${randomFileName}`;

                let recordingsDir = vault.getAbstractFileByPath(recordingDirectory);
                if (!recordingsDir) {
                    await vault.createFolder(recordingDirectory);
                }

                await vault.createBinary(filePath, audioBuffer);
            } else {
                filePath = `temp-audio-recording.wav`;
                await vault.createBinary(filePath, audioBuffer);
            }

            const editor = app.workspace.activeLeaf.view.editor;
            const cursorPosition = editor.getCursor();
            if (pasteRecordingLinkIntoNote && saveRecording) {
                const linkText = `![[${filePath}]]`;
                editor.replaceRange(linkText, cursorPosition);
            }

            const transcription = await transcribeAudio(filePath);
            if (transcription) {
                editor.replaceRange(`${transcription}\n`, cursorPosition);
                if (saveTranscriptionToClipboard) {
                    navigator.clipboard.writeText(transcription);
                    new Notice('Transcription completed and copied to clipboard.', 6000);
                } else {
                    new Notice('Transcription completed.', 6000);
                }
            }

            if (!saveRecording) {
                await vault.adapter.remove(filePath);
            }
        };

        startRevolvingNotice('Recording audio (press ' + startRecordingKey + ' to stop)');

        mediaRecorder.start();

        const stopRecording = function() {
            if (mediaRecorder && mediaRecorder.state === 'recording') {
                mediaRecorder.stop();
                mediaRecorder.stream.getTracks().forEach(track => track.stop());
            }
            window.removeEventListener('keydown', stopRecordingListener);
        };

        const stopRecordingListener = function(e) {
            if (e.key === startRecordingKey) {
                stopRecording();
            }
        };

        window.addEventListener('keydown', stopRecordingListener);

    } catch (error) {
        stopRevolvingNotice();
        new Notice(`Error: ${error.message}`, 6000);
        console.error('Error in recording audio:', error);
    }
}

startRecordingAndSaveAudio();
{{/script}}

```
