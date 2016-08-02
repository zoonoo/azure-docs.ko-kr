<properties
	pageTitle="모범 사례 Resource Manager 템플릿 | Microsoft Azure"
	description="Azure Resource Manager 템플릿 간소화를 위한 지침입니다."
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
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="tomfitz"/>

# Azure Resource Manager 템플릿 생성 모범 사례

다음 지침은 안정적이고 사용하기 쉬운 Resource Manager 템플릿을 만드는 데 도움이 됩니다. 이러한 지침은 제안일 뿐이며 반드시 필요한 것은 아닙니다. 본인의 상황에 맞게 이러한 지침을 적절히 변경해서 적용할 수 있습니다.

## 리소스 이름

일반적으로 다음 세 가지 유형의 리소스 이름을 사용하게 됩니다.

1. 고유해야 하는 리소스 이름
2. 고유하지 않아도 되는 리소스 이름(컨텍스트를 식별하는 데 도움이 되는 이름을 제공할 수 있음)
3. 일반적일 수 있는 리소스 이름

명명 규칙 설정에 대한 도움이 필요한 경우 [인프라 명명 지침](./virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md)을 참조하세요. 리소스 이름 제한에 대한 자세한 내용은 [Azure 리소스에 대한 권장 명명 규칙](./guidance/guidance-naming-conventions.md)을 참조하세요.

### 고유한 리소스 이름

데이터 액세스 끝점이 있는 모든 리소스 유형에 대해 고유한 리소스 이름을 제공해야 합니다. 고유 이름이 필요한 일반적인 유형은 다음과 같습니다.

- 저장소 계정
- 웹 사이트
- SQL Server
- 주요 자격 증명 모음
- Redis 캐시
- 배치 계정
- 트래픽 관리자
- 검색 서비스
- HDInsight 클러스터

또한 저장소 계정 이름은 24자 이내의 소문자여야 하며 하이픈은 포함할 수 없습니다.

