---
title: Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Automation 계정을 만드는 방법을 설명합니다.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/09/2020
ms.openlocfilehash: 7b9d3ea30f502f8f95bb12c6a3b270f8eddde0cf
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186625"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기

[Azure Resource Manager 템플릿](../azure-resource-manager/templates/template-syntax.md)을 사용하여 리소스 그룹에 Azure Automation 계정을 만들 수 있습니다. 이 문서에서는 다음을 수행하는 샘플 템플릿을 제공합니다.

* Azure Monitor Log Analytics 작업 영역 만들기를 자동화합니다.
* Azure Automation 계정 만들기를 자동화합니다.
* Automation 계정을 Log Analytics 작업 영역에 연결합니다.

템플릿은 Azure 또는 비 Azure 가상 머신을 자동으로 사용하도록 설정하지 않습니다. 

>[!NOTE]
>Azure Resource Manager 템플릿을 사용하는 경우 Automation 실행 계정 만들기가 지원되지 않습니다. 포털에서 또는 PowerShell을 사용하여 실행 계정을 수동으로 만들려면 [실행 계정 관리](manage-runas-account.md)를 참조하세요.

이러한 단계를 완료한 후에는 Automation 계정에 대한 [진단 설정을 구성](automation-manage-send-joblogs-log-analytics.md)하여 Runbook 작업 상태 및 작업 스트림을 연결된 Log Analytics 작업 영역으로 보내도록 해야 합니다. 

## <a name="api-versions"></a>API 버전

다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 유형 | API 버전 |
|:---|:---|:---|
| 작업 영역 | workspaces | 2020-03-01-미리 보기 |
| Automation 계정 | Automation | 2018-06-30 | 

## <a name="before-you-use-the-template"></a>템플릿을 사용하기 전에 확인해야 할 사항

이 문서에서는 PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 Azure PowerShell Az 모듈이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다. PowerShell의 경우 배포는 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용합니다.

이 문서에서는 Azure CLI를 로컬로 설치하여 사용하도록 선택한 경우 버전 2.1.0 이상을 실행 중이어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. Azure CLI에서 이 배포는 [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)를 사용합니다. 

JSON 템플릿은 다음을 묻는 메시지를 표시하도록 구성됩니다.

* 작업 영역의 이름
* 작업 영역을 만들 지역
* 리소스 또는 작업 영역 권한을 사용 하도록 설정 합니다.
* Automation 계정의 이름입니다.
* Automation 계정을 만들 지역입니다.

템플릿의 다음 매개 변수는 Log Analytics 작업 영역의 기본값으로 설정됩니다.

* *sku*는 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값입니다.
* *dataRetention*은 기본값이 30일입니다.

>[!WARNING]
>2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성하려면 유효한 유일한 Log Analytics 가격 책정 계층은 *PerGB2018*입니다.
>

JSON 템플릿은 환경에서 표준 구성으로 사용될 수 있는 다른 매개 변수에 대해서는 기본값을 지정합니다. 조직에서 공유 액세스에 대한 Azure Storage 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [Azure Resource Manager 템플릿과 Azure CLI를 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

Azure Automation 및 Azure Monitor를 처음 접하는 경우 다음 구성 정보를 이해하는 것이 중요합니다. 새 자동화 계정에 연결된 Log Analytics 작업 영역을 만들고 구성하고 사용하려고 할 때 오류를 방지하는 데 도움이 될 수 있습니다.

* 액세스 제어 모드, 가격 책정 계층, 보존 및 용량 예약 수준과 같은 작업 영역 구성 옵션을 완전히 이해하려면 [추가 세부 정보](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)를 검토하세요.

* [작업 영역 매핑](how-to/region-mappings.md)을 검토하여 지원되는 지역 인라인 또는 매개 변수 파일에서 지정합니다. 특정 Azure 지역에서만 구독의 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다.

* Azure Monitor 로그를 처음 접하고 아직 작업 영역을 배포하지 않은 경우 [작업 영역 디자인 지침](../azure-monitor/platform/design-logs-deployment.md)을 검토해야 합니다. 이 지침은 액세스 제어에 대해 알아보고 조직에 권장되는 디자인 구현 전략을 이해하는 데 도움이 됩니다.

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
                "description": "Number of days to retain data."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
            "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
                        "apiVersion": "2018-06-30",
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
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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

2. 요구 사항을 충족하도록 템플릿을 편집합니다. 매개 변수를 인라인 값으로 전달하는 대신 [Resource Manager 매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)을 만드는 것이 좋습니다.

3. 이 파일을 로컬 폴더에 deployAzAutomationAccttemplate.json으로 저장합니다.

4. 이제 이 템플릿을 배포할 수 있습니다. PowerShell 또는 Azure CLI 중 하나를 사용할 수 있습니다. 작업 영역 및 Automation 계정 이름을 묻는 메시지가 표시되면 모든 Azure 구독에서 전역적으로 고유한 이름을 입력합니다.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 결과를 포함하는 다음과 같은 메시지가 표시됩니다.

    ![배포가 완료되었을 때 결과 예](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>다음 단계

Runbook 작업 상태 및 작업 스트림을 연결된 Log Analytics 작업 영역에 전달하려면 [Azure Automation 작업 데이터를 Azure Monitor 로그에 전달](automation-manage-send-joblogs-log-analytics.md)을 검토합니다. 이렇게 하면 Azure PowerShell 명령을 사용하여 Automation 계정의 진단 설정을 구성하고 분석을 위한 작업 영역으로 로그를 전송하는 통합을 완료할 수 있습니다. 
