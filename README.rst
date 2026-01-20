.. image:: ./.github/assets/logo.png

|

.. image:: https://github.com/nsidnev/fastapi-realworld-example-app/workflows/API%20spec/badge.svg
   :target: https://github.com/nsidnev/fastapi-realworld-example-app

.. image:: https://github.com/nsidnev/fastapi-realworld-example-app/workflows/Tests/badge.svg
   :target: https://github.com/nsidnev/fastapi-realworld-example-app

.. image:: https://github.com/nsidnev/fastapi-realworld-example-app/workflows/Styles/badge.svg
   :target: https://github.com/nsidnev/fastapi-realworld-example-app

.. image:: https://codecov.io/gh/nsidnev/fastapi-realworld-example-app/branch/master/graph/badge.svg
   :target: https://codecov.io/gh/nsidnev/fastapi-realworld-example-app

.. image:: https://img.shields.io/github/license/Naereen/StrapDown.js.svg
   :target: https://github.com/nsidnev/fastapi-realworld-example-app/blob/master/LICENSE

.. image:: https://img.shields.io/badge/code%20style-black-000000.svg
   :target: https://github.com/ambv/black

.. image:: https://img.shields.io/badge/style-wemake-000000.svg
   :target: https://github.com/wemake-services/wemake-python-styleguide

----------

**NOTE**: This repository is not actively maintained because this example is quite complete and does its primary goal - passing Conduit testsuite.

More modern and relevant examples can be found in other repositories with ``fastapi`` tag on GitHub.

Prerequisites
-------------

- Python 3.9 (recommended for best compatibility with dependencies)
- Poetry (for dependency management)
- Docker (for running PostgreSQL)

Quickstart
----------

1. Start PostgreSQL using Docker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run PostgreSQL in a Docker container with port mapping::

    docker run --name pgdb --rm -d \
        -p 5432:5432 \
        -e POSTGRES_USER=postgres \
        -e POSTGRES_PASSWORD=postgres \
        -e POSTGRES_DB=rwdb \
        postgres:11.5-alpine

Verify the container is running::

    docker ps | grep pgdb

2. Install dependencies with Poetry
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Clone the repository and install dependencies::

    git clone https://github.com/nsidnev/fastapi-realworld-example-app
    cd fastapi-realworld-example-app
    poetry install

3. Configure environment variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Create a ``.env`` file in the project root with the following content::

    DATABASE_URL=postgresql://postgres:postgres@localhost:5432/rwdb
    SECRET_KEY=your-secret-key-here
    APP_ENV=dev

You can generate a secure secret key with::

    openssl rand -hex 32

The ``APP_ENV`` variable controls which settings are loaded:

- ``dev`` - Development settings (default database URL, debug enabled)
- ``prod`` - Production settings (requires DATABASE_URL and SECRET_KEY)
- ``test`` - Test settings (used when running tests)

4. Run database migrations
~~~~~~~~~~~~~~~~~~~~~~~~~~

Apply the database migrations::

    poetry run alembic upgrade head

5. Start the application
~~~~~~~~~~~~~~~~~~~~~~~~

Run the web application in development mode::

    poetry run uvicorn app.main:app --reload

The application will be available at http://localhost:8000

Troubleshooting
~~~~~~~~~~~~~~~

If you encounter a database connection error like::

    sqlalchemy.exc.OperationalError: (psycopg2.OperationalError) could not connect to server

Verify that:

1. The PostgreSQL container is running: ``docker ps | grep pgdb``
2. The ``DATABASE_URL`` in your ``.env`` file points to the correct host (use ``localhost`` when running Docker with port mapping)



Run tests
---------

Tests for this project are defined in the ``tests/`` folder.

This project uses `pytest <https://docs.pytest.org/>`_ to define tests because it allows you to use the ``assert`` keyword with good formatting for failed assertions.

Prerequisites for testing
~~~~~~~~~~~~~~~~~~~~~~~~~

Before running tests, ensure:

1. PostgreSQL is running (see Quickstart section)
2. The ``DATABASE_URL`` environment variable is set in your ``.env`` file

Running all tests
~~~~~~~~~~~~~~~~~

To run all tests with coverage reporting::

    poetry run pytest

The test configuration in ``pyproject.toml`` includes coverage reporting and parallel test execution.

Running specific tests
~~~~~~~~~~~~~~~~~~~~~~

To run a specific test file::

    poetry run pytest tests/test_api/test_routes/test_users.py

To run a specific test function::

    poetry run pytest tests/test_api/test_routes/test_users.py::test_user_can_not_take_already_used_credentials

Running tests with verbose output
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For more detailed test output::

    poetry run pytest -v

Deployment with Docker
----------------------

You must have ``docker`` and ``docker-compose`` tools installed to work with material in this section.
First, create ``.env`` file like in `Quickstart` section or modify ``.env.example``.
``POSTGRES_HOST`` must be specified as `db` or modified in ``docker-compose.yml`` also.
Then just run::

    docker-compose up -d db
    docker-compose up -d app

Application will be available on ``localhost`` in your browser.

Web routes
----------

All routes are available on ``/docs`` or ``/redoc`` paths with Swagger or ReDoc.


Project structure
-----------------

Files related to application are in the ``app`` or ``tests`` directories.
Application parts are:

::

    app
    ├── api              - web related stuff.
    │   ├── dependencies - dependencies for routes definition.
    │   ├── errors       - definition of error handlers.
    │   └── routes       - web routes.
    ├── core             - application configuration, startup events, logging.
    ├── db               - db related stuff.
    │   ├── migrations   - manually written alembic migrations.
    │   └── repositories - all crud stuff.
    ├── models           - pydantic models for this application.
    │   ├── domain       - main models that are used almost everywhere.
    │   └── schemas      - schemas for using in web routes.
    ├── resources        - strings that are used in web responses.
    ├── services         - logic that is not just crud related.
    └── main.py          - FastAPI application creation and configuration.
