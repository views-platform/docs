## Versioning Conventions

| ODR Info           | Details                            |
|--------------------|------------------------------------|
| Subject            | Versioning Conventions             |
| ODR Number         | 002                                |
| Status             | 	Proposed |
| Category           | 	Process |
| Author             | Dylan            |
| Date               | 25.11.2024        |
| Owner              | [Insert Owner/approver (Name)]    |
| Review Date        | [Insert Last Review Date (DD.MM.YYYY)] |


---

## Context
*To streamline development and release procedures, it is clear that a uniform versioning convention is required.*

---

## Decision
All projects and packages inside the views-platform organization (e.g `views-platform-core` and `views-stepshifter`) will adopt the Semantic Versioning (SemVer) convention, using the format x.x.x.

The versioning format `x.y.z` will be used, where:

* `x` (Major): Incremented for incompatible API changes.
* `y` (Minor): Incremented for backward-compatible functionality.
* `z` (Patch): Incremented for backward-compatible bug fixes.

### Overview
*Summarize the decision clearly and concisely.*

---

## Consequences
### Positive Effects
- Creates a clear and predictable versioning scheme.
- Allows dependency management and compatibility tests.
- Improves communication between team members and stakeholders.

### Negative Effects
- Requires discipline and adherence to the versioning guidelines.
- Initial learning curve for team members unfamiliar with SemVer.

---

## Rationale
Semantic Versioning is a prevalent standard that offers a straightforward and systematic method to versioning. It simplifies dependency management and ensures that changes are appropriately conveyed. The decision to use SemVer is based on its proven track record and the benefits it offers for software quality and consistency.

### Considerations
- Ensure all team members are trained on the SemVer convention.
- Update existing documentation to reflect the new versioning scheme.
- Implement automated checks in the CI/CD pipeline to enforce versioning rules.

---

## Additional Notes
**Semantic Versioning (SemVer)** is a versioning scheme that uses a three-part version number: `MAJOR.MINOR.PATCH`

**MAJOR Version**
- **Purpose**: Indicates incompatible API changes.
- **Increment When**: There are changes that break backward compatibility. This could include removing or altering existing functionality in a way that existing users would need to modify their code to accommodate the changes.
- **Example**: If the current version is 1.4.3 and a breaking change is introduced, the new version would be 2.0.0.

**MINOR Version**
**Purpose**: Indicates the addition of new functionality in a backward-compatible manner.
**Increment When**: New features or significant improvements are added that do not break existing functionality. This allows users to upgrade without fear of their existing code breaking.
**Example**: If the current version is 1.4.3 and a new feature is added, the new version would be 1.5.0.

**PATCH Version**
- **Purpose**: Indicates backward-compatible bug fixes.
- **Increment When**: Bug fixes or minor changes are made that do not affect the API or add new features. These changes are typically small and do not require users to change their code.
- **Example**: If the current version is 1.4.3 and a bug is fixed, the new version would be 1.4.4.

---

