# AGENTS.md - Agentic Coding Guidelines for This Repository

This is a **Hexo static blog** using Hexo 8.1.1 with the Butterfly theme. Agents should understand this is primarily a content-based project (Markdown posts + YAML configuration), not a traditional code project.

## Build / Development Commands

| Command | Description |
|---------|-------------|
| `npm run build` | Generate static site (`hexo generate`) |
| `npm run clean` | Clean generated files (`hexo clean`) |
| `npm run server` | Start local dev server (`hexo server`) |
| `npm run deploy` | Deploy to production (`hexo deploy`) |

**Running a single test**: No test framework is configured. This project does not have unit tests.

**Dependencies**: Run `npm install` after any package.json changes.

## Project Structure

```
blog/
├── source/_posts/      # Markdown blog posts
├── source/_data/       # Data files (links.yml)
├── source/_drafts/    # Draft posts
├── source/tags/       # Tag pages
├── source/categories/ # Category pages
├── source/img/        # Images
├── source/css/        # Custom CSS (user.css)
├── themes/            # Hexo themes
├── _config.yml        # Main Hexo config
├── _config.butterfly.yml  # Theme config
├── package.json       # npm dependencies
└── db.json           # Hexo database
```

## Code Style Guidelines

### Markdown Posts (source/_posts/)

- **Front-matter**: Required at top of each post
  ```yaml
  ---
  title: Post Title
  date: YYYY-MM-DD HH:mm:ss
  tags: [tag1, tag2]
  categories: [category]
  abbrlink: xxxxxx
  ---
  ```
- Use Chinese for content (blog is zh-CN)
- Enable `abbrlink` plugin for pretty URLs (crc32/hex format)
- Code blocks: specify language for syntax highlighting
- Images: place in `source/img/` or use external CDN

### YAML Configuration

- Use 2-space indentation
- No tabs
- Quote strings with special characters: `"string with: colon"`
- Use lowercase with underscores for keys: `post_asset_folder: false`

### CSS (source/css/)

- Custom styles go in `user.css`
- Follow existing Butterfly theme overrides

### Git Workflow

- Commit messages in English
- Work on feature branches, merge to dev branch
- Push changes to deploy (GitHub Actions handles deployment)

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Posts | Chinese descriptive | `Windows任务计划程序如何定时执行Python程序.md` |
| Tags | Chinese | `ESP32`, `C语言` |
| Categories | Chinese | `嵌入式`, `教程` |
| Images | Descriptive English | `avatar.png`, `blog-bg.jpg` |
| Config files | Lowercase with underscore | `_config.yml`, `_config.butterfly.yml` |

## Important Configuration Notes

- **URL format**: `posts/:abbrlink/` (no .html)
- **Theme**: butterfly (v5.5.4)
- **Language**: zh-CN
- **Comments**: Waline (server: https://waline.canheting.cn/)
- **Analytics**: Baidu Analytics enabled
- **CDN**: jsdelivr for third-party scripts

## Key Theme Features Used

- TOC (table of contents)
- Code highlighting (light theme)
- Word count / reading time
- Reward/donation QR codes
- Dark mode
- Pangu.js (Chinese spacing)
- Fancybox (image gallery)

## Adding New Posts

1. Create Markdown file in `source/_posts/`
2. Add required front-matter
3. Run `npm run server` to preview locally
4. Commit and push to deploy

## Dependencies

Do not add new npm packages without checking compatibility with Hexo 8.x and Butterfly theme. Key dependencies:
- hexo (8.1.1)
- hexo-theme-butterfly (5.5.4)
- hexo-generator-sitemap
- hexo-generator-feed
- hexo-wordcount

## Troubleshooting

- Build fails: Run `npm run clean` first
- Theme issues: Check `_config.butterfly.yml`
- Images not loading: Verify path in front-matter or CDN settings
- Comments not working: Check Waline server URL configuration
