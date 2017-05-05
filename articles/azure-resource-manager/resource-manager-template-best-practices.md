---
title: "Azure Resource Manager 템플릿 생성 모범 사례 | Microsoft Docs"
description: "Azure Resource Manager 템플릿 간소화를 위한 지침입니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fe52ced5e4dc829b5d1421bf0edcd58cffcaad19
ms.lasthandoff: 04/03/2017


---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 생성 모범 사례
이 지침은 안정적이고 사용하기 쉬운 Azure Resource Manager 템플릿을 만드는 데 도움이 됩니다. 지침은 제안 사항일 뿐입니다. 명시된 경우를 제외하고 필수 사항은 아닙니다. 사용자 시나리오에서는 다음 방법 또는 예제 중 하나를 변형해야 할 수 있습니다.

## <a name="resource-names"></a>리소스 이름
일반적으로 Resource Manager에서는 세 가지 유형의 리소스 이름으로 작업합니다.

* 고유해야 하는 리소스 이름
* 고유해야 할 필요는 없지만 컨텍스트를 기반으로 리소스를 식별하는 데 도움이 될 수 있는 이름을 제공하도록 선택
* 일반적일 수 있는 리소스 이름

명명 규칙 설정에 대한 도움이 필요한 경우 [Azure 인프라 명명 지침](../virtual-machines/windows/infrastructure-naming-guidelines.md)을 참조하세요. 리소스 이름 제한에 대한 자세한 내용은 [Azure 리소스에 대한 권장 명명 규칙](../guidance/guidance-naming-conventions.md)을 참조하세요.

### <a name="unique-resource-names"></a>고유한 리소스 이름
데이터 액세스 끝점이 있는 모든 리소스 유형에 대해 고유한 리소스 이름을 제공해야 합니다. 고유 이름이 필요한 일반적인 리소스 유형은 다음과 같습니다.

* Azure Storage<sup>1</sup> 
* Azure App Service의 Web Apps 기능
* SQL Server
* Azure 키 자격 증명 모음
* Azure Redis 캐시(영문)
* Azure 배치
* Azure 트래픽 관리자
* Azure 검색
* Azure HDInsight

<sup>1</sup> 저장소 계정 이름은 24자 이내의 소문자여야 하며 하이픈은 포함할 수 없습니다.

