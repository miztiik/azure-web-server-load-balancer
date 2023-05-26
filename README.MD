# Azure Network Load Balancer for Web Servers

A simple demo to run a network load balancer for load balancing traffic to web servers running Nginx on Azure VM.

## 🎯 Solution

![Miztiik Automation - Azure Network Load Balancer for Web Servers](images/miztiik_architecture_azure_load_balancer_nginx_on_azure_vm_001.png)

1. ## 🧰 Prerequisites

   This demo, along with its instructions, scripts, and Bicep template, has been specifically designed to be executed in the `northeurope` region. However, with minimal modifications, you can also try running it in other regions of your choice (the specific steps for doing so are not covered in this context)

   - 🛠 Azure CLI Installed & Configured - [Get help here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
   - 🛠 Azure Function Core Tools - [Get help here](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cwindows%2Ccsharp%2Cportal%2Cbash#install-the-azure-functions-core-tools)
   - 🛠 Bicep Installed & Configured - [Get help here](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/install)
     - 🛠 [Optional] VS Code & Bicep Extenstions - [Get help here](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/install#vs-code-and-bicep-extension)
   - `jq` - [Get help here](https://stedolan.github.io/jq/download/)
   - `bash` or git bash - [Get help here](https://git-scm.com/downloads)

1. ## ⚙️ Setting up the environment

   - Get the application code

     ```bash
     git clone https://github.com/miztiik/azure-web-server-load-balancer.git
     cd azure-web-server-load-balancer
     ```

1. ## 🚀 Prepare the local environment

   Ensure you have jq, Azure Cli and bicep working

   ```bash
   jq --version
   func --version
   bicep --version
   bash --version
   az account show
   ```

1. ## 🚀 Deploying the Solution

   - **Stack: Main Bicep**
     The params required for the modules are in `params.json`. Modify them as needed. The helper deployment script `deploy.sh` will deploy the `main.bicep` file. This will create the following resoureces
     - Resource Group(RG)
     - VNet, Subnet & Virtual Machine
     - Virtual Machine(Ubuntu) - `2` - Defined in `params.json` named `vmCount`
        - Bootstrapped with custom libs using `userData` script - Installs Nginx
        - Install Azure Monitoring Agent
     - User Managed Identity - Identity attached to the VM with the following permissions,
        - Monitoring Metrics Publisher
     - Azure Load Balancer
       - Backend Pool
         - Attach VMs to the backend pool
       - Health Probe
       - Load Balancer Rule
       - Load Balancer Frontend IP
       - Load Balancer Outbound Rule

     **Note** - I hacked the template from another repo of mine, so you will see some unused resources(log analytics workspace/queues etc.,). _You can safely ignore them, But do remember to clean up your Resource Group to avoid unnecessary costs._

    ```bash
    # make deploy
    sh deployment_scripts/deploy.sh
    ```

     After successfully deploying the stack, Check the `Resource Groups/Deployments` section for the resources.

1. ## 🔬 Testing the solution

   - You can find the load balancer public IP address in the resource blade. You can also get it from the Azure Portal.

      ```bash
      WEB_SRV_IP="http://20.107.191.133/"
      for i in range{1..10000}; do curl ${WEB_SRV_IP}; sleep 1; done
      ```

      You should see an output like this,

      ```text
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-1</b> <b>10.0.0.5</b> on Fri May 26 22:07:28 UTC 2023 
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-0</b> <b>10.0.0.4</b> on Fri May 26 22:07:25 UTC 2023 
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-1</b> <b>10.0.0.5</b> on Fri May 26 22:07:28 UTC 2023 
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-0</b> <b>10.0.0.4</b> on Fri May 26 22:07:25 UTC 2023 
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-1</b> <b>10.0.0.5</b> on Fri May 26 22:07:28 UTC 2023 
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-0</b> <b>10.0.0.4</b> on Fri May 26 22:07:25 UTC 2023 
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-0</b> <b>10.0.0.4</b> on Fri May 26 22:07:25 UTC 2023 
       Hello World from miztiik-automation-vm <b>m-web-srv-web-server-lb-004-1</b> <b>10.0.0.5</b> on Fri May 26 22:07:28 UTC 2023 
      ```

1. ## 📒 Conclusion

   In this demonstration, we have shown how to bootstrap a VM using `userData` to install Nginx and add load balancer in the front using Bicep. We have also shown how to use `User Managed Identity` to access Azure resources from the VM.
  
1. ## 🧹 CleanUp

   If you want to destroy all the resources created by the stack, Execute the below command to delete the stack, or _you can delete the stack from console as well_

   - Resources created during [Deploying The Solution](#-deploying-the-solution)
   - _Any other custom resources, you have created for this demo_

   ```bash
   # Delete from resource group
   az group delete --name Miztiik_Enterprises_xxx --yes
   # Follow any on-screen prompt
   ```

   This is not an exhaustive list, please carry out other necessary steps as maybe applicable to your needs.

## 📌 Who is using this

This repository aims to show how to Bicep to new developers, Solution Architects & Ops Engineers in Azure.

### 💡 Help/Suggestions or 🐛 Bugs

Thank you for your interest in contributing to our project. Whether it is a bug report, new feature, correction, or additional documentation or solutions, we greatly value feedback and contributions from our community. [Start here](/issues)

### 👋 Buy me a coffee

[![ko-fi](https://www.ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/Q5Q41QDGK) Buy me a [coffee ☕][900].

### 📚 References

1. [Azure Docs: Just In Time Access][10]

### 🏷️ Metadata

![miztiik-success-green](https://img.shields.io/badge/Miztiik:Automation:Level-100-green)

**Level**: 100

[10]: https://learn.microsoft.com/en-us/azure/defender-for-cloud/just-in-time-access-usage

[100]: https://www.udemy.com/course/aws-cloud-security/?referralCode=B7F1B6C78B45ADAF77A9
[101]: https://www.udemy.com/course/aws-cloud-security-proactive-way/?referralCode=71DC542AD4481309A441
[102]: https://www.udemy.com/course/aws-cloud-development-kit-from-beginner-to-professional/?referralCode=E15D7FB64E417C547579
[103]: https://www.udemy.com/course/aws-cloudformation-basics?referralCode=93AD3B1530BC871093D6
[899]: https://www.udemy.com/user/n-kumar/
[900]: https://ko-fi.com/miztiik
[901]: https://ko-fi.com/Q5Q41QDGK