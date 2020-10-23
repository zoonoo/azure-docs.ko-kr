---
title: Azure Resource Manager 템플릿을 사용 하 여 VM 확장 사용
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 하이브리드 클라우드 환경에서 실행 되는 Azure Arc 사용 서버에 가상 머신 확장을 배포 하는 방법을 설명 합니다.
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 23cb1e85b9560b7033dc5bdce672ee8718ed326b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462935"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>ARM 템플릿을 사용 하 여 Azure VM 확장 사용

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 azure Arc 사용 서버에서 지 원하는 Azure VM 확장을 배포 하는 방법을 보여 줍니다.

Azure Resource Manager 템플릿에 VM 확장을 추가하고 템플릿 배포를 통해 실행할 수 있습니다. Arc 사용 서버에서 지 원하는 VM 확장을 사용 하 여 Azure PowerShell를 사용 하 여 Linux 또는 Windows 컴퓨터에서 지원 되는 VM 확장을 배포할 수 있습니다. 아래 각 샘플에는 템플릿에 제공할 샘플 값이 포함 된 템플릿 파일 및 매개 변수 파일이 포함 되어 있습니다.

>[!NOTE]
>여러 확장을 함께 일괄 처리 하 고 처리할 수 있지만 직렬로 설치 됩니다. 첫 번째 확장 설치가 완료 되 면 다음 확장을 설치 하려고 시도 합니다.

## <a name="deploy-the-log-analytics-vm-extension"></a>Log Analytics VM 확장 배포

Log Analytics 에이전트를 쉽게 배포 하려면 Windows 또는 Linux에 에이전트를 설치 하기 위한 다음 샘플이 제공 됩니다.

### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

템플릿과 매개 변수 파일을 디스크에 저장 하 고 배포에 적절 한 값을 사용 하 여 매개 변수 파일을 편집 합니다. 그런 다음, 다음 명령을 사용 하 여 리소스 그룹 내에 있는 모든 연결 된 컴퓨터에 확장을 설치할 수 있습니다. 이 명령은 템플릿 *파일* 매개 변수를 사용 하 여 템플릿을 지정 하 고, 템플릿 *parameterfile* 매개 변수를 사용 하 여 매개 변수 및 매개 변수 값을 포함 하는 파일을 지정 합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>사용자 지정 스크립트 확장 배포

사용자 지정 스크립트 확장을 사용 하기 위해 Windows 및 Linux에서 실행 하기 위해 다음 샘플이 제공 됩니다. 사용자 지정 스크립트 확장에 익숙하지 않은 경우 [Windows 용 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-windows.md) 또는 [Linux 용 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-linux.md)을 참조 하세요. 하이브리드 컴퓨터에서이 확장을 사용 하는 경우 다음과 같은 몇 가지 특성을 이해 해야 합니다.

* Azure VM 사용자 지정 스크립트 확장을 사용 하는 지원 되는 운영 체제 목록은 Azure Arc 사용 서버에 적용 되지 않습니다. Arc 사용 서버에 대해 지원 되는 OSs 목록은 [여기](agent-overview.md#supported-operating-systems)에서 찾을 수 있습니다.

* Azure Virtual Machine Scale Sets 또는 클래식 Vm에 대 한 구성 세부 정보는 적용 되지 않습니다.

* 컴퓨터에서 외부 스크립트를 다운로드 해야 하 고 프록시 서버를 통해서만 통신할 수 있는 경우 [연결 된 컴퓨터 에이전트를 구성](manage-agent.md#update-or-remove-proxy-settings) 하 여 프록시 서버 환경 변수를 설정 해야 합니다.

사용자 지정 스크립트 확장 구성은 스크립트 위치 및 실행할 명령 등을 지정합니다. 이 구성은 Linux 및 Windows 하이브리드 컴퓨터에 대해 아래에 제공 된 Azure Resource Manager 템플릿에 지정 됩니다.

### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-powershell-dsc-extension"></a>PowerShell DSC 확장 배포

PowerShell DSC 확장을 사용 하기 위해 Windows 및 Linux에서 실행 하기 위해 다음 샘플을 제공 합니다. PowerShell DSC 확장에 익숙하지 않은 경우 [DSC 확장 처리기 개요](../../virtual-machines/extensions/dsc-overview.md)를 참조 하세요. 하이브리드 컴퓨터에서이 확장을 사용 하는 경우 다음과 같은 몇 가지 특성을 이해 해야 합니다.

* Azure VM PowerShell DSC 확장을 사용 하는 지원 되는 운영 체제 목록은 Azure Arc 사용 서버에 적용 되지 않습니다. Arc 사용 서버에 대해 지원 되는 OSs 목록은 [여기](agent-overview.md#supported-operating-systems)에서 찾을 수 있습니다.

* 컴퓨터에서 외부 스크립트를 다운로드 해야 하 고 프록시 서버를 통해서만 통신할 수 있는 경우 [연결 된 컴퓨터 에이전트를 구성](manage-agent.md#update-or-remove-proxy-settings) 하 여 프록시 서버 환경 변수를 설정 해야 합니다.

### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent"></a>종속성 에이전트 배포

Azure Monitor 종속성 에이전트 확장을 사용 하려면 Windows 및 Linux에서 실행할 수 있도록 다음 샘플을 제공 합니다. 종속성 에이전트에 익숙하지 않은 경우 [Azure Monitor 에이전트 개요](../../azure-monitor/platform/agents-overview.md#dependency-agent)를 참조 하세요.

### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="next-steps"></a>다음 단계

* Azure CLI 또는 PowerShell을 사용 하 여 확장을 배포 하 고 제거할 수 있습니다.

* 문제 해결 정보는 [VM 확장 문제 해결 가이드](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.