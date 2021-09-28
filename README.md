### Shared GitHub workflows for FOLIO

#### Added on : 15.02.2021

#### Directory `workflow-templates` contains all the CI/CD workflows for the project

### Usage

**buildNPM.yml** is the template for NPM-based CI jobs. The YAML file has the following jobs:

- build: Contains yarn install, lint and test jobs
- sonarQube: Contains the SonarCloud check jobs
- publishModDescriptor: Contains step for publishing module descriptor

**buildMaven.yml** is the template for Maven/Java based CI jobs. The YAML file configures the following jobs:

- set-up: Application set-up and build jobs. Step publishes Docker image and Mod descriptor
- RAML-Lint: Lint jobs for Maven projects

### Setting up shared workflow within a project

Click on the `Actions` tab and click on the `New Workflow` button.
The workflows provided by folio-org should be listed there. Chose a workflow depending on the module type. **ui-** modules generally need **NPM based** workflows and **mod-** modules need **Maven based** modules.

### TODO

This is the Proof of concept version. Detailed CI/CD pipelines still need to be developed. The CI workflows can be used to test the working of Github Actions.
