# 1. Register application in Azure AD

To access the Dataverse tables that you will import in step 2, you will first need to register an application an Azure AD. 

- Open Azure cloud shell at [shell.azure.com](https://shell.azure.com)
> This guide assumes the use of a PowerShell environment.
- Clone the git repository to Azure Cloud Shell `git clone https://github.com/ProvisionGenie/ProvisionGenie.git`
- Change the working directory to the `ProvisionGenie/Deployment/Scripts` folder
    - `cd ./ProvisionGenie/Deployment/Scripts`
- Run the script 