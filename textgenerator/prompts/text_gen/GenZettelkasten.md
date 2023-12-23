---
PromptInfo:
  promptId: GenZettelkasten
  name: Generate Customizable Zettelkasten Directory Structure
  description: Creates a customizable and non-destructive directory structure for a Zettelkasten system in Obsidian.
  author: Mike at SystemSculpt
  tags: zettelkasten, organization, customization, user-friendly
  version: 0.0.4
disableProvider: true
---

```javascript
{{#script}}
// USER CUSTOMIZATION SECTION
// Change the settings below to configure your Zettelkasten structure.
// Do not modify any code beyond the "END OF CUSTOMIZATION" line.
const userOptions = {
    rootDirectoryName: 'Zettelkasten', // Name your main Zettelkasten directory
    includeInbox: true, // Set to false to exclude the Inbox directory
    includeLiteratureNotes: true, // Set to false to exclude the Literature Notes directory
    includePermanentNotes: true, // Set to false to exclude the Permanent Notes directory
    includeProjectNotes: true, // Set to false to exclude the Project Notes directory
    includeReference: true, // Set to false to exclude the Reference directory
    includeArchive: true, // Set to false to exclude the Archive directory
    customDirectories: [] // Add custom directory names here, e.g., ['My Notes', 'Ideas']
};
// END OF CUSTOMIZATION

async function createZettelkastenStructure() {
    const defaultDirectories = {
        '01 Inbox': userOptions.includeInbox,
        '02 Literature Notes': userOptions.includeLiteratureNotes,
        '03 Permanent Notes': userOptions.includePermanentNotes,
        '04 Project Notes': userOptions.includeProjectNotes,
        '05 Reference': userOptions.includeReference,
        '06 Archive': userOptions.includeArchive
    };

    try {

        async function createDirectoryWithFallback(parentDir, dirName) {
            let dirPath = parentDir + '/' + dirName;
            let dirExists = await vault.adapter.exists(dirPath);
            let dirCounter = 1;

            while (dirExists) {
                dirCounter++;
                dirPath = parentDir + '/' + dirName.replace(/ \d+$/, '') + ' ' + dirCounter;
                dirExists = await vault.adapter.exists(dirPath);
            }

            await vault.createFolder(dirPath);
            return dirPath;
        }

        const vault = app.vault;
        const rootDirName = await createDirectoryWithFallback('', userOptions.rootDirectoryName);


        async function createDirectoryStructure(parentDir, directories) {
            for (const [dirName, include] of Object.entries(directories)) {
                if (include) {
                    const dirPath = await createDirectoryWithFallback(parentDir, dirName);
                    await createDirectoryStructure(dirPath, userOptions.customDirectories);
                }
            }
        }

        await createDirectoryStructure(rootDirName, defaultDirectories);

        new Notice('Zettelkasten directory structure created successfully in ' + rootDirName + '.', 6000);
    } catch (error) {
        new Notice(`Error: ${error.message}`, 6000);
        console.error('Error creating Zettelkasten directory structure:', error);
    }
}

await createZettelkastenStructure();
{{/script}}
```
