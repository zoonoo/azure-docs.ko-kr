---
title: "OMS 솔루션의 Automation 리소스 | Microsoft Docs"
description: "OMS의 솔루션에는 일반적으로 모니터링 데이터를 수집하고 처리하는 등의 프로세스를 자동화하기 위한 Azure Automation의 runbook이 포함됩니다.  이 문서에서는 솔루션에 runbook과 관련 리소스를 포함하는 방법을 설명합니다."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>OMS 솔루션의 Automation 리소스(Preview)
> [!NOTE]
> 현재 Preview로 제공되는 OMS의 사용자 지정 솔루션 만들기에 대한 예비 설명서입니다. 아래 설명된 스키마는 변경될 수 있습니다.   
> 
> 

[OMS의 관리 솔루션](operations-management-suite-solutions.md)에는 일반적으로 모니터링 데이터를 수집하고 처리하는 등의 프로세스를 자동화하기 위한 Azure Automation의 runbook이 포함됩니다.  runbook 외에도 Automation 계정에는 솔루션에서 사용되는 runbook을 지원하는 변수, 일정 등의 자산이 포함됩니다.  이 문서에서는 솔루션에 runbook과 관련 리소스를 포함하는 방법을 설명합니다.

> [!NOTE]
> 이 문서의 샘플에는 관리 솔루션에 필요하거나 공통적이며 [OMS(Operations Management Suite)의 관리 솔루션 만들기](operations-management-suite-solutions-creating.md)에서 설명한 매개 변수와 변수가 사용됩니다. 
> 
> 

## <a name="prerequisites"></a>필수 조건
이 문서에서는 [관리 솔루션을 만드는](operations-management-suite-solutions-creating.md) 방법과 솔루션 파일의 구조를 잘 알고 있다고 가정합니다.

## <a name="samples"></a>샘플
Automation 리소스에 대한 샘플 리소스 관리자 템플릿은 [GitHub의 빠른 시작 템플릿](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates)에서 얻을 수 있습니다.