이러한 리소스 이름에 대한 매개 변수를 제공하고 배포 하는 동안 고유한 이름을 추측하는 대신, [uniqueString()](resource-group-template-functions.md#uniquestring) 함수를 사용하여 이름을 생성하는 변수를 만들 수 있습니다. 종종 이름을 보고 리소스 유형을 좀 더 쉽게 알 수 있도록 **uniqueString** 결과에 접두사나 접미사를 추가하려는 경우가 있을 것입니다. 예를 들어 다음 변수를 사용하여 저장소 계정에 대한 고유 이름을 생성할 수 있습니다.

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

uniqueString 접두사가 있는 저장소 계정은 동일한 랙에서 클러스터되지 않습니다.

### 식별을 위한 리소스 이름

이름을 지정하려고 하지만 고유성을 보장할 필요는 없는 리소스 유형의 경우 해당 컨텍스트 및 리소스 유형을 식별하는 이름을 제공하면 됩니다. 리소스 이름 목록에서 리소스를 인식하는 데 도움이 되는 설명이 포함된 이름을 제공할 수 있습니다. 배포 동안 리소스 이름을 변경해야 할 경우 이름에 대해 다음 매개 변수를 사용합니다.

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

배포하는 동안 이름을 제공할 필요가 없으면 다음 변수를 사용합니다.

    "variables": {
        "vmName": "demoLinuxVM"
    }

또는 하드 코드된 다음 값을 사용합니다.

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### 일반 리소스 이름

주로 다른 리소스를 통해 액세스하는 리소스 유형의 경우 템플릿에 하드 코드된 일반 이름을 사용할 수 있습니다. 예를 들어 SQL Server의 방화벽 규칙에 대해서는 사용자 지정이 가능한 이름을 제공하지 않으려고 할 것입니다.

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## 매개 변수

1. 가능하면 매개 변수 수를 최소화합니다. 변수 또는 리터럴을 사용할 수 있는 경우 사용합니다. 다음 경우에만 매개 변수를 제공합니다.
 - 환경마다 다르게 지정하려는 설정(예: sku, 크기 또는 용량)
 - 쉽게 식별할 수 있도록 지정하려는 리소스 이름
 - 다른 작업을 완료하는 데 자주 사용하는 값(예: 관리 사용자 이름)
 - 암호(Secret)(예: 암호(password))
 - 한 리소스 유형의 인스턴스를 여러 개 만들 때 사용할 값의 수 또는 배열

1. 매개 변수 이름은 **camelCasing**을 따라야 합니다.

1. 모든 매개 변수에 대해 메타데이터에 설명을 제공합니다.

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. 매개 변수의 기본값을 정의합니다(암호 및 SSH 키 제외).

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. 모든 암호(password) 및 암호(secret)에 대해 **securestring**을 사용합니다.

        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
 
1. 가능한 경우 **위치**를 지정할 때는 매개 변수를 사용하지 않도록 합니다. 대신 리소스 그룹의 위치 속성을 사용합니다. 모든 리소스에 대해 **resourceGroup ().location** 식을 사용하면 템플릿의 리소스가 리소스 그룹이 있는 동일한 위치에 배포됩니다.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
  
     리소스 유형이 제한된 수의 위치에서만 지원될 경우 템플릿에 직접 유효한 위치를 지정하는 것이 좋습니다. 위치 매개 변수를 사용해야 할 경우 해당 매개 변수 값을 동일한 위치에 있을 수 있는 리소스와 가능한 많이 공유합니다. 이 방법을 사용하면 사용자가 모든 리소스 유형에 대해 위치를 제공할 필요가 최소화됩니다.

1. 리소스 유형의 API 버전에 대해서는 매개 변수나 변수를 사용하지 마세요. 리소스 속성 및 값은 버전 번호에 따라 달라질 수 있습니다. 코드 편집기의 Intellisense는 API 버전이 매개 변수 또는 변수로 설정되면 올바른 스키마를 확인할 수 없습니다. 대신, 템플릿에 API 버전을 하드 코드합니다.

## 변수 

1. 두 번 이상 사용해야 하는 값의 경우 템플릿에서 변수를 사용합니다. 값을 한 번만 사용할 경우에는 하드 코드된 값을 사용해야 템플릿을 읽기가 좀 더 쉬워집니다.

1. 변수 섹션에는 [참조 영역](resource-group-template-functions.md#reference) 함수를 사용할 수 없습니다. 참조 영역 함수는 리소스의 런타임 상태에서 해당 값을 파생하지만 템플릿의 초기 구문 분석 중에 변수가 확인됩니다. 대신, 템플릿의 **리소스** 또는 **출력** 섹션에서 직접 **참조 영역** 함수가 필요한 값을 생성합니다.

1. [리소스 이름](#resource-names)에 표시된 대로 고유해야 하는 리소스 이름에 대한 변수를 포함합니다.

1. 변수를 복잡한 개체로 그룹화할 수 있습니다. **variable.subentry** 형식으로 복잡한 개체의 값을 참조할 수 있습니다. 변수를 그룹화하면 관련 변수를 추적하는 데 도움이 되며 템플릿의 가독성을 높일 수 있습니다.

        "variables": {
            "storage": {
                "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
                "type": "Standard_LRS"
            }
        },
        "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storage').name]",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "sku": {
                  "name": "[variables('storage').type]"
              },
              ...
          }
        ]
 
     > [AZURE.NOTE] 복잡한 개체에는 복잡한 개체의 값을 참조하는 식이 포함될 수 없습니다. 이 목적을 위해서는 별도의 변수를 정의합니다.

     복잡한 개체를 변수로 사용하는 고급 예제를 보려면 [Azure Resource Manager 템플릿에서 상태 공유](best-practices-resource-manager-state.md)를 참조하세요.

## 리소스

1. 템플릿에 각 리소스에 대한 **설명**을 지정하여 다른 참가자들이 리소스의 용도를 이해하도록 도와줍니다.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]

