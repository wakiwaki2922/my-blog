---
title: "Hướng dẫn tạo Blog với Hugo và Deploy lên GitHub Pages"
date: 2024-12-19T10:00:00+07:00
draft: false
description: "Hướng dẫn chi tiết từng bước để tạo blog với Hugo và deploy lên GitHub Pages trên Windows"
tags: ["Hugo", "GitHub Pages", "Blog", "Windows", "Tutorial"]
categories: ["Hướng dẫn"]
author: "G.D"
---

# Hướng dẫn tạo Blog với Hugo và Deploy lên GitHub Pages

Hướng dẫn chi tiết từng bước để tạo một blog với Hugo và deploy lên GitHub Pages hoàn toàn miễn phí. Các bước được thực hiện trên Windows.

---

## 1. Cài đặt Hugo

### Cách 1: Sử dụng Chocolatey (Khuyến nghị)

Mở PowerShell với quyền Administrator và chạy:

```powershell
# Cài đặt Chocolatey nếu chưa có
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Cài đặt Hugo
choco install hugo-extended -y
```

### Cách 2: Download trực tiếp từ GitHub

1. Truy cập [Hugo Releases](https://github.com/gohugoio/hugo/releases)
2. Download file `hugo_extended_x.x.x_windows-amd64.zip`
3. Giải nén và copy file `hugo.exe` vào thư mục `C:\Hugo\bin`
4. Thêm `C:\Hugo\bin` vào PATH environment variable

### Kiểm tra cài đặt

Mở Command Prompt hoặc PowerShell và chạy:

```cmd
hugo version
```

---

## 2. Tạo Hugo Site mới

Mở Command Prompt hoặc PowerShell, navigate đến thư mục để tạo blog:

```cmd
# Tạo site mới
hugo new site my-blog

# Di chuyển vào thư mục
cd my-blog

# Khởi tạo Git repository
git init
```

Cấu trúc thư mục sẽ như sau:

```
my-blog/
├── archetypes/
├── assets/
├── content/
├── data/
├── layouts/
├── static/
├── themes/
└── hugo.toml
```

---

## 3. Chọn và cài đặt Theme

### Tìm Theme

Truy cập [Hugo Themes](https://themes.gohugo.io/) để chọn theme phù hợp.

### Cài đặt Theme (Ví dụ: PaperMod)

```cmd
# Thêm theme như một Git submodule
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

### Cấu hình Theme cơ bản

Mở file `hugo.toml` và cấu hình:

```toml
baseURL = 'https://your-username.github.io'
languageCode = 'vi-VN'
title = 'My Blog'
theme = 'PaperMod'

[params]
    author = "Your Name"
    description = "My personal blog"
    
    [params.homeInfoParams]
        Title = "Chào mừng đến với blog của tôi"
        Content = "Nơi chia sẻ kiến thức và kinh nghiệm"

[[menu.main]]
    name = "Home"
    url = "/"
    weight = 10

[[menu.main]]
    name = "Posts"
    url = "/posts/"
    weight = 20

[[menu.main]]
    name = "About"
    url = "/about/"
    weight = 30
```

---

## 4. Tạo các trang cơ bản

Khi mới khởi tạo Hugo site, bạn sẽ không có các trang cơ bản như `_index`, `about`, `archives`. Đây là cách tạo chúng:

### Tạo trang chủ (_index.md)

```cmd
hugo new _index.md
```

Hoặc tạo thủ công file `content/_index.md`:

```markdown
---
title: "Trang chủ"
date: 2025-01-16
draft: false
---

# Chào mừng đến với blog của tôi!

Đây là trang chủ của blog Hugo. Nơi tôi chia sẻ những kiến thức và kinh nghiệm của mình.
```

### Tạo trang Giới thiệu (About)

```cmd
hugo new about.md
```

Hoặc tạo file `content/about.md`:

```markdown
---
title: "Giới thiệu"
date: 2025-01-16
draft: false
ShowToc: false
ShowReadingTime: false
---

# Xin chào! Tôi là [Tên của bạn]

## Về tôi
Giới thiệu bản thân, sở thích, kinh nghiệm...

## Liên hệ
- Email: your-email@example.com
- GitHub: [github.com/username](https://github.com/username)
```

### Tạo trang Lưu trữ (Archives)

```cmd
hugo new archives.md
```

Hoặc tạo file `content/archives.md`:

```markdown
---
title: "Lưu trữ"
layout: "archives"
url: "/archives/"
summary: archives
---
```

### Tạo trang Contact (Liên hệ) - Tùy chọn

```cmd
hugo new contact.md
```

Hoặc tạo file `content/contact.md`:

```markdown
---
title: "Liên hệ"
date: 2025-01-16
draft: false
---

# Liên hệ với tôi

## Thông tin liên hệ
- **Email**: your-email@example.com
- **GitHub**: [github.com/username](https://github.com/username)
- **LinkedIn**: [linkedin.com/in/username](https://linkedin.com/in/username)

## Gửi tin nhắn
Bạn có thể gửi email trực tiếp hoặc liên hệ qua các kênh mạng xã hội trên.
```

### Tạo trang Search (Tìm kiếm) - Cho themes hỗ trợ

```cmd
hugo new search.md
```

Hoặc tạo file `content/search.md`:

```markdown
---
title: "Tìm kiếm"
layout: "search"
---
```

### Cấu hình Menu cho các trang mới

Cập nhật file `hugo.toml` để thêm menu:

```toml
[[menu.main]]
    name = "Trang chủ"
    url = "/"
    weight = 1

[[menu.main]]
    name = "Bài viết"
    url = "/posts/"
    weight = 2

[[menu.main]]
    name = "Giới thiệu"
    url = "/about/"
    weight = 3

[[menu.main]]
    name = "Lưu trữ"
    url = "/archives/"
    weight = 4

[[menu.main]]
    name = "Liên hệ"
    url = "/contact/"
    weight = 5

[[menu.main]]
    name = "Tìm kiếm"
    url = "/search/"
    weight = 6
```

---

## 5. Tạo bài viết đầu tiên

```cmd
# Tạo bài viết mới
hugo new posts/my-first-post.md
```

Chỉnh sửa file `content/posts/my-first-post.md`:

```markdown
---
title: "Bài viết đầu tiên của tôi"
date: 2024-12-19T10:00:00+07:00
draft: false
description: "Đây là bài viết đầu tiên trên blog Hugo của tôi"
tags: ["Hugo", "Blog"]
---

# Chào mừng đến với blog của tôi!

Đây là bài viết đầu tiên được viết bằng Hugo. Hugo là một static site generator rất nhanh và mạnh mẽ.

## Tại sao chọn Hugo?

- **Nhanh**: Build site trong vài giây
- **Linh hoạt**: Hỗ trợ nhiều theme và customization
- **Miễn phí**: Open source và deploy miễn phí
```

### Preview Local

```cmd
hugo server -D
```

Truy cập `http://localhost:1313` để xem blog.

---

## 6. Cấu hình Theme và các tính năng

Hugo themes thường có nhiều tính năng có thể bật/tắt. Dưới đây là hướng dẫn chi tiết cho theme PaperMod:

### Các tính năng cơ bản của PaperMod Theme

#### 1. Cấu hình giao diện

```toml
[params]
  # Theme mode
  defaultTheme = "auto" # "dark", "light", hoặc "auto"
  
  # Hiển thị thông tin
  ShowReadingTime = true      # Hiển thị thời gian đọc
  ShowCodeCopyButtons = true  # Nút copy code
  ShowShareButtons = true     # Nút chia sẻ
  ShowPostNavLinks = true     # Điều hướng bài viết
  ShowBreadCrumbs = true      # Breadcrumb navigation
  ShowToc = true              # Table of Contents
  TocOpen = false             # TOC mở mặc định
  ShowWordCount = true        # Hiển thị số từ
  ShowRssButtonInSectionTermList = true
  UseHugoToc = true
  disableSpecial1stPost = false
  disableScrollToTop = false
  comments = false
  hidemeta = false
  hideSummary = false
  showtoc = false
  tocopen = false
```

#### 2. Cấu hình Home Page

**Mode 1: Regular Mode (Mặc định)**
```toml
# Không cần config thêm gì
```

**Mode 2: Home-Info Mode**
```toml
[params]
  [params.homeInfoParams]
    Title = "Xin chào 👋"
    Content = "Chào mừng đến với blog của tôi. Nơi tôi chia sẻ kiến thức về lập trình và công nghệ."
```

**Mode 3: Profile Mode**
```toml
[params]
  profileMode = true
  
  [params.profileMode]
    enabled = true
    title = "Tên của bạn"
    subtitle = "Software Developer"
    imageUrl = "/images/profile.jpg"
    imageWidth = 120
    imageHeight = 120
    imageTitle = "Profile"
    buttons = [
      {
        name = "Posts",
        url = "/posts"
      },
      {
        name = "Tags",
        url = "/tags"
      }
    ]
```

#### 3. Cấu hình Social Icons

```toml
[params]
  [[params.socialIcons]]
    name = "github"
    url = "https://github.com/username"
  
  [[params.socialIcons]]
    name = "linkedin"
    url = "https://linkedin.com/in/username"
  
  [[params.socialIcons]]
    name = "email"
    url = "mailto:your-email@example.com"
  
  [[params.socialIcons]]
    name = "twitter"
    url = "https://twitter.com/username"
  
  [[params.socialIcons]]
    name = "facebook"
    url = "https://facebook.com/username"
  
  [[params.socialIcons]]
    name = "instagram"
    url = "https://instagram.com/username"
```

#### 4. Cấu hình Search (Tìm kiếm)

```toml
# Bật search
[params]
  [params.fuseOpts]
    isCaseSensitive = false
    shouldSort = true
    location = 0
    distance = 1000
    threshold = 0.4
    minMatchCharLength = 0
    keys = ["title", "permalink", "summary", "content"]

# Tạo search index
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

#### 5. Cấu hình Comments

**Disqus**
```toml
[params]
  [params.comments]
    disqus = "your-disqus-shortname"
```

**Utterances (GitHub-based)**
```toml
[params]
  [params.comments]
    utterances = true
    utterancesRepo = "username/repo-name"
    utterancesTheme = "github-light"
    utterancesIssue = "pathname"
```

#### 6. Cấu hình Analytics

**Google Analytics**
```toml
googleAnalytics = "G-XXXXXXXXXX"

[params]
  [params.analytics]
    google = "G-XXXXXXXXXX"
```

#### 7. Cấu hình SEO

```toml
[params]
  description = "Mô tả blog của bạn"
  keywords = ["Hugo", "Blog", "Programming"]
  author = "Tên của bạn"
  images = ["/images/cover.jpg"]
  
  [params.assets]
    favicon = "/favicon.ico"
    favicon16x16 = "/favicon-16x16.png"
    favicon32x32 = "/favicon-32x32.png"
    apple_touch_icon = "/apple-touch-icon.png"
    safari_pinned_tab = "/safari-pinned-tab.svg"
```

#### 8. Cấu hình Code Highlighting

```toml
[markup]
  [markup.highlight]
    style = "github"           # Theme: github, dracula, vs, xcode...
    lineNos = true             # Hiển thị số dòng
    lineNumbersInTable = false
    codeFences = true          # Hỗ trợ ```code```
    guessSyntax = true         # Tự động detect ngôn ngữ
    tabWidth = 2
```

#### 9. Cấu hình Multi-language

```toml
[languages]
  [languages.en]
    languageName = "English"
    weight = 1
    title = "My Blog"
    [languages.en.params]
      description = "My personal blog"
  
  [languages.vi]
    languageName = "Tiếng Việt"
    weight = 2
    title = "Blog của tôi"
    [languages.vi.params]
      description = "Blog cá nhân của tôi"
```

#### 10. Cấu hình Cover Images

Trong frontmatter của bài viết:
```markdown
---
title: "Tiêu đề bài viết"
cover:
    image: "/images/cover.jpg"
    alt: "Mô tả ảnh"
    caption: "Caption cho ảnh"
    relative: false # true nếu ảnh trong thư mục bài viết
---
```

### Các themes phổ biến khác và tính năng

#### Hugo Theme Comparison

| Theme | Features | Best For |
|-------|----------|----------|
| **PaperMod** | Fast, SEO-friendly, Multiple modes | Personal blogs, portfolios |
| **Ananke** | Simple, clean, built-in | Beginners, minimal blogs |
| **Academic** | CV/Resume, publications | Academics, researchers |
| **Docsy** | Documentation, multi-language | Technical documentation |
| **Terminal** | Retro terminal look | Developer blogs |
| **Hermit** | Minimal, mobile-first | Simple personal blogs |

#### Universal Theme Features (Có ở hầu hết themes)

1. **Dark/Light Mode Toggle**
2. **Responsive Design**
3. **Social Media Integration**
4. **SEO Optimization**
5. **Syntax Highlighting**
6. **Table of Contents**
7. **Search Functionality**
8. **Comments System**
9. **Analytics Integration**
10. **Custom CSS/JS Support**

---

## 7. Cấu hình GitHub Repository

### Tạo Repository trên GitHub

1. Đăng nhập GitHub
2. Click "New repository"
3. Đặt tên: `your-username.github.io` (thay your-username bằng username GitHub)
4. Check "Public"
5. Click "Create repository"

### Kết nối Local với GitHub

```cmd
# Thêm remote origin
git remote add origin https://github.com/your-username/your-username.github.io.git

# Add và commit files
git add .
git commit -m "Initial commit: Hugo site setup"

# Push lên GitHub
git branch -M main
git push -u origin main
```

---

## 8. Deploy lên GitHub Pages

### Cách 1: Manual Deploy

```cmd
# Build site
hugo

# Copy public folder content to root (cho GitHub Pages)
# Hoặc sử dụng branch gh-pages
```

### Cách 2: Sử dụng GitHub Pages Settings

1. Vào repository trên GitHub
2. Click "Settings" tab
3. Scroll xuống "Pages" section
4. Chọn source: "Deploy from a branch"
5. Chọn branch: `main` và folder: `/ (root)`

---

## 9. Tự động hóa với GitHub Actions

Tạo file `.github/workflows/hugo.yml`:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.120.4
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
      
      - name: Install Dart Sass
        run: sudo snap install dart-sass
        
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
          
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
        
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
        
      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"          
            
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
 ```

### Commit và Push

```cmd
git add .
git commit -m "Add GitHub Actions workflow for Hugo deployment"
git push
```

---

## Kết quả

Sau khi setup thành công, blog sẽ có sẵn tại `https://your-username.github.io`

---

## Lưu ý quan trọng

1. **Thời gian deploy**: GitHub Pages có thể mất 5-10 phút để reflect changes
2. **Custom domain**: Có thể cấu hình custom domain trong Settings > Pages
3. **HTTPS**: GitHub Pages tự động cung cấp HTTPS
4. **Cập nhật content**: Mỗi khi push code mới, blog sẽ tự động rebuild
5. **Theme customization**: Nên tạo custom CSS trong `assets/css/` thay vì sửa trực tiếp theme
6. **Backup**: Luôn backup cấu hình và content của bạn

---

## Kết luận

Sau khi hoàn thành các bước trên, blog Hugo đã được tạo và deploy thành công lên GitHub Pages! Từ đây, chỉ cần viết bài trong thư mục `content/posts/` và push lên GitHub, blog sẽ tự động cập nhật.

**Các bước tiếp theo có thể thực hiện:**
- Customize theme theo nhu cầu
- Thêm Google Analytics
- Cấu hình SEO
- Thêm comment system
- Tạo custom layouts
- Thêm shortcodes
- Tối ưu performance

**Mẹo hữu ích:**
- Sử dụng `hugo server -D --navigateToChanged` để tự động reload và navigate
- Tạo script để tự động tạo bài viết mới
- Sử dụng Hugo modules thay vì git submodules cho themes
- Backup regular với git

🎉 **Blog Hugo của bạn đã sẵn sàng hoạt động!** 