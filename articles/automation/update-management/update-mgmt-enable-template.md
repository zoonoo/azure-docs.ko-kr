---
title: Azure Resource Manager 템플릿을 사용하여 업데이트 관리 사용 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/18/2020
ms.openlocfilehash: 312457a6220920d550f05e3a1cd1dc5ec2f4449a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327857"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 업데이트 관리 사용

[Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md)을 사용하여 리소스 그룹에서 Azure Automation 업데이트 관리 기능을 사용하도록 설정할 수 있습니다. 이 문서에서는 다음을 자동화하는 샘플 템플릿을 제공합니다.

* Azure Monitor Log Analytics 작업 영역 만들기를 자동화합니다.
* Azure Automation 계정 만들기를 자동화합니다.
* Automation 계정을 Log Analytics 작업 영역에 연결합니다.
* 계정에 샘플 Automation Runbook을 추가합니다.
* 업데이트 관리 기능을 사용 하도록 설정 합니다.

템플릿은 하나 이상의 Azure 또는 비 Azure Vm에서 업데이트 관리 사용 하도록 자동화 하지 않습니다.

구독에서 지원되는 지역에 배포된 Log Analytics 작업 영역 및 Automation 계정이 이미 있는 경우에는 연결되지 않습니다. 이 템플릿을 사용 하 여 성공적으로 링크를 만들고 업데이트 관리를 배포 합니다.

>[!NOTE]
>ARM 템플릿을 사용하는 경우 Automation 실행 계정 만들기가 지원되지 않습니다. 포털에서 또는 PowerShell을 사용하여 실행 계정을 수동으로 만들려면 [실행 계정 관리](../manage-runas-account.md)를 참조하세요.

이러한 단계를 완료한 후에는 Automation 계정에 대한 [진단 설정을 구성](../automation-manage-send-joblogs-log-analytics.md)하여 Runbook 작업 상태 및 작업 스트림을 연결된 Log Analytics 작업 영역으로 보내도록 해야 합니다.

## <a name="api-versions"></a>API 버전

다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 유형 | API 버전 |
|:---|:---|:---|
| [작업 영역](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation 계정](/azure/templates/microsoft.automation/automationaccounts) | Automation | 2020-01-13-preview |
| [작업 영역 연결된 서비스](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |
| [솔루션](/azure/templates/microsoft.operationsmanagement/solutions) | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>템플릿을 사용하기 전에

JSON 템플릿은 다음을 묻는 메시지를 표시하도록 구성됩니다.

* 작업 영역의 이름
* 작업 영역을 만들 지역
* Automation 계정의 이름
* Automation 계정을 만들 지역

템플릿의 다음 매개 변수는 Log Analytics 작업 영역의 기본값으로 설정됩니다.

* *sku*는 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값입니다.
* *dataRetention*은 기본값이 30일입니다.

>[!WARNING]
>2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성하려면 유효한 유일한 Log Analytics 가격 책정 계층은 *PerGB2018*입니다.
>

JSON 템플릿은 환경에서 표준 구성으로 사용될 수 있는 다른 매개 변수에 대해서는 기본값을 지정합니다. 조직에서 공유 액세스에 대한 Azure Storage 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [ARM 템플릿과 Azure CLI를 사용하여 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

Azure Automation 및 Azure Monitor를 처음 접하는 경우 다음 구성 정보를 이해하는 것이 중요합니다. 새 자동화 계정에 연결된 Log Analytics 작업 영역을 만들고 구성하고 사용하려고 할 때 오류를 방지하는 데 도움이 될 수 있습니다.

* 액세스 제어 모드, 가격 책정 계층, 보존 및 용량 예약 수준과 같은 작업 영역 구성 옵션을 완전히 이해하려면 [추가 세부 정보](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)를 검토하세요.

* [작업 영역 매핑](../how-to/region-mappings.md)을 검토하여 지원되는 지역 인라인 또는 매개 변수 파일에서 지정합니다. 특정 Azure 지역에서만 구독의 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다.

* Azure Monitor 로그를 처음 접하고 아직 작업 영역을 배포하지 않은 경우 [작업 영역 디자인 지침](../../azure-monitor/platform/design-logs-deployment.md)을 검토해야 합니다. 이 지침은 액세스 제어에 대해 알아보고 조직에 권장되는 디자인 구현 전략을 이해하는 데 도움이 됩니다.

## <a name="deploy-template"></a>템플릿 배포

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                "defaultValue": "[resourceGroup().location]",
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
                    "description": "Specifies the location in which to create the Automation account."
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
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
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
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
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
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. 요구 사항을 충족하도록 템플릿을 편집합니다. 매개 변수를 인라인 값으로 전달하는 대신 [Resource Manager 매개 변수 파일](../../azure-resource-manager/templates/parameter-files.md)을 만드는 것이 좋습니다.

3. 이 파일을 로컬 폴더에 **deployUMSolutiontemplate.json**으로 저장합니다.

4. 이제 이 템플릿을 배포할 수 있습니다. PowerShell 또는 Azure CLI 중 하나를 사용할 수 있습니다. 작업 영역 및 Automation 계정 이름을 묻는 메시지가 표시되면 모든 Azure 구독에서 전역적으로 고유한 이름을 입력합니다.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

    ![배포가 완료되었을 때 결과 예](media/update-mgmt-enable-template/template-output.png)

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 사용자가 만든 Automation 계정을 엽니다.

3. 왼쪽 창에서 **Runbook**을 선택합니다. **Runbook** 페이지에서 Automation 계정을 사용하여 만든 세 가지 자습서 Runbook이 나열됩니다.

    ![Automation 계정을 사용하여 만든 자습서 Runbook](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. 왼쪽 창에서 **연결된 작업 영역**을 선택합니다. **연결된 작업 영역** 페이지에서 이전에 Automation 계정에 연결된 Log Analytics 작업 영역을 표시합니다.

    ![Log Analytics 작업 영역에 연결된 Automation 계정](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. 왼쪽 창에서 **업데이트 관리**를 선택 합니다. **업데이트 관리** 페이지에는 사용 하도록 설정 된 결과로 정보 없이 평가 페이지가 표시 되 고 컴퓨터가 관리를 위해 구성 되지 않습니다.

    ![업데이트 관리 기능 평가 보기](./media/update-mgmt-enable-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 경우 Log Analytics 작업 영역에서 **업데이트** 솔루션을 삭제 하 고 작업 영역에서 automation 계정의 연결을 해제 한 다음 automation 계정 및 작업 영역을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

* Vm에 대 한 업데이트 관리를 사용 하려면 [vm에 대 한 업데이트 및 패치 관리](update-mgmt-manage-updates-for-vm.md)를 참조 하세요.

* 업데이트 관리을 더 이상 사용 하지 않고 제거 하려는 경우 [업데이트 관리 기능 제거](update-mgmt-remove-feature.md)의 지침을 참조 하세요.

* 업데이트 관리에서 VM을 삭제하려면 [업데이트 관리에서 VM 제거](update-mgmt-remove-vms.md)를 참조하세요.