1. 태그를 사용하여 리소스에 대한 정보를 좀 더 추가할 수 있는 메타데이터를 리소스에 추가합니다. 예를 들어 청구 세부 정보를 제공하려는 목적으로 리소스에 메타데이터를 추가할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.

1. 템플릿에서 **공용 끝점**(예: Blob 저장소 공용 끝점)을 사용하는 경우 네임스페이스를 **하드 코드하지 마세요**. **참조 영역** 함수를 사용하여 네임스페이스를 동적으로 검색합니다. 이렇게 하면 템플릿의 끝점을 수동으로 변경하지 않고 다른 공용 네임스페이스 환경에 템플릿을 배포할 수 있습니다. 템플릿에서 storageAccount에 사용하는 것과 동일한 버전으로 apiVersion을 설정합니다.

        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     저장소 계정에 동일한 템플릿에 배포되면 리소스를 참조할 때 공급자 네임스페이스를 지정할 필요가 없습니다. 간단한 구문은 다음과 같습니다.
     
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     템플릿의 다른 값을 공용 네임스페이스로 구성한 경우 동일한 참조 영역 함수를 반영하도록 이러한 값을 변경합니다. 가상 컴퓨터 diagnosticsProfile의 storageUri 속성을 예로 들 수 있습니다.

        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
 
     다른 리소스 그룹에서 기존 저장소 계정을 **참조**할 수도 있습니다.


        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. 응용 프로그램에 필요한 경우에만 publicIPAddresses를 가상 컴퓨터에 할당합니다. 디버그, 관리 또는 운영 목적으로 연결하려면 inboundNatRules, virtualNetworkGateways 또는 jumpbox를 사용합니다.

     가상 컴퓨터에 연결하는 방법에 대한 자세한 내용은 다음을 참조하세요.
     - [Azure에서 N 계층 아키텍처에 대한 VM 실행](./guidance/guidance-compute-3-tier-vm.md)
     - [Azure Resource Manager에서 가상 컴퓨터에 대한 WinRM 액세스 설정](./virtual-machines/virtual-machines-windows-winrm.md)
     - [Azure 포털을 사용하여 VM에 대한 외부 액세스 허용](./virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md)
     - [PowerShell을 사용하여 VM에 대한 외부 액세스 허용](./virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md)
     - [포트 및 끝점 열기](./virtual-machines/virtual-machines-linux-nsg-quickstart.md)

1. publicIPAddresses에 대한 **domainNameLabel** 속성은 고유해야 합니다. domainNameLabel은 3~63자 사이여야 하고 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` 정규식으로 지정된 규칙을 따라야 합니다. uniqueString 함수는 아래 예제에서 13자 길이의 문자열을 생성하므로, dnsPrefixString 접두사 문자열이 50자 이내이고 해당 규칙을 준수하는 것으로 확인되었다고 간주됩니다.

        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }

1. **customScriptExtension**에 암호를 추가하는 경우 protectedSettings에서 **commandToExecute** 속성을 사용합니다.

        "properties": {
            "publisher": "Microsoft.OSTCExtensions",
            "type": "CustomScriptForLinux",
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }

     > [AZURE.NOTE] virtualMachines/extensions에 매개 변수로 제공되는 비밀이 암호화되도록 하려면 관련 확장의 protectedSettings 속성이 사용되어야 합니다.

## 출력

템플릿이 새 **publicIPAddresses**를 만드는 경우 배포 후에 이러한 세부 정보를 쉽게 검색하려면 IP 주소 및 정규화된 도메인의 세부 정보를 제공하는 **출력** 섹션이 만들어져야 합니다. 리소스를 참조할 때 이러한 섹션을 만드는 데 사용된 API 버전을 사용합니다.

```
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## 단일 템플릿 또는 중첩된 템플릿

솔루션을 배포하려면 단일 템플릿 또는 여러 개의 중첩된 템플릿이 있는 주 템플릿 중 하나를 사용할 수 있습니다. 중첩된 템플릿은 좀 더 수준 높은 시나리오에서 일반적입니다. 중첩된 템플릿은 다음과 같은 이점도 제공합니다.

