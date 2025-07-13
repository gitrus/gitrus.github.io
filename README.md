# [gitrus.github.io](https://gitrus.github.io/)

Gitrus personal blog/website.
Created to share thoughts and essential, strictly opinionated resources from mentoring Python developers on the Middle Python Developer course at Praktikum.

## Dev guide
Built with [Zola](https://www.getzola.org/) static site generator.

```bash
# Build the site
zola build

# Start development server
zola serve --port 1111
```

## Theme Updates (Apollo git submodule)
```bash
# Update Apollo theme to latest version
git submodule update --remote themes/apollo
# Commit the theme update
git add themes/apollo
git commit -m "update Apollo theme to latest version"
```
