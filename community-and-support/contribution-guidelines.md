# Contribution guidelines

Thank you for your interest in contributing to **AMRIT**! Your contributions help us improve the platform and make it more valuable for users across the healthcare community. This guide will help you get started with contributing to the project.

**1. Getting started**

Before contributing, please take a moment to familiarize yourself with the project:

* **Explore the codebase:** Review the [AMRIT repositories](https://github.com/PSMRI/) to understand the project's structure and core components.
* **Read the documentation:** Check the documentation to understand how AMRIT works and its key features.
* **Check open issues:** Browse the [GitHub Issues](https://github.com/PSMRI/AMRIT/issues) to see if there are any open issues you can help with.

**2. Types of contributions**

There are many ways to contribute to AMRIT, including:

* **Reporting bugs:** If you find a bug, please report it by opening a [GitHub Issue](https://github.com/PSMRI/AMRIT/issues/new/choose). Include as much detail as possible, including steps to reproduce the issue and any error messages.
* **Suggesting enhancements:** Have an idea for a new feature or improvement? Share it with us by starting a discussion in the [GitHub Discussions forum](https://github.com/orgs/PSMRI/discussions) or by opening a feature request issue.
* **Writing code:** We welcome code contributions, whether it’s fixing bugs, adding new features, or improving existing functionality. See the "How to Submit a Pull Request" section below for details.
* **Improving documentation:** Clear and accurate documentation is vital. If you find gaps or errors in the documentation, please help us improve it.
* **Testing:** Help us by testing the platform and providing feedback. You can test new features, check for regressions, and ensure everything works as expected.

**3. How to submit a Pull Request (PR)**

When you're ready to contribute code to AMRIT, follow these steps:

1. **Fork the repository:** Start by forking the AMRIT repository to your GitHub account.
2. **Create a branch:** Create a new branch from the `main` branch in your fork. Name your branch descriptively, e.g., `feature/new-dashboard` or `bugfix/issue-123`.
3. **Make your changes:** Implement your changes in your branch. Please follow the existing code style and conventions.
4. **Commit your changes:** Write clear, concise commit messages that explain your changes.
5. **Push to your fork:** Push your changes to your fork on GitHub.
6. **Create a Pull Request:** Go to the original AMRIT repository and open a pull request. Provide a detailed description of your changes and link any related issues or discussions.

**4. Code style and standards**

To maintain consistency in the codebase, please adhere to the following guidelines:

* **Code style:** Follow the code style and conventions used in the existing codebase. Check for linting errors before submitting.
* **Testing:** Ensure your code is well-tested. If applicable, add unit tests for your changes.
* **Documentation:** Update or add documentation as needed, especially if your changes impact the user experience or developer API.

### Commit Message Convention

AMRIT follows a specific commit message format that is automatically enforced both locally and in pull requests:

* **Format**: `type(scope): subject`
* **Example**: `feat(login): add remember me functionality`

**Commit Types:**
* **feat**: A new feature
* **fix**: A bug fix
* **docs**: Documentation changes
* **style**: Code style changes (formatting, etc.)
* **refactor**: Code changes that neither fix bugs nor add features
* **perf**: Performance improvements
* **test**: Adding or fixing tests
* **build**: Changes to build process or tools
* **ci**: Changes to CI configuration
* **chore**: Other changes (e.g., maintenance tasks, dependencies)

This project uses Git hooks to enforce consistent code quality and commit message standards. Follow these steps to set up the hooks locally:

* **Prerequisites**
  * Node.js (v14 or later)
  * npm (comes with Node.js)

* **Setup Steps**
  1. **Install Node.js and npm**
     * Download and install from [nodejs.org](https://nodejs.org/)
     * Verify installation with:
       ```bash
       node --version
       npm --version
       ```

  2. **Install dependencies**
     * From the project root directory, run:
       ```bash
       npm ci
       ```
     * This installs required dependencies, including Husky and commitlint.

  3. **Verify hooks installation**
     * The hooks should be automatically installed by Husky.
     * You can verify by checking if the `.husky` directory contains executable hooks.

Your commit messages will be automatically validated when you commit, ensuring project consistency.

**5. Review Process**

Once you submit a pull request:

* **Automated checks:** Your pull request will go through automated checks (e.g., linting, unit tests). Please ensure all checks pass.
* **Code review:** A project maintainer will review your code. Be prepared to discuss and revise your code based on feedback.
* **Merge:** Once your pull request is approved and all checks pass, it will be merged into the `main` branch.

**6. Community guidelines**

* **Be respectful:** Treat all community members with respect. Constructive feedback and discussions are encouraged.
* **Stay on topic:** Keep discussions and contributions relevant to the project.
* **Ask for help:** If you’re unsure about something, don’t hesitate to ask for help. The community and maintainers are here to support you.

**7. Licensing**

By contributing to AMRIT, you agree that your contributions will be licensed under the GNU General Public License v3.0 License. Make sure you understand the licensing terms before submitting your contribution.

**8. Getting Help**

If you need any help with contributing, feel free to ask in the [GitHub Discussions forum](https://github.com/orgs/PSMRI/discussions) or reach out via email at amrit@piramalswasthya.org.

We appreciate your contributions and look forward to collaborating with you to make AMRIT even better!
