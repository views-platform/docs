# infra_001_repository_naming_conventions ODR

| ODR Info           | Details                            |
|--------------------|------------------------------------|
| Subject            | Repository Naming Conventions     |
| ODR Number         | 001                                |
| Status             | Proposed                          |
| Category           | Infra                             |
| Author             | Simon                              |
| Date               | 20.11.2024                         |
| Owner              | [Your Name/Approver]                           |
| Review Date        | [Insert Review Date (DD.MM.YYYY)]                |

---

## Context

The current naming practices for repositories create substantial confusion and a lack of clarity. Many repository names are not descriptive enough to indicate their content or purpose, and in some cases, repositories contain far more than their names suggest, leading to unclear scope and boundaries. This inconsistency makes it difficult to determine a repository's function at a glance, navigate the organization's repositories, and understand where specific responsibilities or functionalities reside. 

Improving the descriptiveness and accuracy of repository names is essential for fostering better alignment, onboarding, and operational efficiency.

---

## Decision

To establish clarity and consistency in repository naming, the following conventions will be adopted:

- **Start with the prefix `views-`**: All repositories will begin with this prefix to align with the organization’s platform identity.
- **Use lowercase letters**: Repository names will use lowercase characters for uniformity.
- **Separate words with hyphens (`-`)**: Hyphens will enhance readability.
- **Limit to three words**: Names should be concise but descriptive.
- **Reflect the true scope and boundaries**: Repository names must clearly and accurately describe the repository’s intended purpose, avoiding ambiguity and overlap with other repositories.

### Overview

These conventions aim to create repository names that are **descriptive** of their purpose, content, and boundaries, while maintaining consistency and navigability across the organization.

---

## Consequences

### Positive Effects
- **Descriptive Naming**: Developers can infer the purpose and scope of a repository from its name, reducing ambiguity and overlap.
- **Improved Searchability**: Clearer names make it easier to locate repositories in search results or lists.
- **Better Onboarding**: New team members can quickly understand the repository structure and what each repository contains.
- **Operational Clarity**: The naming conventions enforce thoughtful scoping of repositories, reducing redundant or unclear boundaries.

### Negative Effects
- **Refactoring Effort**: Renaming existing repositories to meet the new convention will require updates to import statements, documentation, and scripts, incurring a one-time cost.
- **Ongoing Maintenance**: Ensuring that future repositories adhere to the convention may require monitoring and review.

---

## Rationale

### Considerations
- Descriptive names provide immediate clarity about what each repository is and is not responsible for, reducing confusion and the risk of functionality creep.
- Prefixing with `views-` unifies repositories under the organizational identity, while hyphenated, lowercase naming follows industry best practices and enhances readability.
- Alternatives such as underscores and title case were considered but were rejected due to readability concerns and misalignment with common GitHub and Python conventions.
- The three-word limit ensures that names remain concise and scannable, while still being informative.

---

## Additional Notes

The issue of unclear naming is exemplified by repositories like `views_forecasting`, where the name does not sufficiently describe the repository's scope. Forecasting is a core activity of the organization, and the repository’s name fails to clarify what specific aspects of forecasting it handles or its boundaries relative to other repositories. 

The new convention requires names to address these concerns, making it easier for contributors to understand what is in a repository and how it fits within the broader ecosystem.

---

## Feedback and Suggestions

- [**Feedback**](https://github.com/views-platform/docs/issues)
- [**ODR Index**](/ODRs/index.md)
