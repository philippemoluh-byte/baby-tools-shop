# Baby Tools Shop

This guide demonstrates how to dockerize a full-stack Django application and deploy it to a Linux-based virtual server. It covers the steps needed to set up a Django project, containerize it with a Dockerfile, and configure NGINX as a reverse proxy. Baby Tools Shop is an e-commerce sample project for baby tools. It is provided for educational purposes only and makes no claims regarding feature completeness, application security, user experience, or design.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Quickstart](#quickstart)
- [Project Structure](#project-structure)
  - [Directory Descriptions](#directory-descriptions)
  - [Apps Overview](#apps-overview)
    - [`products` app](#products-app)
    - [`users` app](#users-app)
    - [`babyshop` project module](#babyshop-project-module)
- [Usage](#usage)
  - [Use Docker to Start Your App](#use-docker-to-start-your-app)
  - [Configure Your Server](#configure-your-server)
  - [Build and run with Docker](#build-and-run-with-docker)
  - [Configure NGINX as a Reverse Proxy](#configure-nginx-as-a-reverse-proxy)
  - [Verify the Application in Your Browser](#verify-the-application-in-your-browser)

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
git clone https://github.com/philippemoluh-byte/baby-tools-shop.git
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
 .\my-venv\Scripts\Activate.ps1
        
 # On macOS/Linux, run:
 source my-venv/bin/activate
```

Install the project dependencies

```bash
pip install -r babyshop_app/requirements.txt
```

Configure the required application environment variables

```bash
cp example.env babyshop_app/.env.development
```

Edit `babyshop_app/.env.development`:

```env
DEBUG=True
ALLOWED_HOSTS=127.0.0.1,localhost
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
http://localhost:8000
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
    ├── .dockerignore                         # Files excluded from Docker build context
    ├── .env.example                          # Base environment template
    ├── .env.development.example              # Development environment template
    ├── .env.production.example               # Production environment template
    ├── Dockerfile                            # Docker image build instructions
    ├── entrypoint.sh                         # Container startup script
    ├── manage.py                             # Django management entrypoint
    ├── requirements.txt                      # Python dependencies
    ├── db.sqlite3                            # Local SQLite database
    ├── media/                                # Uploaded media files
    ├── babyshop/                             # Django project config package
    │   ├── __init__.py                       # Marks package
    │   ├── asgi.py                           # ASGI app entrypoint
    │   ├── settings.py                       # Global Django settings
    │   ├── urls.py                           # Root URL configuration
    │   └── wsgi.py                           # WSGI app entrypoint
    ├── products/                             # Products domain app
    │   ├── __init__.py                       # Marks package
    │   ├── admin.py                          # Admin registrations
    │   ├── apps.py                           # App config
    │   ├── models.py                         # Product/category models
    │   ├── tests.py                          # App tests
    │   ├── urls.py                           # Product URL routes
    │   ├── views.py                          # Product views
    │   └── migrations/                       # Database migrations
    ├── users/                                # User/authentication app
    │   ├── __init__.py                       # Marks package
    │   ├── admin.py                          # Admin registrations
    │   ├── apps.py                           # App config
    │   ├── forms.py                          # Login/register forms
    │   ├── models.py                         # User-related models
    │   ├── tests.py                          # App tests
    │   ├── urls.py                           # Auth URL routes
    │   ├── views.py                          # Auth views
    │   └── migrations/                       # Database migrations
    └── templates/                            # HTML templates
        ├── login.html                        # Login page template
        ├── product.html                      # Product detail template
        ├── products.html                     # Product list template
        ├── register.html                     # Registration template
        └── partoftemp/                       # Shared partial templates
            ├── _dashboard.html               # Base dashboard layout
            └── footer.html                   # Footer partial
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
| `my-venv/` | Local virtual environment (not required in source control). |

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

## Usage

> [!NOTE]
> You can also run Baby Tools Shop in a Docker container.

### Use Docker to Start Your App

> [!TIP]
> Ensure you have a `Dockerfile` and `.dockerignore` file in your project root directory.

Step 1 — Build an image

> [!IMPORTANT]
> Ensure your virtual environment is created and activated before running local project commands.

You can build the container image by running the following command:

```bash
  # Use -t to provide an image tag.
  # -> baby-tools-shop is the image name, 'local' is the tag
  docker build -t baby-tools-shop:local .
```

Step 2 — Run a container

To start a container based on the image, use the following command in your terminal:

```bash
docker run --rm -it -p 8000:8000 baby-tools-shop:local
```

Verify the application is running by visiting

```bash
http://localhost:8000
```

Create a superuser by running:

```bash
 docker exec -it <your_container_id> python manage.py createsuperuser
```

### Configure Your Server

To containerize the project on a virtual server, follow these steps:

Step 1 — Clone the Repository

```bash
 git clone https://github.com/<your_github_account_name>/<your_github_repository_name>
 cd <your_github_repository_name>
```

Step 2 — Set up the environment file

> [!TIP]
> The environment file should be stored next to the `manage.py` file.
> Other locations may work, but are not guaranteed; if needed, update the project configuration accordingly.

```bash
 cp example.env babyshop_app/.env.production
```

Open the `.env` file in the source directory and set the required environment variables:

```bash
 # `ALLOWED_HOSTS`: Provide a comma-separated list of allowed hosts.
 ALLOWED_HOSTS=<your-domain.com>,<www.your-domain.com>

 # `DEBUG`: Set to True for development or False for production. Defaults to True
 DEBUG=False
```

### Build and run with Docker
>
> [!NOTE]
> The commands below will:
>
> - Build the Python application image
> - Start the app container on internal port 8000

```bash
  # build the container image
  docker build -t baby-tools-shop:local .

  # Start the container based on the image
  docker run --rm -it -p 8000:8000 baby-tools-shop:local
```

### Configure NGINX as a Reverse Proxy

Step 1 — Create or edit the NGINX site configuration:

```bash
 sudo nano /etc/nginx/sites-available/baby-tool-shop
```

Step 2 — Add the following server block:

```nginx
server {
    listen <your_nginx_port>;
    server_name your-domain.com www.your-domain.com;

    location / {
        proxy_pass         http://127.0.0.1:8000;
        proxy_http_version 1.1;
        proxy_set_header   Host $host; # passes the original domain name to the backend
        proxy_set_header   X-Real-IP $remote_addr; # passes the client's real IP
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for; # passes the full IP chain 
        proxy_set_header   X-Forwarded-Proto $scheme; # tells the backend whether the original request was HTTP or HTTPS
    }
}

```

Step 3 — Enable the site and reload NGINX:

```bash
 sudo ln -s /etc/nginx/sites-available/baby-tool-shop /etc/nginx/sites-enabled/
 sudo nginx -t  # Test the configuration
 sudo systemctl reload nginx
```

Step 4 — Enable auto-start on server reboot

```bash
 sudo systemctl enable nginx
```

### Verify the Application in Your Browser

Verify the application is running by visiting:

```bash
http://<your_domain>
```

To create a superuser account, run:

```bash
 docker exec -it <your_container_id> python manage.py createsuperuser
```