1. 솔루션을 대상 구성 요소로 분해할 수 있습니다.
2. 중첩된 템플릿을 다른 주 템플릿에서 다시 사용할 수 있습니다.

템플릿 디자인을 여러 개의 중첩된 템플릿으로 분해하기로 결정한 경우 다음 지침이 디자인을 표준화하는 데 도움이 될 것입니다. 이러한 지침은 [Azure Resource Manager 템플릿 디자인 패턴](best-practices-resource-manager-design-templates.md) 설명서를 기준으로 합니다. 권장되는 디자인은 다음 템플릿으로 구성됩니다.

+ **주 템플릿**(azuredeploy.json). 입력 매개 변수에 사용합니다.
+ **공유 리소스 템플릿**. 다른 모든 리소스(예: 가상 네트워크, 가용성 집합)에서 사용하는 공유 리소스를 배포합니다. 식 dependsOn은 다른 템플릿보다 먼저 이 템플릿이 배포되도록 합니다.
+ **선택적 리소스 템플릿**. 매개 변수(예: jumpbox)를 기준으로 조건에 따라 리소스를 배포합니다.
+ **멤버 리소스 템플릿**. 응용 프로그램 계층 내의 각 인스턴스 유형은 자체 구성을 가집니다. 계층 내에 다른 인스턴스 유형을 정의할 수 있습니다. 예를 들어 첫 번째 인스턴스에서 새 클러스터를 만들면 추가 인스턴스가 기존 클러스터에 추가됩니다. 각 인스턴스 유형은 고유한 배포 템플릿을 갖게 됩니다.
+ **스크립트**. 광범위한 재사용이 가능한 스크립트를 각 인스턴스 유형에 적용할 수 있습니다(예: 추가 디스크 초기화 및 포맷). 인스턴스 유형마다 다른 특정 사용자 지정 목적으로 사용자 지정 스크립트가 생성됩니다.

![중첩된 템플릿](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

자세한 내용은 [Azure 리소스 관리자에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

## 중첩된 템플릿에 조건부로 연결

템플릿에 대한 URI의 일부인 매개 변수를 사용하여 중첩된 템플릿에 조건부로 연결할 수 있습니다.

    "parameters": {
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },
    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                }
            }
        }
    ]

## 템플릿 형식

1. 템플릿을 JSON 유효성 검사기에 적용하여 배포 중에 오류를 야기할 수 있는 불필요한 쉼표, 괄호, 대괄호를 제거하는 것이 좋습니다. 자주 사용하는 편집 환경을 위해 [JSONlint](http://jsonlint.com/) 또는 linter 패키지를 사용합니다(Visual Studio Code, Atom, Sublime Text, Visual Studio 등).
1. 또한 가독성을 높이기 위해 JSON 형식을 지정하는 것이 좋습니다. 로컬 편집기에 대한 JSON 포맷터 패키지를 사용할 수 있습니다. Visual Studio에서는 **Ctrl+K, Ctrl+D**를 사용하여 문서 서식을 지정합니다. VS 코드에서는 **Alt+Shift+F**를 사용합니다. 로컬 편집기에서 문서 서식을 지정하지 않는 경우 [온라인 포맷터](https://www.bing.com/search?q=json+formatter)를 사용할 수 있습니다.

## 다음 단계

1. 가상 컴퓨터에 대한 솔루션 설계 지침을 보려면 [Azure에서 Windows VM 실행](./guidance/guidance-compute-single-vm.md) 및 [Azure에서 Linux VM 실행](./guidance/guidance-compute-single-vm-linux.md)을 참조하세요.
2. 저장소 계정 설정에 대한 자세한 내용은 [Microsoft Azure 저장소 성능 및 확장성 검사 목록](./storage/storage-performance-checklist.md)을 참조하세요.
3. 가상 네트워크에 대한 도움말을 보려면 [네트워킹 인프라 지침](./virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md)을 참조하세요.

<!---HONumber=AcomDC_0720_2016-->