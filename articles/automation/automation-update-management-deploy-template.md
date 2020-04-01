---
title: Azure 리소스 관리자 템플릿을 사용하여 업데이트 관리 온보로 이동 | 마이크로 소프트 문서
description: Azure 리소스 관리자 템플릿을 사용하여 Azure 자동화 업데이트 관리 솔루션을 온보덕수로 사용할 수 있습니다.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: e69f3d7350d0da9f364983eae0935532b576bd76
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411467"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하는 온보드 업데이트 관리 솔루션

[Azure 리소스 관리자 템플릿을](../azure-resource-manager/templates/template-syntax.md) 사용하여 리소스 그룹에서 Azure 자동화 업데이트 관리 솔루션을 활성화할 수 있습니다. 이 문서에서는 다음을 자동화하는 샘플 템플릿을 제공합니다.

* Azure 모니터 로그 분석 작업 영역 만들기.
* Azure 자동화 계정 만들기.
* 아직 연결되지 않은 경우 자동화 계정을 Log Analytics 작업 영역에 연결합니다.
* Azure 자동화 업데이트 관리 솔루션 온보드

템플릿은 하나 이상의 Azure 또는 비 Azure VM의 온보딩을 자동화하지 않습니다.

구독의 지원되는 지역에 Log Analytics 작업 영역 및 자동화 계정이 이미 배포된 경우 해당 계정이 연결되지 않고 작업 영역에 업데이트 관리 솔루션이 아직 배포되지 않은 경우 이 템플릿을 사용하여 링크를 성공적으로 만들고 업데이트 관리 솔루션을 배포합니다. 

## <a name="api-versions"></a>API 버전

다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 유형 | API 버전 |
|:---|:---|:---|
| 작업 영역 | workspaces | 2017-03-15-preview |
| Automation 계정 | Automation | 2015-10-31 | 
| 해결 방법 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>템플릿을 사용하기 전에

로컬에서 PowerShell을 설치하고 사용하도록 선택한 경우 이 문서에는 Azure PowerShell Az 모듈이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다. Azure PowerShell을 사용하면 배포에서 [New-AzResourceGroup 배포를](/powershell/module/az.resources/new-azresourcegroupdeployment)사용합니다.

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.1.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하십시오. Azure CLI를 사용하는 이 배포는 [az 그룹 배포 create를](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)사용합니다. 

JSON 템플릿은 다음과 같은 메시지를 표시하도록 구성됩니다.

* 작업 영역의 이름
* 작업 영역을 만들 영역
* 자동화 계정의 이름
* 계정을 만들 영역은

JSON 템플릿은 사용자 환경에서 표준 구성으로 사용될 수 있는 다른 매개 변수에 대한 기본값을 지정합니다. 조직의 공유 액세스를 위해 Azure 저장소 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [리소스 관리자 템플릿 및 Azure CLI를 사용 하 여 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조 합니다.

템플릿의 다음 매개 변수는 Log Analytics 작업 영역에 대한 기본값으로 설정됩니다.

* sku - 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값
* 데이터 보존 - 기본값30일로
* 용량 예약 - 기본값100GB

>[!WARNING]
>새 2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성할 때 유효한 유일한 Log Analytics 가격 책정 계층은 **PerGB2018**입니다.
>

>[!NOTE]
>이 템플릿을 사용하기 전에 액세스 제어 모드, 가격 책정 계층, 보존 및 용량 예약 수준과 같은 작업 영역 구성 옵션을 완전히 이해하려면 [추가 세부 정보를](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) 검토합니다. Azure Monitor 로그를 새로 접하고 작업 영역을 이미 배포하지 않은 경우 [작업 영역 디자인](../azure-monitor/platform/design-logs-deployment.md) 지침을 검토하여 액세스 제어 및 조직에 권장하는 디자인 구현 전략에 대해 알아봐야 합니다.

## <a name="deploy-template"></a>템플릿 배포

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. 요구 사항을 충족하도록 템플릿을 편집합니다. 매개 [변수를](../azure-resource-manager/templates/parameter-files.md) 인라인 값으로 전달하는 대신 Resource Manager 매개 변수 파일을 만드는 것이 좋습니다.

3. 이 파일을 deployUMSolutiontemplate.json으로 로컬 폴더에 저장합니다.

4. 이제 이 템플릿을 배포할 수 있습니다. PowerShell 또는 Azure CLI를 사용할 수 있습니다. 작업 영역 및 자동화 계정 이름을 묻는 메시지가 표시되면 모든 Azure 구독에서 전역적으로 고유한 이름을 제공합니다.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

    ![배포가 완료되었을 때 결과 예](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>다음 단계

업데이트 관리 솔루션을 배포한 후 VM을 사용하여 관리를 지원하고, 업데이트 평가를 검토하고, 업데이트를 배포하여 규정을 준수할 수 있습니다.

- 하나 이상의 Azure 컴퓨터에 대한 [Azure 자동화 계정과](automation-onboard-solutions-from-automation-account.md) 비 Azure 컴퓨터에 대해 수동으로.

- Azure 포털의 가상 시스템 페이지에서 단일 Azure VM의 경우 이 시나리오는 [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) 및 [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM에서 사용할 수 있습니다.

- Azure 포털의 **가상 시스템** 페이지에서 선택하여 여러 [Azure VM의](manage-update-multi.md) 경우 