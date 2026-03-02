# Baby Tools Shop

This guide demonstrates how to dockerize a full-stack Django application and deploy it to a Linux-based virtual server. It covers the steps needed to set up a Django project, containerize it with a Dockerfile, and configure NGINX as a reverse proxy. Baby Tools Shop is an e-commerce sample project for baby tools. It is provided for educational purposes only and makes no claims regarding feature completeness, application security, user experience, or design.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Quickstart](#quickstart)
  - [Start Your App Manually](#start-your-app-manually)
  - [Use Docker to Start Your App](#use-docker-to-start-your-app)
- [Project Structure](#project-structure)
- [Usage](#usage)
  - [Configure Your Server](#configure-your-server)

## Prerequisites

Before starting, make sure you have the following installed:

- Docker v20+
- Git (to clone your project)

## Quickstart

### Start Your App Manually

To quickly get started with the application, follow these steps:

Clone the Git repository

```bash
git clone git@github:philippemoluh-byte/baby-tools-shop.git
```

Navigate to the repository

```bash
cd baby-tools-shop
```

Configure the required application environment variables

```bash
cp example.env babyshop_app/.env
```

Edit `babyshop_app/.env`:

```bash
 BTS_SECRET_KEY=change-me
 DEBUG=True
 ALLOWED_HOSTS=127.0.0.1,localhost

 # Database settings
 DB_ENGINE=<your_db_engine>
 DB_NAME=<your_db_name>
 DB_USER=<your_db_user>
 DB_PASSWORD=<your_db_password>
 DB_HOST=<your_db_host>
 DB_PORT=<your_db_port>
```

### Use Docker to Start Your App

Step 1 — Build an image

You can build the container image by running the following command:

```bash
  # Use -t to provide an image tag.
  # -> baby-tools-shop is the image name, 'local' is the tag
  docker build -t baby-tools-shop:local .
```

Step 2 — Run a container

To start a container based on the image, use the following command in your terminal:

```bash
docker run -p 8000:8000 --env-file ./.env baby-tools-shop:local
```

Verify the application is running by visiting

```bash
http://<your_ip>:8000
```

## Project Structure

```text
baby-tools-shop/                                  # Repository root
├── .gitignore                                    # Git ignore rules
├── LICENSE                                       # Project license
├── README.md                                     # Project documentation
├── example.env                                   # Example environment template
├── project_images/                               # Images referenced in README
└── babyshop_app/                                 # Django application root
  ├── .dockerignore                             # Docker build ignore file
  ├── Dockerfile                                # Docker image definition
  ├── entrypoint.sh                             # Container startup script
  ├── manage.py                                 # Django management CLI entrypoint
  ├── requirements.txt                          # Python dependencies list
  ├── db.sqlite3                                # SQLite database file
  ├── babyshop/                                 # Django project configuration module
  │   ├── __init__.py                           # Marks Python package
  │   ├── asgi.py                               # ASGI application entrypoint
  │   ├── settings.py                           # Global Django settings
  │   ├── urls.py                               # Root URL routing
  │   └── wsgi.py                               # WSGI application entrypoint
  ├── products/                                 # Products domain app
  │   ├── __init__.py                           # Marks Python package
  │   ├── admin.py                              # Admin registrations
  │   ├── apps.py                               # AppConfig definition
  │   ├── models.py                             # Product and category models
  │   ├── tests.py                              # App tests
  │   ├── urls.py                               # App URL patterns
  │   ├── views.py                              # Request handlers/views
  │   └── migrations/                           # Database migrations for products app
  │       ├── __init__.py                       # Migrations package marker
  │       ├── 0001_initial.py                   # Initial schema migration
  │       ├── 0002_product_price.py             # Adds product price field
  │       ├── 0003_alter_product_name.py        # Alters product name field
  │       ├── 0004_category_product_category.py # Adds category + relation
  │       └── 0005_rename_describtion_product_description.py # Rename typoed field
  ├── users/                                    # Authentication/users app
  │   ├── __init__.py                           # Marks Python package
  │   ├── admin.py                              # Admin registrations
  │   ├── apps.py                               # AppConfig definition
  │   ├── forms.py                              # Login/register forms
  │   ├── models.py                             # User-related models
  │   ├── tests.py                              # App tests
  │   ├── urls.py                               # App URL patterns
  │   ├── views.py                              # Authentication views
  │   └── migrations/                           # Database migrations for users app
  │       └── __init__.py                       # Migrations package marker
  ├── templates/                                # Shared HTML templates
  │   ├── login.html                            # Login page template
  │   ├── product.html                          # Product detail page template
  │   ├── products.html                         # Product listing template
  │   ├── register.html                         # Registration page template
  │   └── partoftemp/                           # Reusable template partials
  │       ├── _dashboard.html                   # Base dashboard layout
  │       └── footer.html                       # Footer partial
  └── media/                                    # Uploaded media files
    └── products/
    
```

## Usage

### Configure Your Server

1. Log in to your server

```bash
ssh -i ~/.ssh/your_key <your_root_name>@<your_ip>
```

2. Clone the Git repository.
3. Navigate to the main directory.
4. Set up the environment file.

> [!TIP]
>
> The environment file should be stored next to the `manage.py` file.
> The `DEBUG` parameter should be set to `False` to avoid verbose error output in production.
> The database settings should also be configured as mentioned in the Quickstart section.

```bash
 # `ALLOWED_HOSTS`: Provide a comma-separated list of allowed hosts.
 ALLOWED_HOSTS=<your_ip>,<www.your-domain.com>

 # `DEBUG`: Set to `True` for development or `False` for production. Default is `True`.
 DEBUG=False
 # Database settings
 # See the "Edit `babyshop_app/.env`" section mentioned in Quickstart.
```

5. Verify the application in your browser.
6. Verify the application is running.
7. Create a superuser account (optional).

```bash
 docker exec -it <your_container_id> python manage.py createsuperuser
```
> [!NOTE]
>
> Whenever you make changes to your models, run the following workflow.

```bash
 # 1. Create migration files
 docker exec -it <your_container_id> python manage.py makemigrations

 # 2. Apply migrations to the database
 docker exec -it <your_container_id> python manage.py migrate
```
