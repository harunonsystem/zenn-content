# AGENTS.md - Zenn Content Repository Guide

## Build/Lint/Test Commands
- `npx zenn preview` - Start local preview server to view content
- `npx zenn new:article` - Create new article
- `npx zenn new:book` - Create new book
- `npm run test` - No tests configured (content repository)
- `npx textlint articles/*.md` - Run linting on all articles
- `npx textlint articles/[filename].md` - Lint single article

## Content Style Guidelines
- Articles use YAML frontmatter: title, emoji, type (tech/idea), topics array, published boolean
- Content primarily in Japanese with technical writing standards (textlint-rule-preset-ja-technical-writing)
- Use Zenn-specific syntax for code blocks and embeds
- Images hosted on Zenn CDN (`https://storage.googleapis.com/zenn-user-upload/`)
- Code examples labeled with file paths (e.g., `dart: lib/main.dart`)
- Use descriptive section headings starting with `#`
- Include practical examples and GIFs/images for complex concepts

## File Naming & Structure
- Articles: descriptive kebab-case names (e.g., `flutter_firebase_auth_error_handling.md`)
- Topics focus: Flutter, Firebase, WebStorm, automation tools, mobile development
- Content validation: Use textlint for Japanese writing standards