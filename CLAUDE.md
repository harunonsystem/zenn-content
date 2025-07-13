# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Zenn content repository for managing technical articles and books. Zenn is a Japanese platform for developers to publish technical content. The repository uses `zenn-cli` for content management and preview.

## Architecture & Structure

- `articles/` - Contains individual technical articles in Markdown format with YAML frontmatter
- `books/` - Directory for book-length content (currently empty)
- Package manager: npm (uses package-lock.json, not pnpm despite references in old docs)
- Single dependency: `zenn-cli` for content management

## Content Format

Articles follow Zenn's frontmatter format:
```yaml
---
title: "Article Title"
emoji: "😘"
type: "tech" # tech: 技術記事 / idea: アイデア  
topics: ["flutter", "firebase"]
published: true
---
```

## Common Commands

**Important**: This project uses npm, not pnpm. Correct commands:

### Development
- `npx zenn preview` - Start local preview server to view content in browser
- `npx zenn new:article` - Create a new article
- `npx zenn new:book` - Create a new book
- `npm install` - Install dependencies

### Content Management
- Articles are written in Markdown with Zenn-specific extensions
- Images are hosted on Zenn's CDN or external services (e.g., `https://storage.googleapis.com/zenn-user-upload/`)
- Content supports code blocks, embeds, and special Zenn syntax
- Content is primarily in Japanese

## Development Notes

- No build/test process - content is deployed directly to Zenn platform
- Topics focus on Flutter, Firebase, WebStorm, automation tools, and mobile development
- Articles include practical tutorials with code examples and GIFs/images
- Preview changes locally before publishing using `npx zenn preview`