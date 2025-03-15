# Workshop 1: Use Power Platform Pipelines for Solution Deployments

## Workshop Pre-requisites
- Three Power Platform environments to create a pipeline.
- Identify the host, development, and target environments.
  
   | Development | UAT       | PROD      | HOST      |
   |------------|----------|----------|----------|
   | CPPS DEV   | CPPS UAT | CPPS PROD | CPPS PROD |
- All environments used in pipelines must have a Microsoft Dataverse database.
- You must have a Power Platform administrator or Dataverse system administrator role to install the pipelines application.
- The target environments used in the pipeline must be enabled as [Managed Environments](https://learn.microsoft.com/en-us/power-platform/admin/managed-environment-overview).
- Environment IDs of the development and target environments. More information: [Find your environment and organization ID](https://learn.microsoft.com/power-platform/admin/environments-overview)

## Introduction
- **Deployment Pipeline Configuration app** - A Power Platform tool that helps automate and manage deployments across environments like Development, Test, and Production. It streamlines ALM processes, supports version control, and integrates with Power Platform Pipelines for efficient solution deployment.
- **Host environment** - This special-purpose environment acts as the storage and management plane for all pipeline configuration, security, and run history.
- **Development environment** - This environment is where you develop solutions. A pipeline can be run from within any development environment linked to it.
- **Target environment** - The destination environment a pipeline deploys solutions to, such as integration testing, user acceptance testing (UAT), or production.

## Workshop Solution
Download the [solution file](LabSolution/ALMSolution_1_0_0_0.zip) and save it on your local machine.

## Lab Instructions

### Install Pipelines Application in Your Host Environment
1. Sign in to [Power Platform Admin Center](https://admin.powerplatform.microsoft.com), then select **Manage > Dynamics 365 apps**.
2. Scroll down within the right-side panel until you find **Power Platform Pipelines**.
3. Click the three dots next to the app and select **Install**.
4. Select the host environment (CPPS PROD) identified in the pre-requisites.
5. Agree to the terms and select **Install**.

*Once installed, the deployment pipelines configuration application appears in the list of installed apps.*

### Import Workshop Solution to Your Development Environment
1. Sign in to [Power Apps](https://make.powerapps.com), then select the development environment (CPPS DEV) identified in the pre-requisites.
2. Navigate to **Solutions**, then on the command bar, select **Import**.
3. On the **Import a solution** page, select **Browse** to locate the solution file downloaded from the workshop files.
4. Select **Next**, leave all default settings as is, and select **Import**.

*Your solution imports in the background and may take a few moments.*

### Set Up Deployment Environments for Pipeline
1. Once the deployment pipeline package installation is complete in the host environment, go to **Apps**, then play the **Deployment Pipeline Configuration** app.
2. Select **Environments** on the left pane, then select **New** to create the environment records in Dataverse:
   - **Name**: Enter the name for the environment record (e.g., CPPS DEV).
   - **Environment Type**: Select **Development Environment** for the source environment and **Target Environment** for the target environments.
   - **Environment Id**: Be sure to select the correct ID.
   - **Description**: Add some description.
3. Select **Save**.
4. Refresh the form, then verify **Validation Status** equals **Success**.
5. Repeat steps 2-4 until all three environments (CPPS DEV,CPPS UAT, CPPS PROD) are added as records.

### Configure the Deployment Pipeline
1. Select **Pipelines** on the left navigation pane of the **Deployment Pipeline Configuration** app, then select **New**:
   - **Name**: My First Custom Pipeline
   - **Description**: To be used during the CPPS 2025 ALM workshop
2. Within the **Linked Development Environments** grid, select **Add Existing Development Environment**, then associate the development environment (CPPS DEV).
3. Within the **Deployment Stages** grid, select **New Deployment Stage**.
4. Enter the details for the new stage, then select **Save & Close**:
   - **Name**: Deploy to UAT environment
   - **Description**: Deploy solution from DEV to UAT environment
   - **Target Deployment Environment ID**: Select the target environment (CPPS UAT)
5. Within the same **Deployment Stages** grid, select **New Deployment Stage** to add a new stage for PROD environment.
6. Enter the details for the new stage, then select **Save & Close**:
   - **Name**: Deploy to PROD environment
   - **Description**: Deploy solution from DEV to PROD environment
   - **Previous Deployment Stage**: Select the stage created in the previous steps - **Deploy to UAT**
   - **Target Deployment Environment ID**: Select the target environment (CPPS PROD)
   - **Pre-Deployment Step Required**: Select this box to later configure a cloud flow for deployment approvals

### Create Approvals for Deployment Using Power Automate Cloud Flow
1. Navigate to [Power Automate](https://make.powerautomate.com) and select the host environment for cloud flow development.
2. Select **Solutions** on the left pane and click **New Solution**.
3. Enter the details for the new solution, then select **Create**:
   - **Display Name**: Pipeline extension flows
   - **Name**: Leave as-is
   - **Publisher**: Select a publisher from the list
4. In the new solution, select **New > Automation > Cloud flow > Automated**.
5. Name your flow **Pre-Deployment Approval Flow**.
6. Search for **When an action is performed**, select this trigger, then click **Create**.
7. In the editor, configure the trigger action as follows:
   - **Catalog**: Microsoft Dataverse Common
   - **Category**: Power Platform Pipelines
   - **Table name**: (none)
   - **Action name**: OnPreDeploymentStarted
8. Select **New Step**.
9. Search for **Approval**, then select **Start and wait for an approval** and configure it as follows:
   - **Approval Type**: Approve/Reject - Everyone must approve
   - **Title**: Approve Deployment
   - **Assigned to**: Assign it to yourself
10. Select **New Step > Condition**.
11. Configure the condition to check if **Outcome** from approval is equal to **Approve**.
12. In the **If yes** branch, select **New Step**.


This guide helps you set up and configure Power Platform Pipelines for automated deployments, ensuring efficient ALM practices. 🚀
