# PostgreSQL Configurable Container

This repository provides an easy way to integrate a PostgreSQL database container into your project using Docker and Docker Compose. It includes configurable options and initialization scripts to get you started quickly.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Usage](#usage)
- [Examples](#examples)
  - [Django Integration](#django-integration)
  - [Flask Integration](#flask-integration)
  - [Go Integration](#go-integration)
- [Troubleshooting](#troubleshooting)

## Prerequisites

- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Getting Started

1. **Clone the repository**

   ```bash
   git clone https://github.com/LorenzoAnas/postgresql-configurable-container.git
   cd postgresql-configurable-container
   ```

2. **Configure environment variables**

   Edit the `.env` file to suit your needs.

3. **Start the PostgreSQL container**

   ```bash
   docker-compose up -d
   ```

4. **Verify the setup**

   You can verify that the database is running and the test data is inserted from the init.sql file.

   ```bash
   docker exec -it postgres_container psql -U admin -d sampledb -c "SELECT * FROM users;"
   ```

## Configuration

All configurable options are stored in the `.env` file.

- `POSTGRES_VERSION`: Version of PostgreSQL (default: `13`)
- `POSTGRES_USER`: Database admin username (default: `admin`)
- `POSTGRES_PASSWORD`: Database admin password (default: `admin123`)
- `POSTGRES_DB`: Default database name (default: `sampledb`)
- `HOST_PORT`: Host port for PostgreSQL (default: `5432`)

## Usage

You can integrate this PostgreSQL setup into your projects. Below are examples for Django, Flask, and Go.

## Examples

### Django Integration

**Settings Configuration**

In your `settings.py`, configure the `DATABASES` setting:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'sampledb',
        'USER': 'admin',
        'PASSWORD': 'admin123',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

**Install Dependencies**

```bash
pip install psycopg2
```

**Run Migrations**

```bash
python manage.py migrate
```

### Flask Integration

**Install Dependencies**

```bash
pip install psycopg2 flask_sqlalchemy
```

**Application Configuration**

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://admin:admin123@localhost:5432/sampledb'
db = SQLAlchemy(app)
```

**Define a Model**

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100))
    email = db.Column(db.String(100))
```

**Run the Application**

```bash
python app.py
```

### Go Integration

**Install Dependencies**

```bash
go get github.com/lib/pq
```

**Database Connection**

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

func main() {
    connStr := "user=admin password=admin123 dbname=sampledb host=localhost port=5432 sslmode=disable"
    db, err := sql.Open("postgres", connStr)
    if err != nil {
        panic(err)
    }
    defer db.Close()

    rows, err := db.Query("SELECT id, name, email FROM users")
    if err != nil {
        panic(err)
    }
    defer rows.Close()

    for rows.Next() {
        var id int
        var name, email string
        err = rows.Scan(&id, &name, &email)
        if err != nil {
            panic(err)
        }
        fmt.Printf("ID: %d, Name: %s, Email: %s\n", id, name, email)
    }
}
```

**Run the Application**

```bash
go run main.go
```

## Troubleshooting

- **Port Conflicts**: Ensure the `HOST_PORT` in the `.env` file does not conflict with other services.
- **Database Connection Errors**: Double-check your connection strings and credentials.
- **Docker Issues**: Make sure Docker and Docker Compose are correctly installed and running.

