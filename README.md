Table of contents:

* [AZD team requirements](#azd-team-requirements)
* [GitHub project settings](#github-project)
* [Testing](#testing)
* [Security](#security)
* [Package-specific recommendations](#package-specific)

# AZD team requirements

## Infra folder

- [ ] Bicep files should be formatted with `bicep format`. Can be automated with [pre-commit](https://github.com/Azure4DevOps/check-azure-bicep).
- [ ] Resources should include a dashboard so that `azd monitor` works, either by referencing the `monitoring.bicep` module or creating a dashboard separately. See [main.bicep](https://github.com/pamelafox/staticmaps-function/blob/ae6fa56af04787df13c124f3500ba143b418e4de/infra/main.bicep#L24)
- [ ] Application code should include either OpenCensus or OpenTelemetry so that the monitor is populated. See [todo/app.py](https://github.com/Azure/azure-dev/blob/cb28058af1e7139be4381532f6b1167d9cd948fb/templates/todo/api/python/todo/app.py#L48).
- [ ] `main.bicep` should reference modules from `core`, copied from [azure-dev](https://github.com/Azure/azure-dev/tree/main/templates/common/infra/bicep).
- [ ] ACA samples need an additional `service_name.bicep` and `service_name.parameters.json` for each containerapp deployed. See [api.bicep](https://github.com/pamelafox/flask-charts-api-container-app/blob/main/infra/api.bicep)

## Elsewhere

- [ ] `azure.yaml` should include metadata for telemetry. See [azure.yaml](https://github.com/pamelafox/flask-charts-api-container-app/blob/main/azure.yaml)
- [ ] `.github/workflows` should include [`azure-dev.yaml`](https://github.com/Azure/azure-dev/blob/main/templates/common/.github/workflows/bicep/azure-dev.yml) to support `azd pipeline config`.
- [ ] .devcontainer should contain a devcontainer.json and Dockerfile/docker-compose.yaml in order to create a full local dev environment. Start with [azure-dev versions](https://github.com/Azure/azure-dev/tree/cb28058af1e7139be4381532f6b1167d9cd948fb/templates/common/.devcontainer) and modify as needed.
- [ ] Include architecture diagram in README.md
- [ ] If using "Open in Codespaces" buttons, use same as TODO samples. See [README.md](https://github.com/Azure/azure-dev/blob/main/templates/todo/projects/python-mongo-swa-func/README.md)

# GitHub project settings

- [ ] The project should be usable as a template. (Settings > General > Template Repository)
- [ ] The project should have Discussions enabled to lower barrier to engagement. (Settings > General > Features)
- [ ] The project should have all Dependabot features enabled (Settings > Code Security and Analysis > Dependabot)
- [ ] The project should have a [CODE_OF_CONDUCT.md](https://github.com/pamelafox/flask-surveys-container-app/blob/main/.github/CODE_OF_CONDUCT.md)
- [ ] The project should have a [ISSUE_TEMPLATE.md](https://github.com/pamelafox/flask-surveys-container-app/blob/main/.github/ISSUE_TEMPLATE.md)
- [ ] The project should have a [PULL_REQUEST_TEMPLATE.md](https://github.com/pamelafox/flask-surveys-container-app/blob/main/.github/PULL_REQUEST_TEMPLATE.md)

# Testing

- [ ] The repo should use either `pytest` or the framework-specific testing module.
- [ ] The repo should include the coverage library (typically via `pytest-cov`).
- [ ] The pyproject.toml file should configure pytest to check coverage for the application code, using --cov= option. See [pyproject.toml](https://github.com/pamelafox/simple-fastapi-container/blob/f0111034355c733fa55382a5af49f3193ed074cc/pyproject.toml#L12)
- [ ] The pyproject.toml file should configure coverage to fail if the coverage is below 100% threshold. See [pyproject.toml](https://github.com/pamelafox/simple-fastapi-container/blob/f0111034355c733fa55382a5af49f3193ed074cc/pyproject.toml#L21)
- [ ] A CI/CD workflow should run all the unit tests, and fail if they fail.

## API tests

If the repo includes an API of some sort:

- [ ] Either schemathesis or hypothesis should be used to perform property-based testing of the API parameters. You may want to run those separately from unit tests for speed reasons. See [property_based.py](https://github.com/pamelafox/flask-charts-api-container-app/blob/main/src/tests/property_based.py), [python-check.yaml](https://github.com/pamelafox/flask-charts-api-container-app/blob/befdfa1bcaf7a1afd48874c4de22a9094f40ee3e/.github/workflows/python-check.yaml#L35)

## Frontend tests

If the repo includes a frontend (HTML/CSS):

- [ ] Playwright tests should be used for end-to-end user flow tests. See [playwright.py](https://github.com/Azure-Samples/azure-django-postgres-aca/blob/main/demo-code/relecloud/playwright.py)
- [ ] Playwright tests should be run in CI/CD (possibly in a different workflow from unit tests). See [playwright.yaml](https://github.com/Azure-Samples/azure-django-postgres-aca/blob/main/.github/workflows/playwright.yaml)
- [ ] Axe should be used to check for accessibility issues. Errors should either be uploaded to Code Scanning tab, displayed in the pull request, or break the workflow.

## Load tests

(Anthony!)

# Dependencies

You can choose to either generate requirements files using pip-tools with .in files or you can write them directly, as long as they follow these guidelines.

- [ ] The root should contain requirements-dev.txt for non-prod packages (linting, formatting, and testing). The package versions don't need to be specified. See [requirements-dev.txt](https://github.com/pamelafox/flask-surveys-container-app/blob/main/requirements-dev.txt) 
- [ ] The requirements-dev.txt file should recursively include a requirements.txt file with the production dependencies.
- [ ] The deployed code should contain requirements.txt for prod packages. See [requirements.txt](https://github.com/pamelafox/flask-surveys-container-app/blob/main/src/requirements.txt)
- [ ] The requirements.txt file should pin exact versions for each package. See [requirements.txt](https://github.com/pamelafox/flask-surveys-container-app/blob/main/src/requirements.txt)
- [ ] The repo must be enabled to use dependabot and include a dependabot.yaml file. It should include at least two sections, one for "pip" and one for "github-actions". See [dependabot.yaml](https://github.com/Azure-Samples/azure-django-postgres-aca/blob/main/.github/.dependabot.yml) Optionally, the repo can include a [dependabot-bot.yaml](https://github.com/pamelafox/flask-surveys-container-app/blob/main/.github/dependabot-bot.yml). 
- [ ] The dependabot.yaml file must point at the directory which contains "requirements-dev.txt". Dependabot should follow the recursive instruction and find "requirements.txt" as well.

# Security

- [ ] CI/CI runs security-devops-action on templates and either breaks build for errors or uploads alerts. See [azure-dev-validate.yaml](https://github.com/tonybaloney/django-on-azure/blob/main/.github/workflows/azure-dev-validate.yml)
- [ ] README includes section about security, acknowledging any drawbacks to default infra and including recommendations, especially in regards to database username/passwords. See [README.md#security](https://github.com/pamelafox/django-quiz-app#security)

## Database access

- [ ] Uses a VNet with managed identity. (No sample yet exists, Aaron working on it).
- [ ] Uses a VNet. See [vnet.bicep](https://raw.githubusercontent.com/pamelafox/my-py-talks/main/postgres-bicep/vnet.bicep)
- [ ] Uses managed identity. This requires an additional script, as it's not possible to setup entirely in Bicep. See [flask-postgresql-managedidentity](https://github.com/Azure-Samples/flask-postgresql-managed-identity/tree/main/scripts)

### Username/password

Most samples should include VNet integration, but some may choose to not use a VNet for cost or complexity reasons. Much care must be taken in that case, but even with a VNet, best practices around usernames/passwords should be followed.

- [ ] Username should be somewhat randomly generated. Note that [azure-dev issue #1939](https://github.com/Azure/azure-dev/issues/1939) prevents using a completely random username, but uniqueString() function can be used instead. See [main.bicep](https://github.com/pamelafox/django-quiz-app/blob/main/infra/main.bicep#L36)
- [ ] Password should be randomly generated using secretOrRandomPassword and stored in Key Vault. See [main.parameters.json](https://github.com/pamelafox/django-quiz-app/blob/main/infra/main.parameters.json#L15), [main.bicep](https://github.com/pamelafox/django-quiz-app/blob/main/infra/main.bicep#L133)
- [ ] If using App Service, username and password should be referenced using secret refs, not stored directly in environment variables. See [main.bicep:DBPASS](https://github.com/pamelafox/django-quiz-app/blob/main/infra/main.bicep#L80)
- [ ] If using Container Apps, username and password should be retrieved using the Key Vault SDK. See [production.py](https://github.com/pamelafox/flask-surveys-container-app/blob/main/src/backend/settings/production.py)

### Firewall rules

- [ ] The firewall rules should *only* permit access to other Azure servers (0.0.0.0-0.0.0.0), not to any IP anywhere. There can also be a rule to allow the developer to specify their local IP, but make sure not to hardcode any IP there. See [main.bicep:allowAzureIPsFirewall](https://github.com/pamelafox/flask-surveys-container-app/blob/main/infra/main.bicep#L70)
- [ ] The README should contain a warning that using this firewall rule means that other Azure customers can access your server if they discover username/password.

## Secret keys

Both Django and Flask have a SECRET_KEY setting used for cryptographic signing.

- [ ] SECRET_KEY must be generated and stored in Key Vault. See [main.parameters.json](https://github.com/pamelafox/flask-surveys-container-app/blob/69a459dad573745202c790b296b51cb5a49fe7a8/infra/main.parameters.json), [main.bicep](https://github.com/pamelafox/flask-surveys-container-app/blob/69a459dad573745202c790b296b51cb5a49fe7a8/infra/main.bicep#L124)
- [ ] SECRET_KEY should *not* have a default value in production settings.

## Django admin

- [ ] The admin URL should either be disabled or be randomly generated, to prevent drive-by Django admin login attempts. See [main.bicep:ADMIN_URL](https://github.com/pamelafox/django-quiz-app/blob/main/infra/main.bicep#L76) and [production.py:ADMIN_URL](https://github.com/pamelafox/django-quiz-app/blob/main/quizsite/production.py#L9)


# Package-specific

## GUnicorn

- [ ] There should be a test to check that the gunicorn configuration file is valid. See [gunicorn_test.py](https://github.com/pamelafox/simple-fastapi-container/blob/main/src/gunicorn_test.py)
- [ ] There should be a gunicorn.conf.py file that specifies max_requests, max_requests_jitter, log_file, bind. See [FastAPI: gunicorn.conf.py](https://github.com/pamelafox/simple-fastapi-container/blob/main/src/gunicorn.conf.py) or [Flask: gunicorn.conf.py](https://github.com/pamelafox/flask-charts-api-container-app/blob/befdfa1bcaf7a1afd48874c4de22a9094f40ee3e/src/gunicorn.conf.py#L4)
- [ ] The gunicorn.conf.py should calculate the optimal number of workers and threads based on CPU count. (If the worker class is uvicorn, threads cannot be specified). See [FastAPI: gunicorn.conf.py](https://github.com/pamelafox/simple-fastapi-container/blob/main/src/gunicorn.conf.py) or [Flask: gunicorn.conf.py](https://github.com/pamelafox/flask-charts-api-container-app/blob/befdfa1bcaf7a1afd48874c4de22a9094f40ee3e/src/gunicorn.conf.py#L4)

## Flask

- [ ] The README command should use the Flask module to run the server, not call the Python file directly. See [README#local-dev](https://github.com/pamelafox/simple-flask-server-example#local-development)
- [ ] The README command for running the local server should include `--debug`. See [README#local-dev](https://github.com/pamelafox/simple-flask-server-example#local-development)
- [ ] The README command for running the local server should include `--port=50505` (to avoid collisions with built-in Mac application). See [README#local-dev](https://github.com/pamelafox/simple-flask-server-example#local-development)
- [ ] The devcontainer.json should expose ports 5000 and 50505. See [devcontainer.json](https://github.com/pamelafox/simple-flask-api-azure-function/blob/ea332d8a2d0b4e36a967af009c4591a261f15bc7/.devcontainer/devcontainer.json#L31)
