# VIEWS Platform Onboarding Guide

Welcome to the Views Platform! This guide is designed to help you navigate our repositories and utilize the available resources effectively.

## 1. Overview of Repositories

The VIEWS Platform comprises several specialized repositories:

- **[docs](https://github.com/views-platform/docs)**: This centralized documentation repository serves as the single source of truth for organizational standards, Organizational Decision Records (ODRs), workflows, guides, and best practices, ensuring clarity and consistency across all projects and repositories.

- **[views-pipeline-core](https://github.com/views-platform/views-pipeline-core)**: A comprehensive machine learning pipeline designed to produce monthly predictions of future violent conflict at both country and sub-country levels.

- **[views-stepshifter](https://github.com/views-platform/views-stepshifter)**: Contains modeling procedures, evaluation, and forecasting using Darts.

- **[views-models](https://github.com/views-platform/views-models)**: Hosts various models and related Jupyter Notebooks.

- **[views-hydranet](https://github.com/views-platform/views-hydranet)**: Implements modeling procedures, evaluation, and forecasting using HydraNet in PyTorch.

- **[views-evaluation](https://github.com/views-platform/views-evaluation)**: Focuses on the evaluation metrics and analysis of model outputs.

For more information on the individual contents of the repositories, please see the repository `README.md` files. 

## 2. Getting Started

**Step 1: Familiarize Yourself with the Documentation**

Begin by exploring the [docs](https://github.com/views-platform/docs) repository. This will provide you with an understanding of the organizational standards and decision records that guide our projects. Here you can also find the [Glossary](../FAQ%20&%20Glossary/glossary.md) containing all the relevant terminology for navigating through our material. Pay attention to the Organizational Decision Records (ODRs) to grasp the rationale behind key decisions. You can also find the [Contribution Guides](/Contribution%20Guides/README.md) for details on how to make your first contribution.

**Step 2: Set Up the Development Environment**

Clone the [views-pipeline-core](https://github.com/views-platform/views-pipeline-core) repository, as it contains the main pipeline for data processing and modeling. Follow the instructions in the [`README.md`](https://github.com/views-platform/views-pipeline-core/README.md) file to set up your development environment. Ensure you have all necessary dependencies installed and understand the pipeline's structure.

**Step 3: Explore Additional Repositories**

Depending on your area of interest, delve into the other repositories:

- For exploring the VIEWS models, see the [views-models](https://github.com/views-platform/views-models) repo. You can find all of the models in catalogs in the `README.md` file along with more information.

- For time series modeling with Darts, refer to [views-stepshifter](https://github.com/views-platform/views-stepshifter). Review the available models and consider running some of the provided examples to familiarize yourself with the framework.

- For HydraNet implementations, check out [views-hydranet](https://github.com/views-platform/views-hydranet). Understand the architecture and experiment with the models to gain hands-on experience.

- For evaluation metrics and analysis, visit [views-evaluation](https://github.com/views-platform/views-evaluation). Learn about the evaluation schemas and metrics used to assess model performance.

**Step 4: Review the Code of Conduct and Contributing Guidelines**

Ensure you read through the organization's Code of Conduct and Contributing Guidelines, found in the [`docs`](https://github.com/views-platform/docs) repo. This will help you understand the expectations and processes for contributing to the projects.

**Step 5: Always consult the Glossary**

Use the terms defined in the Glossary in your code and documentation. Consistent terminology helps avoid misunderstandings and makes the codebase easier to navigate.

**Step 6: Revisit the ODRs if you encounter any uncertainties.** 

They are the authoritative source for understanding the crucial decisions made in the project. **This project runs on the philosophy of "if you can't answer it with a link, it is not documented."** As such, all decisions regarding the organization or pipeline architecture should be properly documented with an ODR.


## 3. Additional Resources

- **Issue Tracker**: Monitor open issues or feature requests across repositories to stay informed about current priorities and discussions. Participate in discussions to gain deeper insights into ongoing developments.

- **About VIEWS**: For all other information about VIEWS, including our forecasts, collaborations and publications, visit our [website](https://viewsforecasting.org/). 


## 4. Contributor Checklist

To ensure a smooth onboarding process, make sure you have completed the following:

- [ ] **Read the Code of Conduct**: Understand the expected behavior within the community.

- [ ] **Set Up Development Environment**: Follow the setup guide in the relevant repository.

- [ ] **Review Existing Issues**: Familiarize yourself with current discussions and ongoing work.

- [ ] **Understand Key Repositories**: Explore the main repositories and their documentation.


By following this guide and utilizing the resources provided, you'll be well-prepared to contribute effectively to the Views Platform. If you have any questions or need further assistance, feel free to reach out to the project maintainers or the community.

Happy coding! 
