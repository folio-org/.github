### Shared GitHub workflows for FOLIO

#### Added on : 15.02.2021

#### Directory `wokflow-templates` contains all the CI/CD workflows for the project

### Usage

**buildNPM.yml** is the template for NPM based CI jobs. The YAML file has the following jobs:
- build: Contains yarn install, lint and test jobs
- sonarQube: Contains the SonarCloud check jobs
- publishModDescriptor: Contains step for publishing module descriptor

**buildMaven.yml** is the template for Maven/Java based CI jobs. The YAML file configures the following jobs
- set-up: Initial application build
- RAML-Lint: Lint jobs for Maven projects
- Maven-Deploy: Deploy jobs 


