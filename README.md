<div align="center">

<h1>
  <img src="https://em-content.zobj.net/source/apple/391/pencil_270f-fe0f.png" width="40" alt="blog icon"/>
  Django Blog
</h1>

<p>
  <strong>A full-stack blogging platform built with Django 6 — featuring a public reader-facing site and a private author dashboard, all powered by SQLite and Bootstrap.</strong>
</p>

<p>
  <img src="https://img.shields.io/badge/Django-6.0.3-092E20?style=for-the-badge&logo=django&logoColor=white" alt="Django">
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Bootstrap-4.x-7952B3?style=for-the-badge&logo=bootstrap&logoColor=white" alt="Bootstrap">
  <img src="https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite">
  <img src="https://img.shields.io/badge/Pillow-12.2.0-FFD700?style=for-the-badge" alt="Pillow">
</p>

<p>
  <img src="https://img.shields.io/badge/status-development-orange?style=flat-square" alt="Status">
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="License">
</p>

</div>

---

## 📖 Table of Contents

- [✨ Features](#-features)
- [🏗️ Project Architecture](#️-project-architecture)
- [📂 Directory Structure](#-directory-structure)
- [🗃️ Data Models](#️-data-models)
- [🛣️ URL Routes](#️-url-routes)
- [⚙️ Tech Stack](#️-tech-stack)
- [🚀 Getting Started](#-getting-started)
- [🔑 Environment Variables](#-environment-variables)
- [📋 Dashboard Features](#-dashboard-features)
- [🌐 Public Site Features](#-public-site-features)
- [🤝 Contributing](#-contributing)

---

## ✨ Features

| Category | Feature |
|---|---|
| 📝 **Content** | Create, edit, and delete blog posts with rich text bodies |
| 🏷️ **Categories** | Organise posts under named categories; filter by category on the public site |
| ⭐ **Featured Posts** | Mark posts as "Featured" — they appear as hero cards on the home page |
| 🔍 **Search** | Full-text keyword search across titles, short descriptions, and body content |
| 💬 **Comments** | Authenticated users can post comments on any published blog |
| 📸 **Media Uploads** | Cover images stored under `media/uploads/YYYY/MM/DD/` |
| 🔐 **Auth** | Register · Login · Logout with Django's built-in auth system |
| 🛡️ **Dashboard** | Protected admin-style dashboard: manage posts, categories, and users |
| 🔗 **Social Links** | Add platform links (admin-managed) shown in the sidebar |
| 📄 **About Section** | Editable "About" blurb shown in the sidebar |
| 🐌 **Slug Generation** | Auto-generated URL-friendly slugs on post creation/update |
| 📦 **crispy-forms** | Bootstrap 4-styled forms throughout, zero extra CSS needed |

---

## 🏗️ Project Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                         Django Blog Platform                         │
│                                                                      │
│  ┌──────────────┐    ┌──────────────┐    ┌───────────────────────┐  │
│  │  blog_main   │    │    blogs     │    │      dashboards       │  │
│  │  (project)   │    │    (app)     │    │        (app)          │  │
│  │              │    │              │    │                       │  │
│  │ settings.py  │───▶│  Blog        │    │  CRUD for Posts       │  │
│  │ urls.py      │    │  Category    │    │  CRUD for Categories  │  │
│  │ views.py     │    │  Comment     │    │  CRUD for Users       │  │
│  │ forms.py     │    │              │    │  protected by         │  │
│  │              │    │  views.py    │    │  @login_required      │  │
│  └──────────────┘    │  admin.py    │    └───────────────────────┘  │
│                      │  context_   │                                 │
│  ┌──────────────┐    │  processors │    ┌───────────────────────┐  │
│  │ assignments  │    └──────────────┘    │       templates/       │  │
│  │    (app)     │                        │                       │  │
│  │              │                        │  base.html            │  │
│  │  About       │                        │  home.html            │  │
│  │  SocialLink  │                        │  blogs.html           │  │
│  │              │                        │  dashboard/           │  │
│  └──────────────┘                        │    └ 11 templates     │  │
│                                          └───────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

The project follows a **multi-app Django architecture**:

- **`blog_main`** — The project core: settings, root URLs, auth views, and registration form.
- **`blogs`** — The primary content app: models for `Blog`, `Category`, `Comment`; public views; and context processors that inject categories and social links site-wide.
- **`assignments`** — A supporting app housing the `About` section and `SocialLink` entries (displayed in the public sidebar).
- **`dashboards`** — A protected admin dashboard app providing full CRUD over posts, categories, and users.

---

## 📂 Directory Structure

```
blog/
│
├── blog_main/               # Django project core
│   ├── settings.py          # All project configuration
│   ├── urls.py              # Root URL dispatcher
│   ├── views.py             # home, register, login, logout
│   ├── forms.py             # RegistrationForm (UserCreationForm)
│   ├── wsgi.py / asgi.py    # WSGI & ASGI entrypoints
│   └── static/
│       └── css/
│           └── blog.css     # Custom CSS (Playfair Display font, layout helpers)
│
├── blogs/                   # Content app
│   ├── models.py            # Category · Blog · Comment
│   ├── views.py             # posts_by_category · blogs (detail) · search
│   ├── urls.py              # /category/<id>/ routes
│   ├── admin.py             # BlogAdmin with slug pre-population
│   └── context_processors.py  # Injects `categories` & `social_links` globally
│
├── assignments/             # Site metadata app
│   ├── models.py            # About · SocialLink
│   └── admin.py             # Registered for admin management
│
├── dashboards/              # Protected dashboard app
│   ├── models.py            # (no extra models — reuses blogs & auth)
│   ├── views.py             # Full CRUD views for posts/categories/users
│   ├── urls.py              # /dashboard/* routes
│   └── forms.py             # CategoryForm · BlogPostForm · AddUserForm · EditUserForm
│
├── templates/               # All HTML templates
│   ├── base.html            # Shared layout: navbar, categories nav, footer
│   ├── home.html            # Hero + featured posts + recent articles + sidebar
│   ├── blogs.html           # Single post detail + comments
│   ├── posts_by_category.html
│   ├── search.html
│   ├── login.html
│   ├── register.html
│   ├── 404.html
│   └── dashboard/           # 11 dashboard-specific templates
│       ├── dashboard.html   # Stats overview
│       ├── posts.html       # Post list
│       ├── add_post.html / edit_post.html
│       ├── categories.html
│       ├── add_category.html / edit_category.html
│       ├── users.html
│       ├── add_user.html / edit_user.html
│       └── sidebar.html
│
├── media/                   # User-uploaded images (git-ignored)
│   └── uploads/YYYY/MM/DD/
│
├── db.sqlite3               # SQLite database
├── manage.py                # Django management script
├── requirements.txt         # Python dependencies
└── .gitignore
```

---

## 🗃️ Data Models

### `blogs` app

```python
Category
  ├── category_name   CharField(max_length=50, unique=True)
  ├── created_at      DateTimeField(auto_now_add=True)
  └── updated_at      DateTimeField(auto_now=True)

Blog
  ├── title            CharField(max_length=100)
  ├── slug             SlugField(max_length=150, unique=True)  ← auto-generated
  ├── category         ForeignKey(Category)
  ├── author           ForeignKey(User)
  ├── featured_image   ImageField(upload_to="uploads/%Y/%m/%d")
  ├── short_description TextField(max_length=500)
  ├── blog_body        TextField(max_length=2000)
  ├── status           CharField  [Draft | Published]
  ├── is_featured      BooleanField  ← shown as hero on home page
  ├── created_at       DateTimeField
  └── updated_at       DateTimeField

Comment
  ├── user    ForeignKey(User)
  ├── blog    ForeignKey(Blog)
  └── comment TextField(max_length=250)
```

### `assignments` app

```python
About
  ├── about_heading      CharField(max_length=25)
  ├── about_description  TextField(max_length=255)
  ├── created_at         DateTimeField
  └── updated_at         DateTimeField

SocialLink
  ├── platform    CharField(max_length=25)  ← e.g. "Twitter", "GitHub"
  ├── link        URLField(max_length=100)
  ├── created_at  DateTimeField
  └── updated_at  DateTimeField
```

---

## 🛣️ URL Routes

| Method | URL | View | Name |
|---|---|---|---|
| GET | `/` | `blog_main.views.home` | `home` |
| GET | `/blogs/<slug>/` | `blogs.views.blogs` | `blogs` |
| GET/POST | `/blogs/<slug>/` | (comment POST) | `blogs` |
| GET | `/blog/search/?keyword=…` | `blogs.views.search` | `search` |
| GET | `/category/<id>/` | `blogs.views.posts_by_category` | `posts_by_category` |
| GET/POST | `/register/` | `blog_main.views.register` | `register` |
| GET/POST | `/login/` | `blog_main.views.login` | `login` |
| GET | `/logout/` | `blog_main.views.logout` | `logout` |
| GET | `/dashboard/` | `dashboards.views.dashboard` | `dashboard` |
| GET | `/dashboard/posts/` | `dashboards.views.posts` | `posts` |
| GET/POST | `/dashboard/posts/add` | `dashboards.views.add_post` | `add_post` |
| GET/POST | `/dashboard/posts/edit/<pk>` | `dashboards.views.edit_post` | `edit_post` |
| POST | `/dashboard/posts/delete/<pk>` | `dashboards.views.delete_post` | `delete_post` |
| GET | `/dashboard/categories/` | `dashboards.views.categories` | `categories` |
| GET/POST | `/dashboard/categories/add/` | `dashboards.views.add_category` | `add_category` |
| GET/POST | `/dashboard/categories/edit/<pk>/` | `dashboards.views.edit_category` | `edit_category` |
| POST | `/dashboard/categories/delete/<pk>/` | `dashboards.views.delete_category` | `delete_category` |
| GET | `/dashboard/users/` | `dashboards.views.users` | `users` |
| GET/POST | `/dashboard/users/add/` | `dashboards.views.add_user` | `add_user` |
| GET/POST | `/dashboard/users/edit/<pk>/` | `dashboards.views.edit_user` | `edit_user` |
| POST | `/dashboard/users/delete/<pk>/` | `dashboards.views.delete_user` | `delete_user` |

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| **Framework** | Django 6.0.3 |
| **Language** | Python 3.x |
| **Database** | SQLite 3 (via `django.db.backends.sqlite3`) |
| **ORM** | Django ORM |
| **Frontend** | Bootstrap 4, Playfair Display (Google Fonts), Font Awesome 4.7 |
| **Forms** | `django-crispy-forms` + `crispy-bootstrap4` |
| **Image Handling** | Pillow 12.2.0 |
| **Type Checking** | `django-stubs`, `django-stubs-ext` |
| **Linting/Formatting** | `djlint`, `cssbeautifier`, `jsbeautifier` |
| **ASGI/WSGI** | Django built-in |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- `pip`
- (Optional) `virtualenv` or any virtual-environment tool

### 1 — Clone & enter the project

```bash
git clone https://github.com/Anjas2005/django-blog.git
cd django-blog
```

### 2 — Create and activate a virtual environment

```bash
python -m venv env
source env/bin/activate        # Linux / macOS
# env\Scripts\activate.bat     # Windows
```

### 3 — Install dependencies

```bash
pip install -r requirements.txt
```

### 4 — Apply migrations

```bash
python manage.py migrate
```

### 5 — Create a superuser (for the dashboard)

```bash
python manage.py createsuperuser
```

### 6 — Run the development server

```bash
python manage.py runserver
```

Open **http://127.0.0.1:8000** in your browser. 🎉

> **Tip:** Visit `/admin/` to manage `About`, `SocialLink`, and blog content via Django's built-in admin, or use `/dashboard/` once logged in.

---

## 🔑 Environment Variables

A `.env_secret` file is present at the project root (git-ignored). At minimum you should set:

| Variable | Description |
|---|---|
| `SECRET_KEY` | Django secret key — **never expose in production** |
| `DEBUG` | Set to `False` in production |
| `ALLOWED_HOSTS` | Comma-separated list of allowed host/domain names |

> The current `settings.py` uses a hardcoded insecure key for development. Before deploying, load these from environment variables using `python-decouple` or `django-environ`.

---

## 📋 Dashboard Features

The `/dashboard/` section is **login-protected** (`@login_required`). It provides:

- **📊 Overview** — total post count and category count at a glance.
- **📝 Posts CRUD** — list all posts (draft & published), add, edit, or delete any post. Slugs are auto-generated as `slugify(title) + "-" + id`.
- **🗂️ Categories CRUD** — add, rename, or remove categories.
- **👥 Users CRUD** — add new users (with permissions: `is_staff`, `is_superuser`, groups), edit, or delete existing users.

---

## 🌐 Public Site Features

- **🏠 Home page** — Hero image drawn from the most-recently-updated featured post; secondary featured posts shown as cards; recent non-featured articles listed in the main column alongside an "About" sidebar.
- **🔍 Search** — `Q` object search across `title`, `short_description`, and `blog_body`.
- **🏷️ Category filter** — Click any category in the nav bar to see only that category's published posts.
- **📄 Blog detail** — Full post body, author, date, comment count, and a comment form for logged-in users.
- **🔗 Social links** — Listed in sidebar, managed from the admin panel.
- **🔐 Auth** — Register (email + username + password), Login, and Logout flows.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Commit your changes: `git commit -m "feat: add your feature"`
4. Push to the branch: `git push origin feat/your-feature`
5. Open a Pull Request

---

<div align="center">

Built with ❤️ by **Anjas Vaidya**

*Django Blog — simple, clean, and extensible.*

</div>
