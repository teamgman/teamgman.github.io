---
draft: true
---

```dataviewjs
const pages = dv.pages('"technical-notes"')
    .sort(p => p.file.name, 'asc');

let markdownOutput = "";

pages.forEach(p => {
    let displayName = p.file.name;
    if (p.aliases && p.aliases.length > 0) {
        displayName = Array.isArray(p.aliases) ? p.aliases[0] : p.aliases;
    } else if (p.title) {
        displayName = p.title;
    }
    
    // Strip the .md extension from the path for standard Obsidian linking
    const cleanPath = p.file.path.replace(/\.md$/, "");
    
    // Construct the explicit markdown line
    markdownOutput += `- [[${cleanPath}|${displayName}]]\n`;
});

// Wrap the output in a markdown code block so it's easily copyable
dv.paragraph("```markdown\n" + markdownOutput + "```");
```
