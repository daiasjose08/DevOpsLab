# Lab 2: Automate Deployments using Azure DevOps

## Workshop Pre-requisites  
- We will be using the same environments from Lab 1
- Three Power Platform environments to create a pipeline.   

  | Development | UAT       | PROD      |
  |------------|----------|----------|
  | CPPS DEV   | CPPS UAT | CPPS PROD |

- To separate our solution from the one used in Lab 1, we will import a new unmanaged solution in CPPS DEV

- Azure DevOps project set up (see Pre-Requisits)

## Workshop Solution  
📥 **Download the [solution file - ALM Azure DevOps Solution from /LabSolution/ALMAzureDevOpsSolution_1_0_0_1.zip](LabSolution/ALMAzureDevOpsSolution_1_0_0_1.zip) and save it on your local machine.**  

---

## Lab Instructions  

### 1️⃣ Prepare Azure DevOps - Install Power Platform Build Tools 
Microsoft has an official package of build and deploy tools for Power Platform called **Power Platform Build Tools**. They are not installed by default and have to be installed from the Visual Studio Marketplace.

1. Go to [Power Platform Build Tools](https://marketplace.visualstudio.com/items?itemName=microsoft-IsvExpTools.PowerPlatform-BuildTools).
2. Click on **Get it free**
![Create new repository button](images/devops_install1.png)  

3. Select your DevOps organization
![Create new repository button](images/devops_install2.png) 

4. After successfull installation click on **Proceed to organization**

---

### 2️⃣ Prepare Azure DevOps - Setup Service Connections
To interact with Dataverse environments we need to set up the authentication for Azure DevOps. Azure DevOps supports using Application user to access Dataverse via it's **Service Connections**. For this we will first create an Application in Entra Id and give its permissions in each Dataverse environment.

### Create Application in Entra Id

1. Go to [Azure Portal](https://portal.azure.com/).
2. Click on **Manage** under **Manage Microsoft Entra Id**
![Open Entra Id](images/devops_entra1.png) 

3. Click on **App registrations** and then on **New registration**
![Create new application](images/devops_entra2.png)  

4. Give it a meaningful name representing Azure DevOps pipeline and click **Register**
![Create new application dialog](images/devops_entra3.png) 

5. Save the **Directory / Tenant Id** and **Application Id** for later use
![Create new application dialog](images/devops_entra4.png) 

6. Click on **Secrets & Certificates** and on **New client secret**, in the opening dialog give it any name and click **Add**
![Create new application dialog](images/devops_entra5.png) 

7. Save the **Value** of the secret for later use (not the Secret Id!)
![Create new application dialog](images/devops_entra6.png) 

### Create Application Users in Dataverse

The following steps you have to repeat for all three environments (CPPS DEV / UAT / PROD), because we need to create the application user for Azure DevOps in each environment. This will create the link between Entra Id Application and Dataverse.

1. Go to [Power Platform Admin Portal / Environments](https://admin.powerplatform.microsoft.com/environments).
2. Click on the environment to get to its detail page
![Open environment](images/devops_entra7.png)  

3. Click on **See all** under **S2S Apps**
![See all S2S apps](images/devops_entra8.png) 

4. Click on **New app user**
![New App User](images/devops_entra9.png) 

5. Click on **Add an app** by either searching by your Entra Id Name or Application Id. Next select the business unit (start typing environment name if dropdown is not working) and assigned the **System Customizer** security role.
![Select app, business unit and security role](images/devops_entra10.png)  

6. Click on **Create** to create the application user in Dataverse
![Finish application user creation](images/devops_entra11.png) 

7. Repeat for all other environments

### Create Service Connections in Azure DevOps

The following steps you have to repeat for all three environments (CPPS DEV / UAT / PROD), because we need to create the Service Connections in Azure DevOps for each environment. This will create the link between Entra Id Application and Azure DevOps and Dataverse.

1. Go to [Azure DevOps](https://dev.azure.com/).
2. Click **Project Settings**, then **Service Connections** and then on **Create service connection**
![Navigate to Create Service Connection dialog](images/devops_entra12.png)  

3. Search for **Power Platform**, select it as option and click on **Next**
![Start Power Platform service connection dfialog](images/devops_entra13.png)   

4. Select **Applicaton Id and Secret** as Authentication method
5. Enter the URL of the Dataverse environment as **Server Url**
6. Enter the **Tenant Id** from your created Entra Id application
7. Enter the **Application Id** from your created Entra Id application
6. Enter the **Secret Value** from your created Entra Id application
7. Give it a meaningful **Service Connection Name** 
8. Select **Grant access to all pipelines** 
9. Click on **Save** to create the service connection  
![Power Platform service connection dialog](images/devops_entra14.png)  

10. Repeat for all other environments

---

### 3️⃣ Import Workshop Solution to Your Development Environment  
1. Sign in to [Power Apps](https://make.powerapps.com).
2. Select the **development environment (CPPS DEV)** from the prerequisites.
3. Navigate to **Solutions**, then select **Import**.
   ![import](images/install_ALM_solution1.jpg)

4. On the **Import a solution** page, click **Browse** and select the solution file downloaded earlier.
   ![import](images/install_ALM_solution2.png)  

5. Click **Next**, leave default settings, and select **Import**.

⏳ *Solution import may take a few moments.*  

---

### 4️⃣ Create Solution Repository in Azure DevOps
In Azure DevOps all deployments are based on repositories. This covers our need for a source code control systems as well. For this reason we create a repository per solution.

1. Sign in to [Azure DevOps](https://dev.azure.com/).
2. Select the project you created from the pre-requisites
3. Select **Repos**
4. Select the repository picker in the top navigation
5. Select **New repository**
![Create new repository button](images/devops_repo1.png)  

6. Enter the **name of the solution** as **Repository Name**. We do not use the environment name because the repository is independent from the environment.
7. Select **Create** to create the new repository
![Create new repository dialog](images/devops_repo2.png) 

8. Azure DevOps will navigate to the new repository after successful creation
![New repository in Azure DevOps](images/devops_repo3.png)  

---

### 6️⃣ Connect environment with repository
As a next step we want to check in our solution content to the newly created repository. This can be done via Pipelines as well, but Microsoft has a new feature in preview which makes this process a lot easier and is integrated in the make.powerapps.com UI. This setup will be done only for the DEV environment, because this will be the only environment where the solution will unmanaged.

1. Go to [make.powerapps.com](https://make.powerapps.com/) and select your DEV environment
2. Click on **Solutions** and on **Connect to Git**  
![Open Connect to Git](images/devops_connect0.png) 

3. Pick **Solution** for connection type, this enables you to have fine grained control about which solution from your environment you check in.
4. Pick your **Azure DevOps organization** and **Project** which you created in the pre-requists
5. Pick your created **repository** from the previous step
6. Use **solution** as root folder
![Open Connect to Git dialog](images/devops_connect1.png) 

7. Select the solution you want to check in and select the branch **main** and click on **Connect**
![Select solution](images/devops_connect2.png) 

8. The base connection is done, go to **Solutions** and click on the solution you want to check in
![Open solution details](images/devops_connect3.png) 

9. Go to the **Source Code (preview)**, check your changes and click on **Commit**
![Source control menu](images/devops_connect4.png) 

10. Add a **Comment** and click on **Commit**  
![Source control menu](images/devops_connect5.png) 

11. In youz Azure DevOps repository you will see a new commit and the solution files
![Repository with solution](images/devops_connect6.png) 

---

### 7️⃣ Create a local Azure DevOps agents
The free Azure DevOps instance is not having any hosted pipeline capacity anymore. You can either purchase that from Microsoft or you run a agent for the pipelines locally. This chapter will show the local setup.

1. Sign in to [Azure DevOps](https://dev.azure.com/).
2. Click **Project Settings**, then **Agent Pools** and then on **Add pool**
![Agent pool settings](images/devops_agent1.png) 

3. Select **Self Hosted** as pool type and select a simple name  
4. Click on **Grant permissions to all pipelines**
5. Click on **Create** to create the agent pool
![Agent pool creation](images/devops_agent2.png) 

6. Select the newly created Agent Pool by clicking on its name
7. Click on **New Agent**
![Add agent](images/devops_agent3.png) 

8. Click on **Download** to download the agent files (can take a while)
![Download agent](images/devops_agent4.png) 

9. Based on your operation system follow the steps described.
10. For Windows start PowerShell with Admin permission and after the download is finished use the following commands

- cd C:\
- mkdir agent ; cd agent
- Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$HOME\Downloads\vsts-agent-win-x64-4.252.0.zip", "$PWD")
- .\config.cmd

This starts the configuration process for the local agent. 

- Enter your Azure DevOps URL in this format **https://dev.azure.com/nckrdev** and press enter
- Press enter to select **PAT** as authentication format
- Go back to Azure DevOps and open the **Personal Access Tokens**
![Personal access tokens](images/devops_agent5.png) 

- Click on **New Token**
![Open new token](images/devops_agent6.png) 

- Enter a simple **Name** and select **Full access** as scope
- Click on **Create** to create your token
![Create new token](images/devops_agent7.png)

- Copy the shown token 
- Go back to PowerShell and enter the token as Personal Access Token
![enter new token](images/devops_agent9.png)

- For **Enter Agent Pool** enter the name you picked in step #3
- For **Agent Name** press enter to use your computer name
- For **Work Folder** press enter to user "_work"
- For **Run agent as service** enter "Y" and press enter
- For **User Account for service** press enter
- For the last question press enter for "N"
![agent config](images/devops_agent10.png)

- Your agent is running, and will show up in Azure DevOps in your **Agent Pools** under **Agents**
![local agent running](images/devops_agent11.png)

---

### 8️⃣ Set up Build Pipeline in Azure DevOps
To automate our deployments we will create a pipeline which will trigger on each commit, export the solution as a managed solution from DEV and import the solution to TEST.

1. Go to your repository on the top level and click on **Set up build**
![enter new token](images/devops_build1.png)

2. Select **Starter Pipeline** 
![starter pipeline](images/devops_build2.png)

3. Remove the comments on top and the the parts below "steps:"
4. Change the line under "pool:" to **name: Your Agent name you pick in the previous chapter**
![initialize script](images/devops_build3.png)

5. To add steps to the build script click on **Show Assistant**
![show assistant](images/devops_build31.png)

6. Search for **Power Platform Tool Installer** and select **Power Platform Tool Installer**. This will install the needed tools on your local agent.  Use the default settings in the next dialog and click on **Add** to add it as a step to the build script  
![show assistant](images/devops_build10.png)

7. Search for **Export Solution** and select **Power Platform Export Solution**  
![show assistant](images/devops_build4.png)

8. Select **Service Principial** for authentication type and pick the service connection your created for your **DEV** environment in chapter #2
9. Enter the Dataverse URL from your **DEV** environment
10. Enter the Name of the solution you want to export **ALMAzureDevOpsSolution**
11. Use a meaningful name for the created zip **ALMAzureDevOpsSolution_managed.zip**
12. Select **Export as managed solution**
13. Click on **Add** to add as a step to the build script  
![show assistant](images/devops_build7.png)

14. Your script looks like this now  
![script after export step](images/devops_build11.png)

15. Open the assistant again and search for the **Power Platform Import Solution** step and click on it  
![script after export step](images/devops_build7.png)

16. Select **Service Principial** for authentication type and pick the service connection your created for your **TEST** environment in chapter #2
17. Enter the Dataverse URL from your **TEST** environment
18. Enter the Name of the solution you want to export **ALMAzureDevOpsSolution**
19. Use the variable for the build directory together with the solution file name from the export step: **$(Build.SourcesDirectory)/output/ALMAzureDevOpsSolution_managed.zip**  
20. Click on **Add** to add as a step to the build script  
![import step](images/devops_build8.png)

21. Your script looks like this now, click on **Save and run**, use the default commit message and click on **Save** to start the test.
![final script](images/devops_build9.png)



---

### 9️⃣ Trigger build from environment
After changes are done we want to trigger the deployment pipeline. Follow these steps:

1. Make change in solution. Add Yes/No choice field "Would you recommend this workshop to a friend?" and add the field to form.
![Add new column](images/devops_trigger2.png) 

2. Increase solution version by navigating to the solution overview
![Increase solution version](images/devops_trigger2.png) 

3. Go to the **Source Code (preview)**, check your changes and click on **Commit**
![Source control menu](images/devops_trigger3.png) 

4. Add a **Comment** and click on **Commit**  
![Source control menu](images/devops_trigger4.png) 

5. The pipeline is set to trigger on Commits so you will see a new pipeline run
![Source control menu](images/devops_trigger5.png) 

---

## 🚀 Summary  
✅ Installed **Power Platform Build Tools**  
✅ Configured **DevOps Service Connections**  
✅ Created a **Solution repository**  
✅ Implemented **Build and Deployment pipeline**  
✅ Ran **Pipeline** to deploy solutions  

Happy deploying! 🎯🚀  
