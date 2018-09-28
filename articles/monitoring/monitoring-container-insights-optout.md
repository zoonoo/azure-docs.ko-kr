---
title: Azure Kubernetes Service 클러스터 모니터링을 중지하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Monitor를 사용하여 컨테이너에 대한 Azure AKS 클러스터의 모니터링을 중단하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: magoedte
ms.openlocfilehash: 2b989fbebe237e4e3746ef2f237193587173dfe4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963409"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-azure-monitor-for-containers"></a>컨테이너에 대한 AKS(Azure Kubernetes Service) Azure Monitor의 모니터링을 중지하는 방법

AKS 클러스터에 대한 모니터링을 사용하도록 설정한 후에 더 이상 모니터링하지 않기로 결정한 경우 Azure CLI 또는 PowerShell cmdlet **New-AzureRmResourceGroupDeployment**에서 제공된 Azure Resource Manager 템플릿을 사용하여 *옵트아웃(opt out)* 할 수 있습니다. 하나의 JSON 템플릿은 *옵트아웃(opt out)* 할 구성을 지정합니다. 다른 템플릿에는 클러스터가 배포된 AKS 클러스터 리소스 ID 및 리소스 그룹을 지정하도록 구성하는 매개 변수 값이 포함됩니다. 

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-template"></a>템플릿 만들기

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. 이 파일을 로컬 폴더에 **OptOutTemplate.json**으로 저장합니다.
3. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. 선택한 클러스터에 대한 **속성** 페이지에서 찾을 수 있는 AKS 클러스터 값을 사용하여 **aksResourceId** 및 **aksResourceLocation**의 값을 편집합니다.

    ![컨테이너 속성 페이지](./media/monitoring-container-health/container-properties-page.png)

    **속성** 페이지에서 **작업 영역 리소스 ID**도 복사합니다. 이 값은 나중에 Log Analytics 작업 영역을 삭제하려는 경우 필요합니다. Log Analytics 작업 영역을 삭제하는 작업은 이 단계에서는 수행되지 않습니다. 

5. 이 파일을 로컬 폴더에 **OptOutParam.json**으로 저장합니다.
6. 이제 이 템플릿을 배포할 수 있습니다. 

## <a name="remove-the-solution-using-azure-cli"></a>Azure CLI를 사용하여 솔루션 제거
Linux에서 Azure CLI로 다음 명령을 실행하여 솔루션을 제거하고 AKS 클러스터에서 구성을 정리합니다.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 다음과 비슷한 메시지가 반환됩니다.

```azurecli
ProvisioningState       : Succeeded
```

## <a name="remove-the-solution-using-powershell"></a>PowerShell을 사용하여 솔루션 제거

템플릿이 포함된 폴더에서 다음 PowerShell 명령을 실행하여 솔루션을 제거하고 AKS 클러스터에서 구성을 정리합니다.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 다음과 비슷한 메시지가 반환됩니다.

```powershell
ProvisioningState       : Succeeded
```

클러스터 모니터링을 지원하려는 목적으로만 작업 영역을 만들었으며 더 이상 필요하지 않은 경우 수동으로 삭제해야 합니다. 작업 영역을 삭제하는 방법을 모를 경우 [Azure Portal에서 Azure Log Analytics 작업 영역 삭제](../log-analytics/log-analytics-manage-del-workspace.md)를 참조하세요. 이전에 4단계에서 복사한 **작업 영역 리소스 ID**는 나중에 필요하므로 기억해두세요. 