## <a name="automation-account"></a>Automation 계정
Azure Automation의 모든 리소스는 [Automation 계정](../automation/automation-security-overview.md#automation-account-overview)에 포함됩니다.  [OMS 작업 영역 및 Automation 계정](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)에서 설명한 대로 Automation 계정은 관리 솔루션에 포함되지 않지만, 솔루션이 설치되기 전에 존재해야 합니다.  계정을 사용할 수 없으면 솔루션 설치에 실패합니다.

Automation 계정의 이름은 각 Automation 리소스의 이름에 포함됩니다.  이 작업은 다음 runbook 리소스 예제와 같이 **accountName** 매개 변수가 포함된 솔루션에서 이루어집니다.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
[Azure Automation runbook](../automation/automation-runbook-types.md) 리소스의 형식은 **Microsoft.Automation/automationAccounts/runbooks**이고 포함된 속성은 다음 표에 나와 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| runbookType |runbook 유형을 지정합니다. <br><br> Script - PowerShell 스크립트 <br>PowerShell - PowerShell 워크플로 <br> GraphPowerShell - 그래픽 PowerShell 스크립트 runbook <br> GraphPowerShellWorkflow - 그래픽 PowerShell 워크플로 runbook |
| logProgress |runbook에 대한 [진행률 레코드](../automation/automation-runbook-output-and-messages.md)를 생성해야 하는지 지정합니다. |
| logVerbose |runbook에 대한 [세부 정보 레코드](../automation/automation-runbook-output-and-messages.md)를 생성해야 하는지 지정합니다. |
| 설명 |runbook에 대한 선택적 설명입니다. |
| publishContentLink |runbook의 내용을 지정합니다. <br><br>uri - runbook 내용의 URL입니다.  이 URL은 PowerShell 및 Script runbook의 경우 .ps1 파일이 되고 그래프 runbook의 경우 내보낸 그래픽 runbook 파일이 됩니다.  <br> version - 자체적으로 추적하기 위한 runbook 버전입니다. |

runbook 리소스의 예는 다음과 같습니다.  이 경우 [PowerShell 갤러리](https://www.powershellgallery.com)에서 runbook을 검색합니다.

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Runbook 시작
관리 솔루션에서 runbook을 시작하는 방법은 두 가지가 있습니다.

* 솔루션이 설치될 때 runbook을 시작하려면 아래 설명대로 [작업 리소스](#automation-jobs)를 만듭니다.
* 일정에 따라 runbook을 시작하려면 아래 설명대로 [일정 리소스](#schedules)를 만듭니다. 

## <a name="automation-jobs"></a>Automation 작업
관리 솔루션이 설치될 때 runbook을 시작하려면 **job** 리소스를 만듭니다.  작업 리소스의 형식은 **Microsoft.Automation/automationAccounts/jobs**이고 포함된 속성은 다음 표에 나와 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| runbook |runbook 이름을 포함하는 단일 **name** 엔터티입니다. |
| 매개 변수 |Runbook에 필요한 각 매개 변수의 엔터티입니다. |

작업에는 runbook 이름과 runbook으로 전송되는 모든 매개 변수 값이 포함됩니다.  작업 전에 runbook이 만들어져야 하므로 작업은 시작할 runbook에 [종속](operations-management-suite-solutions-creating.md#resources)되어야 합니다.  현재 runbook 전에 완료되어야 하는 runbook의 다른 작업에 대한 종속성을 만들 수도 있습니다.

작업 리소스의 이름에는 보통 매개 변수를 통해 할당되는 GUID가 포함되어야 합니다.  GUID 매개 변수에 대한 자세한 내용은 [OMS(Operations Management Suite)의 관리 솔루션 만들기](operations-management-suite-solutions-creating.md#parameters)를 참조하세요.  

관리 솔루션이 설치될 때 runbook을 시작하는 작업 리소스의 예는 다음과 같습니다.  이 runbook이 시작되기 전에 또 다른 runbook이 완료되어야 하므로 이 runbook은 또 다른 runbook의 작업에 종속됩니다.  작업에 대한 세부 정보는 직접 지정되지 않고 매개 변수와 변수를 통해 제공됩니다.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>인증서
[Azure Automation 인증서](../automation/automation-certificates.md)의 형식은**Microsoft.Automation/automationAccounts/certificates**이고 포함된 속성은 다음 표에 나와 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| base64Value |인증서에 대한 Base 64 값입니다. |
| thumbprint |인증서의 지문입니다. |

인증서 리소스의 예는 다음과 같습니다.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>자격 증명
[Azure Automation 자격 증명](../automation/automation-credentials.md)의 유형은 **Microsoft.Automation/automationAccounts/credentials**이고 포함된 속성은 다음 표에 나와 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| userName |인증서의 사용자 이름입니다. |
| password |인증서의 암호입니다. |

자격 증명 리소스의 예는 다음과 같습니다.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>일정
[Azure Automation 일정](../automation/automation-schedules.md)의 유형은 **Microsoft.Automation/automationAccounts/schedules**이고 포함된 속성은 다음 표에 나와 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| 설명 |일정에 대한 선택적 설명입니다. |
| startTime |일정 시작 시간을 DateTime 개체로 지정합니다. 적합한 DateTime으로 변환될 수 있는 경우 문자열을 제공할 수 있습니다. |
| isEnabled |일정 사용 여부를 지정합니다. |
| interval |일정 간격의 유형입니다.<br><br>일<br>시간 |
| frequency |특정 기간(일 또는 시간) 내에 일정이 실행되는 빈도입니다. |

일정 리소스의 예는 다음과 같습니다.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>변수
[Azure Automation 변수](../automation/automation-variables.md)의 유형은 **Microsoft.Automation/automationAccounts/variables**이고 포함된 속성은 다음 표에 나와 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| 설명 |변수에 대한 선택적 설명입니다. |
| isEncrypted |변수를 암호화해야 하는지 지정합니다. |
| type |변수의 데이터 형식입니다. |
| value |변수의 값입니다. |

변수 리소스의 예는 다음과 같습니다.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>모듈
runbook은 항상 사용 가능하므로 관리 솔루션에서는 [글로벌 모듈](../automation/automation-integration-modules.md)을 정의할 필요가 없습니다.  runbook에서 사용되는 다른 모듈에 대한 리소스를 포함해야 하고 runbook 전에 리소스가 만들어지도록 runbook이 모듈 리소스에 종속되어야 합니다.

[통합 모듈](../automation/automation-integration-modules.md)의 형식은 **Microsoft.Automation/automationAccounts/modules**이고 포함된 속성은 다음 표에 나와 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| contentLink |모듈의 내용을 지정합니다. <br><br>uri - runbook 내용의 URL입니다.  이 URL은 PowerShell 및 Script runbook의 경우 .ps1 파일이 되고 그래프 runbook의 경우 내보낸 그래픽 runbook 파일이 됩니다.  <br> version - 자체적으로 추적하기 위한 runbook 버전입니다. |

모듈 리소스의 예는 다음과 같습니다.

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>모듈 업데이트
일정을 사용하는 runbook이 포함된 관리 솔루션을 업데이트하는데 해당 runbook에 사용되는 새 모듈이 솔루션의 새 버전에 포함된 경우 runbook은 이전 버전의 모듈을 사용할 수 있습니다.  솔루션에 다음 runbook을 포함하고 다른 runbook 전에 runbook을 실행할 작업을 만들어야 합니다.  이렇게 하면 runbook이 로드되기 전에 필요한 경우 모듈이 업데이트됩니다.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript)을 사용하면 솔루션에서 runbook에 사용된 모든 모듈이 최신 버전이 됩니다.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript)를 사용하면 일정 리소스에 연결된 runbook이 최신 모듈을 사용하도록 모든 일정 리소스가 등록됩니다.

모듈 업데이트를 지원하기 위한 솔루션 필수 요소의 샘플은 다음과 같습니다.

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>다음 단계
* [솔루션에 보기를 추가](operations-management-suite-solutions-resources-views.md)하여 수집된 데이터를 시각화합니다.




<!--HONumber=Nov16_HO3-->


