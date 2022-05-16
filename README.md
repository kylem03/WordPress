# WordPress

instructions

this code is a bicep file that you need to upload into the azure CLI console.
once you upload that code you need to paste this command into the powershell window with your information inserted into it. 

New-AzResourceGroupDeployment -Name [Deployment Name] -ResourceGroupName [Resource Group Name] -TemplateFile [Path to File] -c

it will ask you if you want to deploy all or no, put "A" in the line and hit enter, it will take awhile to work. eventually it will go through.
once that happens, in your resource group the website app service should have been created. 
```
param sku string = 'B1'
param repoUrl string = 'https://github.com/azureappserviceoss/wordpress-azure'
param branch string = 'master'
param location string = resourceGroup().location
var hostingPlanName_var = '${uniqueString(resourceGroup().id)}hostingplan'
var siteName_var = '${uniqueString(resourceGroup().id)}website'
resource hostingPlanName 'Microsoft.Web/serverfarms@2022-03-01' = {
  sku: {
    name: sku
    capacity: 1
  }
  name: hostingPlanName_var
  location: location
  properties: {}
}
resource siteName 'Microsoft.Web/sites@2022-03-01' = {
  name: siteName_var
  location: location
  properties: {
    serverFarmId: hostingPlanName.id
    siteConfig: {
      localMySqlEnabled: true
      appSettings: [
        {
          name: 'WEBSITE_MYSQL_ENABLED'
          value: '1'
        }
        {
          name: 'WEBSITE_MYSQL_GENERAL_LOG'
          value: '0'
        }
        {
          name: 'WEBSITE_MYSQL_SLOW_QUERY_LOG'
          value: '0'
        }
        {
          name: 'WEBSITE_MYSQL_ARGUMENTS'
          value: '--max_allowed_packet=16M'
        }
      ]
    }
  }
}
resource siteName_web 'Microsoft.Web/sites/sourcecontrols@2022-03-01' = {
  parent: siteName
  name: 'web'
  properties: {
    repoUrl: repoUrl
    branch: branch
    isManualIntegration: true
  }
}
resource Microsoft_Web_sites_config_siteName_web 'Microsoft.Web/sites/config@2022-03-01' = {
  parent: siteName
  name: 'web'
  properties: {
    phpVersion: '7.0'
  }
}


```
New-AzResourceGroupDeployment -Name [Deployment Name] -ResourceGroupName [Resource Group Name] -TemplateFile [Path to File] -c
