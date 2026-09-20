# POS API

A backend Point of Sale (POS) API built with FastAPI, SQLAlchemy, and PostgreSQL.

The application provides APIs for managing products, categories, suppliers, users, customers, sales, sale items, payments, and receipts. It also includes an automated Pytest test suite and GitHub Actions continuous integration to verify that the application continues to work correctly as changes are introduced.

## Features

* Product management
* Category management
* Supplier management
* User management
* Customer management
* Sales management
* Sale item management
* Payment management
* Receipt management
* PostgreSQL database integration
* SQLAlchemy ORM
* API validation
* Automated testing with Pytest
* Isolated SQLite test database
* GitHub Actions continuous integration
* Interactive Swagger API documentation
* ReDoc API documentation

## Tech Stack

| Technology     | Purpose                         |
| -------------- | ------------------------------- |
| Python         | Backend programming language    |
| FastAPI        | REST API framework              |
| SQLAlchemy     | ORM and database interaction    |
| PostgreSQL     | Development database            |
| SQLite         | Isolated test database          |
| Pydantic       | Request and response validation |
| Uvicorn        | ASGI application server         |
| Pytest         | Automated testing               |
| PyJWT          | JWT functionality               |
| Passlib        | Password hashing                |
| python-dotenv  | Environment configuration       |
| GitHub Actions | Continuous integration          |

## Project Structure

```text
pos/
│
├── app/
│   ├── models/
│   │   ├── category.py
│   │   ├── customer.py
│   │   ├── payment.py
│   │   ├── product.py
│   │   ├── receipt.py
│   │   ├── sale.py
│   │   ├── sale_item.py
│   │   ├── supplier.py
│   │   └── user.py
│   │
│   ├── routers/
│   │   ├── category.py
│   │   ├── customer.py
│   │   ├── payment.py
│   │   ├── product.py
│   │   ├── receipt.py
│   │   ├── sale.py
│   │   ├── sale_item.py
│   │   ├── supplier.py
│   │   └── user.py
│   │
│   └── ...
│
├── tests/
│   ├── conftest.py
│   ├── test_categories.py
│   ├── test_customers.py
│   ├── test_products.py
│   ├── test_suppliers.py
│   ├── test_users.py
│   ├── test_sales.py
│   ├── test_sale_items.py
│   ├── test_payments.py
│   └── test_receipts.py
│
├── .github/
│   └── workflows/
│       └── tests.yml
│
├── database.py
├── main.py
├── conftest.py
├── pytest.ini
├── requirements.txt
└── README.md
```

## Architecture

The application is organized around FastAPI routers and SQLAlchemy models.

```text
Client
   |
   v
FastAPI
   |
   +-------------------------------+
   |               |               |
   v               v               v
Products        Customers        Users
   |               |               |
   +---------------+---------------+
                   |
                   v
                 Sales
                   |
          +--------+--------+
          |                 |
          v                 v
      Sale Items         Payments
          |
          v
       Products
          |
          v
       Receipts
```

## Database

The application uses PostgreSQL for normal development and deployment.

The database connection is configured through an environment variable:

```env
DATABASE_URL=postgresql://username:password@localhost:5432/pos_db
```

Create the database:

```sql
CREATE DATABASE pos_db;
```

