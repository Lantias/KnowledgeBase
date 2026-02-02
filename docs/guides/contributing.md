# Contributing to the Knowledge Base

Thank you for contributing to our Knowledge Base! This guide will help you create and maintain high-quality documentation.

## 📋 Content Guidelines

### Writing Style
- **Clear and Concise**: Use simple language and short sentences
- **Action-Oriented**: Start with verbs (Configure, Install, Create)
- **User-Focused**: Write for your intended audience
- **Examples**: Include practical examples and code snippets

### Document Structure
1. **Title**: Clear, descriptive title using Title Case
2. **Summary**: Brief overview of what the document covers
3. **Prerequisites**: What users need before starting
4. **Steps**: Numbered steps for procedures
5. **Examples**: Code snippets, screenshots, or sample outputs
6. **Related Links**: References to other relevant documents

## 📝 Templates

Use these templates for consistency:

- **Guide Template**: [guide-template.md](../templates/guide-template.md)
- **Tutorial Template**: [tutorial-template.md](../templates/tutorial-template.md)
- **Reference Template**: [reference-template.md](../templates/reference-template.md)
- **Troubleshooting Template**: [troubleshooting-template.md](../templates/troubleshooting-template.md)

## 📂 File Organization

### Naming Convention
- Use `kebab-case` for file names
- Be descriptive: `setting-up-development-environment.md`
- Avoid spaces, special characters, and capitals

### Folder Structure
- **guides/**: How-to guides for specific tasks
- **tutorials/**: Learning-oriented step-by-step lessons
- **reference/**: Technical specifications, API docs, configuration
- **troubleshooting/**: Problem-solving guides and FAQs

## 🖼️ Images and Assets

### Image Guidelines
- Store images in `assets/images/`
- Use descriptive file names: `user-dashboard-screenshot.png`
- Optimize file sizes (prefer PNG for screenshots, JPG for photos)
- Include alt text for accessibility

### Linking Images
```markdown
![Alt text](../assets/images/your-image.png)
```

## 🏷️ Document Metadata

Add metadata at the top of each document:

```markdown
---
title: Document Title
category: guides|tutorials|reference|troubleshooting
tags: [tag1, tag2, tag3]
difficulty: beginner|intermediate|advanced
last_updated: YYYY-MM-DD
---
```

## ✅ Review Checklist

Before submitting:

- [ ] Used appropriate template
- [ ] Followed naming conventions
- [ ] Included metadata tags
- [ ] Added examples and screenshots
- [ ] Checked spelling and grammar
- [ ] Tested all code snippets
- [ ] Updated related documents
- [ ] Added cross-references where applicable

## 🔄 Maintenance

### Regular Updates
- Review documents quarterly
- Update screenshots when UI changes
- Verify code examples still work
- Check external links

### Deprecation
- Mark outdated content clearly
- Provide migration paths
- Archive old documents instead of deleting

## 📞 Getting Help

- Create an issue for questions
- Tag relevant team members
- Use discussion threads for collaboration

Thank you for helping maintain our Knowledge Base! 🙏