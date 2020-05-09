---
title: Azure Resource Manager 템플릿을 사용 하 여 Automation 계정 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Automation 계정을 만들 수 있습니다.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 949b07a16b2c2b08891d721e46948481cfe572b2
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996110"
---
# <a name="create-an-automation-account-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 Automation 계정 만들기

[Azure Resource Manager 템플릿을](../azure-resource-manager/templates/template-syntax.md) 사용 하 여 리소스 그룹에 Azure Automation 계정을 만들 수 있습니다. 이 문서에서는 다음을 수행 하는 샘플 템플릿을 제공 합니다.

* Azure Monitor Log Analytics 작업 영역 만들기를 자동화 합니다.
* Azure Automation 계정 만들기를 자동화 합니다.
* Automation 계정을 Log Analytics 작업 영역에 연결 합니다.

템플릿은 Azure 또는 비 Azure virtual machines를 자동으로 사용 하도록 설정 하지 않습니다. 

>[!NOTE]
>Azure Resource Manager 템플릿을 사용 하는 경우 Automation 실행 계정 만들기가 지원 되지 않습니다. 포털에서 또는 PowerShell을 사용 하 여 실행 계정을 수동으로 만들려면 [실행 계정 관리](manage-runas-account.md)를 참조 하세요.

## <a name="api-versions"></a>API 버전

다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 유형 | API 버전 |
|:---|:---|:---|
| 작업 영역 | workspaces | 2017-03-15-preview |
| Automation 계정 | Automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>템플릿을 사용 하기 전에

PowerShell을 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에는 Azure PowerShell Az 모듈이 필요 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다. PowerShell을 사용 하는 경우 배포는 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용 합니다.

Azure CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에서는 2.1.0 이상 버전을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. Azure CLI에서이 배포는 [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)를 사용 합니다. 

JSON 템플릿이 사용자에 게 묻는 메시지를 표시 하도록 구성 되어 있습니다.

* 작업 영역의 이름입니다.
* 작업 영역을 만들 지역입니다.
* Automation 계정의 이름입니다.
* 계정을 만들 지역입니다.

템플릿의 다음 매개 변수는 Log Analytics 작업 영역에 대 한 기본값으로 설정 됩니다.

* *sku* 는 2018 년 4 월 가격 책정 모델에서 릴리스된 GB 당 가격 책정 계층으로 설정 됩니다.
* *Dataretention* 기본값은 30 일입니다.
* *capacityReservationLevel* 기본값은 100입니다.

>[!WARNING]
>4 월 2018 가격 책정 모델에 옵트인 한 구독에서 Log Analytics 작업 영역을 만들거나 구성 하려면 유효한 Log Analytics 가격 책정 계층만 *PerGB2018*합니다.
>

JSON 템플릿은 사용자 환경에서 표준 구성으로 사용 될 수 있는 다른 매개 변수에 대 한 기본값을 지정 합니다. 조직에서 공유 액세스를 위해 Azure storage 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대 한 자세한 내용은 [리소스 관리자 템플릿을 사용 하 여 리소스 배포 및 Azure CLI](../azure-resource-manager/templates/deploy-cli.md)를 참조 하세요.

Azure Automation 및 Azure Monitor를 처음 접하는 경우 다음 구성 정보를 이해 하는 것이 중요 합니다. 새 자동화 계정에 연결 된 Log Analytics 작업 영역을 만들고, 구성 하 고, 사용 하려고 할 때 오류를 방지 하는 데 도움이 될 수 있습니다. 

* 액세스 제어 모드, 가격 책정 계층, 보존 및 용량 예약 수준과 같은 작업 영역 구성 옵션을 완전히 이해 하려면 [추가 정보](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) 를 검토 합니다.

* [작업 영역 매핑을](how-to/region-mappings.md) 검토 하 여 지원 되는 지역 인라인 또는 매개 변수 파일을 지정 합니다. 구독에서 Log Analytics 작업 영역 및 Automation 계정을 연결 하는 데는 특정 영역만 지원 됩니다.

* Azure Monitor 로그를 처음 접하는 경우 이미 작업 영역을 배포 하지 않은 경우 [작업 영역 디자인 지침](../azure-monitor/platform/design-logs-deployment.md)을 검토 해야 합니다. 액세스 제어에 대해 알아보고 조직에 권장 되는 디자인 구현 전략을 이해 하는 데 도움이 됩니다.

## <a name="deploy-the-template"></a>템플릿 배포

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
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
     ]
    }
    ```

2. 요구 사항을 충족하도록 템플릿을 편집합니다. 매개 변수를 인라인 값으로 전달 하는 대신 [리소스 관리자 매개 변수 파일](../azure-resource-manager/templates/parameter-files.md) 을 만드는 것이 좋습니다.

3. 이 파일을 로컬 폴더에 deployAzAutomationAccttemplate로 저장 합니다.

4. 이제 이 템플릿을 배포할 수 있습니다. PowerShell 또는 Azure CLI 중 하나를 사용할 수 있습니다. 작업 영역 및 Automation 계정 이름을 입력 하 라는 메시지가 표시 되 면 모든 Azure 구독에서 전역적으로 고유한 이름을 제공 합니다.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    배포를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 이 경우 결과를 포함 하는 다음과 같은 메시지가 표시 됩니다.

    ![배포가 완료되었을 때 결과 예](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>다음 단계

이제 Automation 계정이 있으므로 runbook을 만들고 수동 프로세스를 자동화할 수 있습니다.

* PowerShell Runbook을 시작하려면 [PowerShell Runbook 만들기](automation-first-runbook-textual-powershell.md)를 참조하세요.
* PowerShell 워크플로 runbook을 시작 하려면 [Powershell workflow Runbook 만들기](automation-first-runbook-textual.md)를 참조 하세요.
* Python 2 runbook을 시작 하려면 [python Runbook 만들기](automation-first-runbook-textual-python2.md)를 참조 하세요.