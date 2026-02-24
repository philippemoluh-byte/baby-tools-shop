# E-Commerce Project For Baby Tools

This guide demonstrates how to dockerize a full stack Django 6 application and deploy it to a Linux-based virtual server. It covers the steps needed to set up a Django project, containerize it using Dockerfile, and configure NGINX as a reverse proxy. Baby Tools shop ist an E-Commerce Project For Baby Tools and it is used as a demonstration project developed for educational purposes only and makes no claims regarding feature completeness, application security, user experience, or design

## Prerequisites

Before starting, make sure your Linux server and local machine have the following installed:

- Python Interpreter
- OCI-Compliant Container Engine (Docker v20+)
- NGINX (only on the virtual server)
- Git (to clone your project)
- Network connectivity from GitHub to your virtual server and local machine

## Quickstart

To quickly get started with the application, follow these steps:

Clone the Git repository

```bash
git clone https://github.com/philippemoluh-byte/baby-tools-shop.git.
```

Navigate to the repository

```bash
  cd baby-tools-shop
```

Create a virtual environment

```bash
 python -m venv my-venv
```

Activate the virtual environment

```bash
 # On Windows, run:
 my-venv\Scripts\activate
        
 # On macOS/Linux, run:
 source my-venv/bin/activate
```

Install the project dependencies

```bash
 pip install -r requirements.txt
```

Configure required application environment variables

```bash
 cp example.env .env
```

Navigate to the source directory

```bash
cd babyshop_app
```

Prepare the database (create and apply migrations)

```bash
  python manage.py makemigrations
  python manage.py migrate
```

Start the application

```bash
 python manage.py runserver
```

Verify the application is running by visiting

```bash
 localhost:8000
```

(Optional) Create a superuser by running:

```bash
python manage.py createsuperuser
```

## Project Structure

```text
baby-tools-shop/                               # Repository root
├── .gitignore                                 # Git ignore rules
├── LICENSE                                    # Project license
├── README.md                                  # Project documentation
├── example.env                                # Example environment variables
├── project_images/                            # Screenshots used in README
├── my-venv/                                   # Local virtual environment
└── babyshop_app/                              # Main Django application folder
  ├── .dockerignore                          # Files excluded from Docker build context
  ├── .env.example                           # Base environment template
  ├── .env.development.example               # Development environment template
  ├── .env.production.example                # Production environment template
  ├── Dockerfile                             # Docker image build instructions
  ├── entrypoint.sh                          # Container startup script
  ├── manage.py                              # Django management entrypoint
  ├── requirements.txt                       # Python dependencies
  ├── db.sqlite3                             # Local SQLite database
  ├── media/                                 # Uploaded media files
  ├── babyshop/                              # Django project config package
  │   ├── __init__.py                        # Marks package
  │   ├── asgi.py                            # ASGI app entrypoint
  │   ├── settings.py                        # Global Django settings
  │   ├── urls.py                            # Root URL configuration
  │   └── wsgi.py                            # WSGI app entrypoint
  ├── products/                              # Products domain app
  │   ├── __init__.py                        # Marks package
  │   ├── admin.py                           # Admin registrations
  │   ├── apps.py                            # App config
  │   ├── models.py                          # Product/category models
  │   ├── tests.py                           # App tests
  │   ├── urls.py                            # Product URL routes
  │   ├── views.py                           # Product views
  │   └── migrations/                        # Database migrations
  ├── users/                                 # User/authentication app
  │   ├── __init__.py                        # Marks package
  │   ├── admin.py                           # Admin registrations
  │   ├── apps.py                            # App config
  │   ├── forms.py                           # Login/register forms
  │   ├── models.py                          # User-related models
  │   ├── tests.py                           # App tests
  │   ├── urls.py                            # Auth URL routes
  │   ├── views.py                           # Auth views
  │   └── migrations/                        # Database migrations
  └── templates/                             # HTML templates
    ├── login.html                         # Login page template
    ├── product.html                       # Product detail template
    ├── products.html                      # Product list template
    ├── register.html                      # Registration template
    └── partoftemp/                        # Shared partial templates
      ├── _dashboard.html                # Base dashboard layout
      └── footer.html                    # Footer partial
```

### Directory Descriptions

| Directory | Description |
| --- | --- |
| `babyshop_app/` | Main Django source folder. |
| `babyshop_app/babyshop/` | Django project configuration (`settings.py`, `urls.py`, ASGI/WSGI). |
| `babyshop_app/products/` | Product and category app (models, views, routes, migrations). |
| `babyshop_app/users/` | User/auth app (forms, views, routes). |
| `babyshop_app/templates/` | Shared templates for auth and product pages. |
| `project_images/` | README screenshots. |
| `my-env/` | Local virtual environment (not required in source control). |

### Apps Overview

The project is split into focused Django modules with clear responsibilities.

#### `products` app

- **Purpose**: Manages catalog data and product pages.
- **Key files**:
  - `models.py`: Defines `Category` and `Product` models.
  - `views.py`: Handles product listing and product detail views.
  - `urls.py`: Maps product-related routes.
  - `admin.py`: Registers catalog models in Django admin.
  - `migrations/`: Tracks database schema changes for catalog entities.

#### `users` app

- **Purpose**: Manages authentication flows.
- **Key files**:
  - `forms.py`: Defines register and login forms.
  - `views.py`: Handles register, login, and logout actions.
  - `urls.py`: Maps authentication routes.
  - `admin.py`: Admin registration for user-related models (if configured).
  - `models.py`: Placeholder for app-specific models.

#### `babyshop` project module

- **Purpose**: Central Django project configuration and composition.
- **Key files**:
  - `settings.py`: Installed apps, middleware, templates, DB, static/media config.
  - `urls.py`: Root URL dispatcher that includes app routes.
  - `asgi.py` / `wsgi.py`: Deployment entry points.

Request handling flow:

1. Requests enter through `babyshop/urls.py`.
2. Routing delegates to `products/urls.py` or `users/urls.py`.
3. App `views.py` processes logic and reads/writes model data as needed.
4. Templates in `babyshop_app/templates/` render the final HTML response.

### Hints

This section will cover some hot tips when trying to interacting with this repository:

- Settings & Configuration for Django can be found in `babyshop_app/babyshop/settings.py`
- Routing: Routing information, such as available routes can be found from any `urls.py` file in `babyshop_app` and corresponding subdirectories

### Photos

#### Home Page with login

![Home Page with login](https://github.com/MET-DEV/Django-E-Commerce/blob/master/project_images/capture_20220323080815407.jpg)

#### Home Page with filter

![Home Page with filter](https://github.com/MET-DEV/Django-E-Commerce/blob/master/project_images/capture_20220323080840305.jpg)

#### Product Detail Page

![Product Detail Page](https://github.com/MET-DEV/Django-E-Commerce/blob/master/project_images/capture_20220323080934541.jpg)

#### Home Page with no login

![Home Page with no login](https://github.com/MET-DEV/Django-E-Commerce/blob/master/project_images/capture_20220323080953570.jpg)

#### Register Page

![Register Page](https://github.com/MET-DEV/Django-E-Commerce/blob/master/project_images/capture_20220323081016022.jpg)

#### Login Page

![Login Page](https://github.com/MET-DEV/Django-E-Commerce/blob/master/project_images/capture_20220323081044867.jpg)