리소스 이름에 매개 변수를 제공하는 경우 리소스 배포 시 고유한 이름을 제공해야 합니다. 필요할 경우 [uniqueString()](resource-group-template-functions.md#uniquestring) 함수를 사용하여 이름을 생성하는 변수를 만들 수 있습니다. 

또한 **uniqueString** 결과에 접두사 또는 접미사를 추가할 수도 있습니다. 고유한 이름을 수정하면 이름으로 리소스 유형을 보다 쉽게 식별할 수 있습니다. 예를 들어 다음 변수를 사용하여 저장소 계정에 대한 고유 이름을 생성할 수 있습니다.

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>식별을 위한 리소스 이름
일부 리소스 유형의 경우 이름을 지정할 수 있지만 이름이 고유해야 할 필요는 없습니다. 이러한 리소스 유형의 경우 리소스 컨텍스트와 리소스 유형 모두를 식별하는 이름을 제공할 수 있습니다. 리소스 목록에서 리소스를 식별하는 데 도움이 되는 설명이 포함된 이름을 제공합니다. 배포마다 다른 리소스 이름을 사용해야 하는 경우 이름에 대해 매개 변수를 사용할 수 있습니다.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

배포하는 동안 이름을 전달할 필요가 없으면 변수를 사용하면 됩니다. 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

하드 코드된 값을 사용할 수도 있습니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>일반 리소스 이름
주로 다른 리소스를 통해 액세스하는 리소스 유형의 경우 템플릿에 하드 코드된 일반 이름을 사용할 수 있습니다. 예를 들어 SQL Server에서 방화벽 규칙에 대해 표준 일반 이름을 설정할 수 있습니다.

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>매개 변수
다음 정보는 매개 변수로 작업하는 경우 도움이 될 수 있습니다.

* 매개 변수 사용을 최소화합니다. 가능하면 항상, 변수 또는 리터럴 값을 사용합니다. 다음과 같은 시나리오에 대해서만 매개 변수를 사용합니다.
   
   * 환경(SKU, 크기, 용량)에 따라 변수를 사용하려는 설정
   * 쉽게 식별할 수 있도록 지정하려는 리소스 이름
   * 다른 작업을 완료하는 데 자주 사용하는 값(예: 관리 사용자 이름)
   * 비밀(예: 암호)
   * 한 리소스 유형의 인스턴스를 여러 개 만들 때 사용할 값의 수 또는 배열
* 매개 변수 이름에 카멜식 대/소문자를 사용합니다.
* 매개 변수에 모든 메타데이터의 설명을 제공합니다.

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 매개 변수의 기본값을 정의합니다(암호 및 SSH 키 제외).
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 모든 암호(password) 및 비밀(secret)에 대해 **SecureString**을 사용합니다. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 가능하면 항상, 위치를 지정할 때는 매개 변수를 사용하지 마세요. 대신 리소스 그룹의 **위치** 속성을 사용하세요. 모든 리소스에 대해 **resourceGroup().location** 식을 사용하면 템플릿의 리소스가 리소스 그룹과 동일한 위치에 배포됩니다.
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   리소스 유형이 제한된 수의 위치에서만 지원될 경우 템플릿에 직접 유효한 위치를 지정하려고 할 수 있습니다. **위치** 매개 변수를 사용해야 할 경우 해당 매개 변수 값을 동일한 위치에 있을 수 있는 리소스와 가능한 많이 공유합니다. 이렇게 하면 사용자에게 위치 정보를 제공하라고 요청하는 횟수가 최소화됩니다.
* 리소스 유형의 API 버전에 대해서는 매개 변수나 변수를 사용하지 마세요. 리소스 속성 및 값은 버전 번호에 따라 달라질 수 있습니다. API 버전이 매개 변수 또는 변수로 설정되면 코드 편집기의 IntelliSense에서 올바른 스키마를 확인할 수 없습니다. 대신, 템플릿에 API 버전을 하드 코드합니다.

## <a name="variables"></a>variables
다음 정보는 변수로 작업하는 경우 도움이 될 수 있습니다.

* 두 번 이상 사용해야 하는 값의 경우 템플릿에서 변수를 사용합니다. 값을 한 번만 사용할 경우에는 하드 코드된 값을 사용해야 템플릿을 더 쉽게 읽을 수 있습니다.
* 템플릿의 **변수** 섹션에는 [reference](resource-group-template-functions.md#reference) 함수를 사용할 수 없습니다. **reference** 함수는 리소스의 런타임 상태에서 해당 값을 파생합니다. 하지만 템플릿의 초기 구문 분석 중에 변수가 확인됩니다. 템플릿의 **resources** 또는 **outputs** 섹션에서 직접 **reference** 함수에 필요한 값을 생성합니다.
* [리소스 이름](#resource-names)에 설명된 대로 고유해야 하는 리소스 이름에 대한 변수를 포함합니다.
* 변수를 복잡한 개체로 그룹화할 수 있습니다. **variable.subentry** 형식을 사용하여 복잡한 개체의 값을 참조합니다. 변수 그룹화는 관련 변수를 추적하는 데 도움이 됩니다. 또한 템플릿의 가독성도 향상시킵니다. 예를 들면 다음과 같습니다.
   
   ```json
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
   ```
   
   > [!NOTE]
   > 복잡한 개체에는 복잡한 개체의 값을 참조하는 식이 포함될 수 없습니다. 이 목적을 위해서는 별도의 변수를 정의합니다.
   > 
   > 
   
     복잡한 개체를 변수로 사용하는 고급 예제를 보려면 [Azure Resource Manager 템플릿에서 상태 공유](best-practices-resource-manager-state.md)를 참조하세요.

## <a name="resources"></a>리소스
다음 정보는 리소스로 작업하는 경우 도움이 될 수 있습니다.

* 다른 참가자들이 리소스의 용도를 이해하도록 하려면 템플릿에 각 리소스에 대한 **설명**을 지정합니다.
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* 태그를 사용하여 리소스에 메타데이터를 추가할 수 있습니다. 메타데이터를 사용하여 리소스에 대한 정보를 추가합니다. 예를 들어 리소스에 대한 청구 세부 정보를 기록하기 위해 메타데이터를 추가할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.
* 템플릿에서 *공용 끝점*(예: Azure Blob Storage 공용 끝점)을 사용하는 경우 네임스페이스를 *하드 코딩하지 마세요*. **reference** 함수를 사용하여 네임스페이스를 동적으로 검색합니다. 이 방법을 사용하면 템플릿의 끝점을 수동으로 변경하지 않고 다른 공용 네임스페이스 환경에 템플릿을 배포할 수 있습니다. 템플릿에서 저장소 계정에 사용하는 것과 동일한 버전으로 API 버전을 설정합니다.
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   저장소 계정이 생성 중인 것과 동일한 템플릿에 배포되면 리소스를 참조할 때 공급자 네임스페이스를 지정할 필요가 없습니다. 간단한 구문은 다음과 같습니다.
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   템플릿의 다른 값을 공용 네임스페이스를 사용하도록 구성한 경우 동일한 **reference** 함수를 반영하도록 이러한 값을 변경합니다. 가상 컴퓨터 진단 프로필의 **storageUri** 속성을 설정할 수 있습니다.
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   다른 리소스 그룹에서 기존 저장소 계정을 참조할 수도 있습니다.

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* 응용 프로그램에 필요한 경우에만 공용 IP 주소를 가상 컴퓨터에 할당합니다. 디버깅, 관리 또는 관리 목적으로 가상 컴퓨터(VM)에 연결하려면 인바운드 NAT 규칙, 가상 네트워크 게이트웨이 또는 Jumpbox를 사용합니다.
   
     가상 컴퓨터에 연결하는 방법에 대한 자세한 내용은 다음을 참조하세요.
   
   * [Azure에서 N 계층 아키텍처에 대한 VM 실행](../guidance/guidance-compute-n-tier-vm.md)
   * [Azure Resource Manager에서 VM에 대한 WinRM 액세스 설정](../virtual-machines/windows/winrm.md)
   * [Azure Portal을 사용하여 VM에 대한 외부 액세스 허용](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell을 사용하여 VM에 대한 외부 액세스 허용](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLI를 사용하여 Linux VM에 대한 외부 액세스 허용](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* 공용 IP 주소에 대한 **domainNameLabel** 속성은 고유해야 합니다. **domainNameLabel** 값은 3~63자 사이여야 하고 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` 정규식으로 지정된 규칙을 따라야 합니다. **uniqueString** 함수는 13자 길이의 문자열을 생성하고, **dnsPrefixString** 매개 변수는 50자로 제한됩니다.

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* 사용자 지정 스크립트 확장에 암호를 추가하는 경우 **protectedSettings** 속성에서 **commandToExecute** 속성을 사용합니다.
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > VM 및 확장에 매개 변수로 전달되는 비밀이 암호화되도록 하려면 관련 확장의 **protectedSettings** 속성을 사용합니다.
   > 
   > 

## <a name="outputs"></a>outputs
템플릿을 사용하여 공용 IP 주소를 만드는 경우 IP 주소 및 정규화된 도메인 이름(FQDN)의 세부 정보를 반환하는 **outputs** 섹션을 포함합니다. 출력 값을 사용하여 배포 후 공용 IP 주소 및 FQDN에 대한 세부 정보를 쉽게 검색할 수 있습니다. 리소스를 참조할 때 이러한 섹션을 만드는 데 사용된 API 버전을 사용합니다. 

```json
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

## <a name="single-template-vs-nested-templates"></a>단일 템플릿 또는 중첩된 템플릿
솔루션을 배포하려면 단일 템플릿 또는 여러 개의 중첩된 템플릿이 있는 주 템플릿 중 하나를 사용할 수 있습니다. 중첩된 템플릿은 좀 더 수준 높은 시나리오에서 일반적입니다. 중첩된 템플릿을 사용하면 다음과 같은 장점을 얻을 수 있습니다.

* 솔루션을 대상 구성 요소로 세분화할 수 있습니다.
* 중첩된 템플릿은 다른 주 템플릿에서 다시 사용할 수 있습니다.

중첩된 템플릿을 사용하도록 선택한 경우 다음 지침이 템플릿 디자인을 표준화하는 데 도움이 될 수 있습니다. 이러한 지침은 [Azure Resource Manager 템플릿 디자인 패턴](best-practices-resource-manager-design-templates.md)을 기준으로 합니다. 다음 템플릿을 포함하는 디자인을 사용하는 것이 좋습니다.

* **주 템플릿** (azuredeploy.json). 입력 매개 변수에 사용합니다.
* **공유 리소스 템플릿**. 다른 모든 리소스(예: 가상 네트워크, 가용성 집합)에서 사용하는 공유 리소스를 배포하는 데 사용합니다. 식 **dependsOn**을 사용하여 다른 템플릿보다 먼저 이 템플릿이 배포되도록 합니다.
* **선택적 리소스 템플릿**. 매개 변수(예: jumpbox)를 기준으로 리소스를 조건부로 배포하는 데 사용합니다.
* **멤버 리소스 템플릿**. 응용 프로그램 계층 내의 각 인스턴스 형식은 자체 구성을 가집니다. 계층 내에서 서로 다른 인스턴스 형식을 정의할 수 있습니다. (예를 들어 첫 번째 인스턴스에서 클러스터를 만들고 추가 인스턴스는 기존 클러스터에 추가합니다.) 각 인스턴스 형식에는 고유한 배포 템플릿이 있습니다.
* **스크립트**. 폭 넓게 재사용할 수 있는 스크립트를 각 인스턴스 형식에 적용할 수 있습니다(예: 추가 디스크 초기화 및 포맷). 특정 사용자 지정 목적으로 생성한 사용자 지정 스크립트는 인스턴스 유형에 따라 다릅니다.

![중첩된 템플릿](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

자세한 내용은 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

## <a name="conditionally-link-to-nested-templates"></a>중첩된 템플릿에 조건부로 연결
매개 변수를 사용하여 중첩된 템플릿에 조건부로 연결할 수 있습니다. 매개 변수는 템플릿에 대한 URI의 일부가 됩니다.

```json
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
```

## <a name="template-format"></a>템플릿 형식
JSON 유효성 검사기를 통해 템플릿을 전달하는 것이 좋습니다. 유효성 검사기를 통해 불필요한 쉼표, 괄호 및 배포 중에 오류를 유발할 수 있는 대괄호를 제거할 수 있습니다. 자주 사용하는 편집 환경을 위해 [JSONLint](http://jsonlint.com/) 또는 linter 패키지를 사용합니다(Visual Studio Code, Atom, Sublime Text, Visual Studio 등).

또한 가독성을 높이기 위해 JSON 형식을 지정하는 것이 좋습니다. 로컬 편집기에 대한 JSON 포맷터 패키지를 사용할 수 있습니다. Visual Studio에서는 **Ctrl+K, Ctrl+D**를 눌러 문서 서식을 지정합니다. Visual Studio Code에서 **Alt+Shift+F**를 누릅니다. 로컬 편집기에서 문서 서식을 지정하지 않는 경우 [온라인 포맷터](https://www.bing.com/search?q=json+formatter)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 가상 컴퓨터를 위한 솔루션 설계에 대한 자세한 내용은 [Azure에서 Windows VM 실행](../guidance/guidance-compute-single-vm.md) 및 [Azure에서 Linux VM 실행](../guidance/guidance-compute-single-vm-linux.md)을 참조하세요.
* 저장소 계정 설정에 대한 자세한 내용은 [Azure Storage 성능 및 확장성 검사 목록](../storage/storage-performance-checklist.md)을 참조하세요.
* 가상 네트워크에 대한 도움말을 보려면 [네트워킹 인프라 지침](../virtual-machines/windows/infrastructure-networking-guidelines.md)을 참조하세요.
* 엔터프라이즈에서 Resource Manager를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대해 자세히 알아보려면 [Azure 엔터프라이즈 스캐폴드: 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.


