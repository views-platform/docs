## Package development guidelines

| ODR Info           | Details                            |
|--------------------|------------------------------------|
| Subject            | Package development guidelines                  |
| ODR Number         | 003             |
| Status             | Proposed |
| Category           | Infra |
| Author             | Dylan            |
| Date               | 29.11.2024        |
| Owner              | Dylan    |
| Review Date        | [Insert Last Review Date (DD.MM.YYYY)] |


---

## Context
To streamline Python package production and distribution throughout the VIEWS organization, clear norms and best practices must be established. This provides uniformity, quality, and ease of maintenance for all packages.
---

## Decision
All Python packages developed within the VIEWS organization will follow these guidelines for creating and publishing packages to PyPI using Poetry and GitHub Workflows. Package names should follow the format `views_{packagename}`.

**Overview**
1. **Package Naming**: All packages must be named using the format `views_{packagename}` See [the ODR for naming conventions](https://github.com/views-platform/docs/blob/main/ODRs/infra_001_repository_naming_conventions.md) for more information.
2. **Poetry for Dependency Management**: Use [Poetry](https://python-poetry.org/docs/) to manage dependencies and build the package.
3. **GitHub Workflows for CI/CD**: Implement [GitHub actions](https://docs.github.com/en/actions) to automate [testing](https://docs.pytest.org/en/stable/), [building](https://packaging.python.org/en/latest/tutorials/packaging-projects/), and [publishing](https://python-poetry.org/docs/libraries/) of packages.
4. **Gitignores**: Ensure your project has a .gitignore file to exclude unnecessary files from version control.
5. **Versioning**: See [this ODR](https://github.com/views-platform/docs/blob/main/ODRs/process_002_versioning_conventions.md) for VIEWS specific guidelines for package versioning.

### Overview

---

## Consequences

### Positive Effects
- Ensures consistent package naming and structure.
- Simplifies dependency management and package development.
- Automates the release process to reduce manual errors and effort.
- Improves teamwork and code quality through automated testing.

### Negative Effects
- Requires initial setup and learning for team members unfamiliar with Poetry and GitHub Workflows.
- Potential dependency on GitHub Actions for CI/CD, which may have limitations or require adjustments over time.

---

## Rationale
Using Poetry and GitHub Workflows offers a modern reliable way to manage Python packages. Poetry streamlines dependency management and packaging, whereas GitHub Workflows automates the CI/CD process, guaranteeing that packages are tested and published consistently.

### Considerations
- Ensure that all team members are trained to use Poetry and GitHub Workflows.
- Regularly review and update procedures to reflect changes in GitHub Actions or Poetry.
- Monitor the CI/CD process to address any issues as they arise.

---
