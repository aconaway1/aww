# Installing a Hugo Theme

This guide shows how to install and switch Hugo themes for this site using git submodules.

## Steps to Install a Theme

1. **Find the theme repository**
   - Check https://themes.gohugo.io/ or GitHub for the exact repo URL
   - Example: `https://github.com/owner/hugo-theme-name`

2. **Add it as a git submodule**
   ```bash
   git submodule add https://github.com/owner/hugo-theme-name.git themes/name
   ```

3. **Update `config.toml`**
   - Change the `theme` setting to match your new theme folder name
   - Example: `theme = "name"`

4. **Check the theme's requirements**
   - Read the theme's `README.md` for required Hugo version
   - Note any required config settings or front matter
   - Compare minimum Hugo version against Cloudflare Pages build (currently Hugo v0.147.7)

5. **Test locally**
   ```bash
   hugo --gc --minify
   ```
   - Fix any build errors or shortcode syntax issues before proceeding

6. **Remove old theme if switching** (optional)
   - List current themes: `ls themes/`
   - Remove the old one: `git rm themes/old-theme`
   - This updates `.gitmodules` automatically

7. **Commit and push**
   ```bash
   git add .gitmodules config.toml themes/new-theme
   git commit -m "theme: add new-theme"
   git push
   ```

8. **Cloudflare Pages will rebuild automatically**
   - Check the Pages dashboard → Deployments for build status
   - Verify the new theme is live at your site URL

## Notes

- Always remove local template overrides in `layouts/` if you want the theme to be fully active
- If the theme requires a Hugo version newer than v0.147.7, set the `HUGO_VERSION` environment variable in Pages build settings
- Page bundles (folders with `index.md` + resources) work with all themes
- Use `hugo new posts/post-slug/index.md` to create new posts with consistent dates

## Current Theme

Theme: **PaperMod**
- Repo: https://github.com/adityatelange/hugo-PaperMod.git
- Status: Active and deployed
