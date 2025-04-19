# CI/CD pipelines

With AMRIT, we are committed to delivering high-quality, secure, and reliable software through robust Continuous Integration and Continuous Delivery (CI/CD) practices. Our approach ensures that every commit & pull request meets the industry standards of quality, security, and performance.

### The CI/CD Journey: From Code to Deployment

### 1. Collaborative Development

#### Pull Request Workflow

When a contributor works on new features or fixes, every change goes through a rigorous review process.&#x20;

* [**Pull Request Template**](https://github.com/PSMRI/.github/blob/main/PULL_REQUEST_TEMPLATE.md): Ensure that new changes are documented well and a JIRA ticket ID is provided in the description.&#x20;
* **Code Review**: Every code modification is submitted via a structured pull request. PR review process is assisted by [Code Rabbit](https://www.coderabbit.ai/).
* **Automated Checks**: Immediate validation of code quality and security.
* **Commitlint Check**: All commit messages in pull requests are automatically validated against AMRIT's commit message convention using commitlint to ensure:
  * Consistent commit history across the project
  * Better readability and automated changelog generation
  * Proper categorization of changes (features, fixes, etc.)
  * Commits follow the required format: `type(scope): subject`
  * For more details on the commit message format and types, see the [Contribution Guidelines](https://piramal-swasthya.gitbook.io/amrit/community-and-support/contribution-guidelines).
* **Transparency**: Clear documentation of changes, motivations, and testing performed.

### 2. Automated Quality Assurance

#### Comprehensive Automated Checks

**User Interface (Angular) Workflow**

* **Code Linting**: Automatic code style and quality checks
* **Dependency Security Scan**:
  * Continuous monitoring of npm package vulnerabilities
  * Automatic blocking of pull requests with fixable security issues
* **Performance and Standards**: Ensuring code meets our strict coding guidelines

**Backend API (Spring Boot) Workflow**

* **Build Validation**: Automatic compilation and build checks
* **Packaging**: Creation of deployable artefacts
* **Security Analysis**:
  * Static Application Security Testing (SAST)
  * CodeQL intelligent security scanning

### 3. Security-First Approach

#### Proactive Security Measures

* **Dependency Auditing**:
  * Continuous scanning of all project dependencies
  * Immediate alerts for potential vulnerabilities
* **Code Analysis**:
  * Advanced CodeQL scanning
  * Identifying potential security risks before deployment

### 4. Deployment Readiness

#### Artifact Preparation

* **Standardized Packaging**:
  * WAR files for API
  * Consistent and reproducible deployment artifacts
* **Version Control**:
  * Tracked and versioned deployments
  * Easy rollback capabilities

### Deployment

#### Deployment to various servers can be accomplished by Jenkins. PSMRI runs an in-house Jenkins instance to deploy the war files to various servers.

More details about Jenkins pipelines can be found in the [AMRIT-DevOps](https://github.com/PSMRI/AMRIT-DevOps) repository.

### Our Commitment

Our CI/CD process is more than just a technical workflow. It's a commitment to:

* Delivering high-quality healthcare technology
* Ensuring the safety and reliability of our digital solutions
* Continuously improving our development practices
