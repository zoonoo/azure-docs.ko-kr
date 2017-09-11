---
title: "Azure 가상 컴퓨터 확장 집합에 앱 배포 | Microsoft Docs"
description: "Azure Resource Manager 템플릿을 사용하여 가상 컴퓨터 확장 집합에 간단한 자동 크기 조정 응용 프로그램을 배포하는 방법을 알아봅니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.contentlocale: ko-kr
ms.lasthandoff: 08/25/2017

---

# <a name="deploy-an-autoscaling-app-using-a-template"></a>템플릿을 사용하여 자동 크기 조정 앱 배포

[Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)은 관련된 리소스 그룹을 배포하는 유용한 방법입니다. 이 자습서는 [간단한 확장 집합 배포](virtual-machine-scale-sets-mvss-start.md)를 토대로 구축되었으며 Azure Resource Manager 템플릿을 사용하여 확장 집합에 간단한 자동 크기 조정 응용 프로그램을 배포하는 방법을 설명합니다.  PowerShell, CLI 또는 포털을 사용하여 크기 자동 조정도 설정할 수 있습니다. 자세한 내용은 [크기 자동 조정 개요](virtual-machine-scale-sets-autoscale-overview.md)를 참조하세요.

## <a name="two-quickstart-templates"></a>2개의 빠른 시작 템플릿
확장 집합을 배포할 때 [VM 확장](../virtual-machines/virtual-machines-windows-extensions-features.md)을 사용하여 플랫폼 이미지에 새 소프트웨어를 설치할 수 있습니다. VM 확장은 Azure Virtual Machines에 배포 후 구성 및 Automation 작업(예: 앱 배포)을 제공하는 작은 응용 프로그램입니다. [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)에는 VM 확장을 사용하여 확장 집합에 자동 크기 조정 응용 프로그램을 배포하는 방법을 보여 주는 2개의 다른 샘플 템플릿이 제공됩니다.

### <a name="python-http-server-on-linux"></a>Linux의 Python HTTP 서버
[Linux의 Python HTTP 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) 샘플 템플릿은 Linux 확장 집합에서 실행되는 간단한 자동 크기 조정 응용 프로그램을 배포합니다.  Python 웹 프레임워크인 [Bottle](http://bottlepy.org/docs/dev/)과 간단한 HTTP 서버가 사용자 지정 스크립트를 VM 확장을 사용하여 확장 집합의 각 VM에 배포됩니다. 모든 VM의 평균 CPU 사용률이 60%보다 클 때 확장 집합이 확장되고, 평균 CPU 사용률이 30% 미만일 때 확장 집합이 축소됩니다.

확장 집합 리소스 외에, *azuredeploy.json* 샘플 템플릿은 가상 네트워크, 공용 IP 주소, 부하 분산 장치 및 자동 크기 조정 설정 리소스도 선언합니다.  템플릿에서 이러한 리소스를 만드는 방법에 대한 자세한 내용은 [자동 크기 조정 기능이 포함된 Linux 확장 집합](virtual-machine-scale-sets-linux-autoscale.md)을 참조하세요.

*azuredeploy.json* 템플릿에서 `Microsoft.Compute/virtualMachineScaleSets` 리소스의 `extensionProfile` 속성은 사용자 지정 스크립트 확장을 지정합니다. `fileUris`는 스크립트 위치를 지정합니다. 이 경우 간단한 HTTP 서버를 정의하는 *workserver.py*와 Bottle을 설치하고 HTTP 서버를 시작하는 *installserver.sh*의 2개 파일이 사용됩니다. `commandToExecute`는 확장 집합이 배포된 후에 실행할 명령을 지정합니다.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Windows의 ASP.NET MVC 응용 프로그램
[Windows의 ASP.NET MVC 응용 프로그램](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 샘플 템플릿은 Windows 확장 집합의 IIS에서 실행되는 간단한 ASP.NET MVC 앱을 배포합니다.  IIS 및 MVC 앱은 [PowerShell DSC(필요한 상태 구성)](virtual-machine-scale-sets-dsc.md) VM 확장을 사용하여 배포됩니다.  확장 집합은 5분 동안 CPU 사용률이 50%보다 큰 경우 확장됩니다(한 번에 VM 인스턴스에서). 

확장 집합 리소스 외에, *azuredeploy.json* 샘플 템플릿은 가상 네트워크, 공용 IP 주소, 부하 분산 장치 및 자동 크기 조정 설정 리소스도 선언합니다. 이 템플릿은 응용 프로그램 업그레이드도 보여 줍니다.  템플릿에서 이러한 리소스를 만드는 방법에 대한 자세한 내용은 [자동 크기 조정 기능이 포함된 WIndows 확장 집합](virtual-machine-scale-sets-windows-autoscale.md)을 참조하세요.

*azuredeploy.json* 템플릿에서 `Microsoft.Compute/virtualMachineScaleSets` 리소스의 `extensionProfile` 속성은 WebDeploy 패키지에서 IIS 및 기본 웹앱을 설치하는 [DSC(필요한 상태 구성)](virtual-machine-scale-sets-dsc.md) 확장을 지정합니다.  *IISInstall.ps1* 스크립트는 가상 컴퓨터에 IIS를 설치하며 *DSC* 폴더에 포함되어 있습니다.  MVC 웹앱은 *WebDeploy* 폴더에 있습니다.  설치 스크립트 및 웹앱 경로는 *azuredeploy.parameters.json* 파일의 `powershelldscZip` 및 `webDeployPackage` 매개 변수에 정의됩니다. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>템플릿 배포
[Linux에서 Python HTTP 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) 또는 [Windows에서 ASP.NET MVC 응용 프로그램](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 템플릿을 배포하는 가장 간단한 방법은 GitHub의 추가 정보 파일에 있는 **Azure에 배포** 단추를 사용하는 것입니다.  또한 PowerShell 또는 Azure CLI를 사용하여 샘플 템플릿을 배포할 수도 있습니다.

### <a name="powershell"></a>PowerShell
GitHub 리포지토리에 있는 [Linux의 Python HTTP 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) 또는 [Windows의 ASP.NET MVC 응용 프로그램](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 파일을 로컬 컴퓨터의 폴더에 복사합니다.  *azuredeploy.parameters.json* 파일을 열고 `vmssName`, `adminUsername` 및 `adminPassword` 매개 변수의 기본값을 업데이트합니다. 다음 PowerShell 스크립트를 *azuredeploy.json* 템플릿과 동일한 폴더에서 *deploy.ps1*로 저장합니다. 샘플 템플릿을 배포하려면 PowerShell 명령 창에서 *deploy.ps1* 스크립트를 실행합니다.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

### <a name="azure-cli"></a>Azure CLI
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

