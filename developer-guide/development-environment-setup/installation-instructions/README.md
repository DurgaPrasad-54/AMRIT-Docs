# Installation Instructions

## Development Environment Setup

### Step 1: Software Dependencies
First, ensure all required software is installed by following the [Software Dependencies Guide](https://piramal-swasthya.gitbook.io/amrit/developer-guide/development-environment-setup/software-dependencies):

#### Dockerized Path (Recommended)
- Make sure you followed Step 7 in the Software Dependencies Guide to install Docker and Docker Compose
- **Skip** Steps 8 & 9 as you won't need manual database installation

#### Manual Path
- Make sure you followed Steps 8 & 9 in the Software Dependencies Guide to install:
  - MySQL and MongoDB (Step 8)
  - Redis for caching (Step 9)
- **Skip** Step 7 as you won't need Docker

### Step 2: Infrastructure Setup

#### Dockerized Path (Recommended)
1. Clone the AMRIT DevOps repository
2. Follow the [DevOps Setup Documentation](https://github.com/PSMRI/AMRIT-DevOps/tree/main/amrit-local-setup)
   - This will automatically set up your databases with required dummy data

> 💡 **We strongly recommend the Docker path** as it ensures consistent database setup and eliminates potential conflicts.

#### Manual Path
1. Refer to the [Database Schema Documentation](https://piramal-swasthya.gitbook.io/amrit/data-management/database-schema) to:
   - Set up required database schemas
   - Load necessary data

### Step 3: Application Setup
After completing either path above:
1. Clone the required application repositories