The production/development database should never be used when running the automated test suite.

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/andylaique/pos.git
cd pos
```

### 2. Create a virtual environment

#### Windows

```bash
python -m venv venv
venv\Scripts\activate
```

#### macOS / Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure the database

Create a `.env` file:

```env
DATABASE_URL=postgresql://username:password@localhost:5432/pos_db
```

### 5. Start the API

```bash
uvicorn main:app --reload
```

The API will be available at:

```text
http://127.0.0.1:8000
```

## API Documentation

FastAPI provides interactive API documentation automatically.

### Swagger UI

```text
http://127.0.0.1:8000/docs
```

### ReDoc

```text
http://127.0.0.1:8000/redoc
```

## Automated Testing

The project includes an automated test suite built with Pytest.

The test suite is designed to verify the application's major entities and API operations without connecting to the normal PostgreSQL development database.

### Test Database

Tests use SQLite as an isolated test database.

This provides several benefits:

* Tests do not modify development data.
* Tests do not require a running PostgreSQL server.
* Each test run can start with a clean database.
* Tests are faster and easier to run locally.
* GitHub Actions can execute the suite without configuring a PostgreSQL service.

The test database is separate from the application's normal `DATABASE_URL`.

## What Is Tested

The test suite covers the application's major resources and common success and failure scenarios.

### CRUD Operations

Tests cover successful operations such as:

* Creating resources
* Retrieving resources
* Listing resources
* Updating resources
* Deleting resources

### Validation

Tests verify that invalid requests are rejected correctly, including:

* Missing required fields
* Invalid field values
* Incorrect data types
* Invalid request payloads
* Invalid identifiers where applicable

### Missing Resources

The suite verifies appropriate behavior when attempting to access resources that do not exist.

Examples include:

```text
GET /products/9999
GET /customers/9999
GET /suppliers/9999
GET /sales/9999
```

The API should return the appropriate HTTP error response instead of silently succeeding.

### Failure Scenarios

Tests also cover important failure cases, such as:

* Duplicate records where uniqueness is required
* Invalid relationships
* Invalid update requests
* Invalid delete requests
* Missing resources
* Invalid request bodies
* Other endpoint-specific error conditions

## Running Tests Locally

Make sure your virtual environment is activated and dependencies are installed.

Run the complete test suite:

```bash
pytest
```

For detailed output:

```bash
pytest -v
```

To run a specific test file:

```bash
pytest tests/test_products.py
```

To run a specific test:

```bash
pytest tests/test_products.py -k create
```

The test suite should run against SQLite and should not require the PostgreSQL development database.

## Test Isolation

The test configuration is intentionally separated from the normal application database.

```text
Development

FastAPI
   |
   v
PostgreSQL
   |
   v
Development Data


Automated Tests

Pytest
   |
   v
FastAPI
   |
   v
SQLite
   |
   v
Temporary Test Data
```

This prevents automated tests from accidentally creating, modifying, or deleting records in the development database.

## Continuous Integration

GitHub Actions is used to automatically run the test suite.

The workflow is located at:

```text
.github/workflows/tests.yml
```

The workflow runs when:

* Code is pushed to the repository
* A Pull Request is opened
* A Pull Request is updated

The CI pipeline performs the following steps:

```text
Push / Pull Request
        |
        v
Checkout repository
        |
        v
Set up Python
        |
        v
Install dependencies
        |
        v
Run Pytest
        |
        v
All tests pass?
     /       \
   Yes        No
    |          |
    v          v
Success       Fail
```

A failed test causes the GitHub Actions workflow to fail.

This prevents changes with failing tests from being considered successfully validated.

## GitHub Actions Workflow

The workflow follows a simple test-only CI process:

```yaml
name: Tests

on:
  push:
  pull_request:

permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v6

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.x"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: |
          pytest
```

No Heroku deployment is performed by this workflow. Its purpose is to automatically validate the application through the complete automated test suite.

## API Modules

The backend provides modules for:

```text
/categories
/suppliers
/products
/users
/customers
/sales
/sale-items
/payments
/receipts
```

## Example POS Workflow

A typical transaction follows this flow:

```text
1. User selects a customer
          |
          v
2. Products are selected
          |
          v
3. Products are added to a sale
          |
          v
4. Subtotal is calculated
          |
          v
5. Discount and tax are applied
          |
          v
6. Total is calculated
          |
          v
7. Payment is recorded
          |
          v
8. Sale is completed
          |
          v
9. Receipt is generated
          |
          v
10. Inventory is updated
```

## Security Considerations

The application stores password hashes rather than plaintext passwords.

For production deployment, additional security measures should be considered:

* Secure environment variables
* HTTPS
* Authentication and authorization
* Role-based access control
* Input validation
* Database backups
* Secure CORS configuration
* Rate limiting
* Production logging
* Monitoring
* Secret management

Never commit `.env` files or database credentials to GitHub.

Recommended `.gitignore` entries:

```gitignore
.env
venv/
__pycache__/
.pytest_cache/
*.pyc
```

## Future Improvements

* [ ] JWT authentication and refresh tokens
* [ ] Role-based authorization
* [ ] Inventory movement history
* [ ] Low-stock notifications
* [ ] Sales analytics
* [ ] Revenue reporting
* [ ] Product search and filtering
* [ ] Pagination
* [ ] Alembic database migrations
* [ ] Docker support
* [ ] API rate limiting
* [ ] Expanded integration tests
* [ ] Frontend POS interface
* [ ] Receipt PDF generation
* [ ] Production deployment

## Author

**Andy Laique**

Software Engineer | AI/ML | Data & Backend

GitHub: https://github.com/andylaique

## License

This project currently does not specify an open-source license.
