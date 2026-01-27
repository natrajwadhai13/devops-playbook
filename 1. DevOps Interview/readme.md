---
title: "• Readme file"
parent: "1. DevOps"
nav_order: 4
has_children: true
---

Perfect idea 👍
This is exactly the kind of **“future-me will thank present-me”** document.
Below is a **ready-to-copy `remedy.md` file** written specially for your setup:

- GitHub Pages
- **Jekyll + Just the Docs theme**
- Mix of **HTML + Markdown**
- Parent / child pages
- Images
- Internal links
- Same-page heading navigation
- Back to home links

You can paste this **as-is** into `docs/remedy.md`.

---

```md
---
title: 🛠 Website Remedy / Cheatsheet
nav_order: 99
---

# 🛠 GitHub Pages + Jekyll (Just the Docs) – Remedy Notes

This file is my **personal reference guide** for maintaining and extending my
GitHub Pages website built using:

- Jekyll
- Just the Docs theme
- Markdown (`.md`)
- HTML (`.html`)
- Parent–child navigation
- Internal links & anchors

Whenever I forget something, **check here first** instead of Googling.

---

## 📁 Folder Structure (Best Practice)
```

docs/
├── index.md
├── travel-plans/
│ ├── goa-planning.md
│ └── singapore-planning.md
├── remedy.md
└── assets/
└── images/

````

✔ Use **kebab-case**
❌ Avoid spaces in file/folder names

---

## 🧱 Page Types Used

### 1️⃣ Markdown pages (`.md`)
- Preferred for content
- Automatically converted to HTML by Jekyll
- Supports front matter (`---`)

### 2️⃣ HTML pages (`.html`)
- Used for **home page**
- Used when custom layout / JS / CSS is needed
- Can still link to `.md` pages

---

## 🧭 Parent – Child Pages (Navigation)

### Parent page example
```md
---
title: Travel Plans
has_children: true
---
````

### Child page example

```md
---
title: Goa Planning
parent: Travel Plans
nav_order: 1
---
```

✔ Appears under parent in sidebar
✔ Clean navigation

---

## 🔗 Internal Links (Between Pages)

### Link to another MD page

```md
[Goa Plan](../travel-plans/goa-planning/)
```

### Link from HTML to MD page

```html
<a href="docs/travel-plans/goa-planning/">Goa Plan</a>
```

✔ Always end with `/`
❌ Don’t use `.html` for MD pages

---

## 🔙 Back to Home Link (Inside MD Page)

```html
<a href="../index.html#portfolio" class="back-home"> ← Back to Home </a>
```

✔ Works from subfolders
✔ Can jump to section using `#id`

---

## 🧷 Same Page Navigation (Anchor Links)

### Heading

```md
## CI/CD Pipelines
```

### Link to that heading

```md
[Go to CI/CD](#ci-cd-pipelines)
```

🔹 Rules:

- All lowercase
- Spaces → hyphens
- Special characters removed automatically

---

## 🖼 Images in Markdown

### Image stored in assets folder

```md
![Goa Beach](../assets/images/goa-beach.jpg)
```

✔ Relative path matters
✔ Use `assets/images/` for cleanliness

---

## 🎨 Inline CSS in MD Page (Only for that page)

```html
<style>
  .main-content-wrap {
    width: 80vw !important;
  }
</style>
```

✔ Useful for wide pages
❌ Don’t overuse

---

## 📐 Page Width Using body_class

```md
---
title: Example
body_class: wide-page
---
```

Then in CSS:

```css
.wide-page .main-content-wrap {
  max-width: 90vw;
}
```

✔ Cleaner than inline CSS

---

## 🔠 Bold Headings / Text

### Markdown

```md
**Important**
```

### HTML

```html
<h1><strong>🚀 DevOps Playbook by Natraj</strong></h1>
```

---

## 🧩 Mixing HTML + Markdown

✔ Allowed in Jekyll
✔ HTML blocks render first
✔ Useful for buttons, layouts, icons

Example:

```md
<div class="hero">
  <h1>DevOps Playbook</h1>
</div>

## Notes Section
```

---

## 🚫 Common Mistakes to Avoid

❌ File names with spaces
❌ Linking `.md` as `.html`
❌ Absolute paths (`/docs/...`)
❌ Forgetting front matter
❌ Overwriting theme CSS unnecessarily

---

## ✅ Golden Rules

- Use `.md` wherever possible
- Keep URLs clean
- Use parent–child structure
- Store images centrally
- Add anchors for long pages
- Maintain this file 🙂

---

## 🧠 Final Reminder

> If something breaks:

1. Check file name
2. Check folder path
3. Check front matter
4. Check trailing `/` in links

---

_Last updated: 2026_

```

---

If you want next:
- ✅ **travel-plans index page**
- ✅ **Goa planning template**
- ✅ **Singapore planning template**
- ✅ **Reusable Back-Home button CSS**
- ✅ **Hidden pages (no sidebar)**

Just tell me 👍
```
