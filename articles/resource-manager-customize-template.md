<properties
	pageTitle="내보낸 Resource Manager 템플릿 사용자 지정 | Microsoft Azure"
	description="내보낸 Azure Resource Manager 템플릿에 매개 변수를 추가하고 Azure PowerShell 또는 Azure CLI를 통해 다시 배포합니다."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/10/2016"
	ms.author="tomfitz"/>

# 내보낸 Azure Resource Manager 템플릿 사용자 지정

이 문서에서는 내보낸 템플릿을 수정하여 추가 값을 매개 변수로 전달하는 방법을 보여 줍니다. 이는 [Resource Manager 템플릿 내보내기](resource-manager-export-template.md) 문서에서 수행한 단계를 기준으로 하지만 반드시 해당 문서를 먼저 완료해야 하는 것은 아닙니다. 이 문서에서 필요한 템플릿과 스크립트를 찾을 수 있습니다.

## 내보낸 템플릿 보기

[Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 완료한 경우 다운로드한 템플릿을 엽니다. 템플릿 이름은 **template.json**입니다. Visual Studio 또는 Visual Code가 있는 경우 각각을 사용하여 템플릿을 편집할 수 있습니다. 그렇지 않을 경우 JSON 편집기 또는 텍스트 편집기를 사용할 수 있습니다.

이전 연습을 완료하지 않은 경우 **template.json**이라는 파일을 만들고 내보낸 템플릿에서 파일에 다음 콘텐츠를 추가합니다.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

template.json 템플릿은 모든 배포에 대해 동일한 주소 접두사와 동일한 서브넷 접두사를 사용하는 가상 네트워크가 구축된 동일 지역에 동일한 유형의 저장소 계정을 만들려는 경우에만 올바르게 작동합니다. 하지만 Resource Manager는 훨씬 더 유연하게 템플릿을 배포할 수 있도록 옵션을 제공합니다. 예를 들어 배포 중에 만들 저장소 계정 유형 또는 가상 네트워크 주소 접두사 및 서브넷 접두사에 사용할 값을 지정할 수 있습니다.

## 템플릿 사용자 지정

이 섹션에서는 다른 환경에 이러한 리소스를 배포할 때 템플릿을 다시 사용할 수 있도록 내보낸 템플릿에 매개 변수를 추가합니다. 또한 템플릿을 배포할 때 오류 발생 가능성을 줄이기 위해 템플릿에 몇 가지 기능을 추가할 수도 있습니다. 더 이상 저장소 계정에 대해 고유한 이름을 고민하지 않아도 됩니다. 대신 템플릿이 고유한 이름을 생성합니다. 저장소 계정 유형에 지정할 수 있는 값을 유효한 옵션으로만 제한합니다.

1. 배포 중에 지정하려는 값을 전달할 수 있도록 하려면 **매개 변수** 섹션을 다음 매개 변수 정의로 바꿉니다. **storageAccount\_accountType**에 대한 **allowedValues** 값을 확인합니다. 실수로 잘못된 값을 입력한 경우 해당 오류는 배포가 시작되기 전에 인식됩니다. 또한 저장소 계정 이름의 접두사만 입력해야 하며 접두사는 11자로 제한됩니다. 접두사를 11자로 제한하면 전체 이름이 저장소 계정의 최대 문자 수를 초과하지 않게 됩니다. 접두사를 사용하면 저장소 계정에 대한 명명 규칙을 적용할 수 있습니다. 다음 단계에서 고유한 이름을 만드는 방법을 확인할 수 있습니다.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. 현재 템플릿의 **변수** 섹션이 비어 있습니다. 이 섹션을 다음 코드로 교체합니다. **변수** 섹션에서는 템플릿 작성자로서 나머지 템플릿의 구문을 간소화하는 값을 만들 수 있습니다. **storageAccount\_name** 변수는 매개 변수의 접두사를 리소스 그룹 식별자를 기준으로 생성되는 고유 문자열로 자릅니다.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. 리소스 정의의 매개 변수와 변수를 사용하려면 **리소스** 섹션을 다음 정의로 바꿉니다. 리소스 속성에 할당된 값을 제외하고 리소스 정의에서 실제로 변경된 사항은 거의 없습니다. 속성은 내보낸 템플릿의 속성과 정확하게 동일합니다. 하드 코드된 값 대신 매개 변수 값에 속성을 할당하기만 하면 됩니다. 리소스 위치는 **resourceGroup().location** 식을 통한 리소스 그룹과 동일한 위치를 사용하도록 설정됩니다. 저장소 계정 이름에 대해 만든 변수는 **변수** 식을 통해 참조됩니다.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## 매개 변수 파일 수정

다운로드한 매개 변수 파일은 더 이상 템플릿의 매개 변수와 일치하지 않습니다. 매개 변수 파일을 사용하지 않아도 되지만 사용하면 환경을 다시 배포할 경우 프로세스를 간소화할 수 있습니다. 템플릿에서 다양한 매개 변수에 대해 정의한 기본값을 사용하므로 매개 변수 파일은 두 개 값만 필요합니다.

parameters.json 파일의 내용을 아래 코드로 바꿉니다.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

업데이트된 매개 변수 파일은 기본값이 없는 매개 변수의 값만 제공합니다. 기본값과 다른 값을 원할 경우에만 다른 매개 변수의 값을 제공할 수 있습니다.

## 템플릿 배포

Azure PowerShell 또는 Azure CLI(명령줄 인터페이스)를 사용하여 사용자 지정된 템플릿과 매개 변수 파일을 배포할 수 있습니다. 필요할 경우 [Azure PowerShell](powershell-install-configure.md) 또는 [Azure CLI](xplat-cli-install.md)를 설치합니다. 원본 템플릿을 내보낼 때 템플릿과 함께 다운로드한 스크립트를 사용하거나 고유 스크립트를 작성하여 템플릿을 배포할 수 있습니다. 두 옵션은 이 문서에 나와 있습니다.

2. 사용자 고유 스크립트를 사용하여 배포하려면 다음 중 하나를 사용합니다.

     PowerShell의 경우 다음을 실행합니다.

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Azure CLI의 경우 다음을 실행합니다.

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. 내보낸 템플릿과 스크립트를 다운로드한 경우 **deploy.ps1** 파일(PowerShell) 또는 **deploy.sh** 파일(Azure CLI)을 검색합니다.

     PowerShell의 경우 다음을 실행합니다.

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Azure CLI의 경우 다음을 실행합니다.

        .\deploy.sh

## 다음 단계

- [Resource Manager 템플릿 연습](resource-manager-template-walkthrough.md)은 더 복잡한 솔루션의 템플릿을 만들어 이 문서에서 학습한 내용을 바탕으로 자세한 내용을 배웁니다. 이 연습을 통해 사용 가능한 리소스와 제공할 값을 결정하는 방법을 이해할 수 있습니다.
- PowerShell을 통해 템플릿을 내보내는 방법을 알아보려면 [Azure Resource Manager에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
- Azure CLI를 통해 템플릿을 내보내는 방법은 [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.
- 템플릿 구조를 정하는 방법은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

<!---HONumber=AcomDC_0518_2016-->