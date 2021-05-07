---
title: 포털 창의 CreateUiDefinition.json 파일
description: Azure Portal의 사용자 인터페이스 정의를 만드는 방법을 설명합니다. Azure Managed Applications를 정의할 때 사용합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: tomfitz
ms.openlocfilehash: 586237c6dd909312780163cf316220d2f3fddd8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641650"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Azure 관리되는 애플리케이션의 만들기 환경을 위한 CreateUiDefinition.json

이 문서에서는 **createUiDefinition.json** 파일의 핵심 개념을 소개합니다. Azure Portal에서 이 파일을 사용하여 관리형 애플리케이션을 만들 때 사용자 인터페이스를 정의합니다.

템플릿은 다음과 같습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

`CreateUiDefinition`에는 항상 다음 세 개의 속성이 포함됩니다.

* handler(핸들러)
* 버전
* 매개 변수

처리기는 항상 `Microsoft.Azure.CreateUIDef`이어야 하며, 지원되는 최신 버전은 `0.1.2-preview`여야 합니다.

매개 변수 속성의 스키마는 지정된 처리기와 버전의 조합에 따라 다릅니다. 관리형 애플리케이션의 경우 지원되는 속성은 `config`, `basics`, `steps`, `outputs`입니다. `basics` 단계의 기본 동작을 재정의해야 하는 경우에만 `config`를 사용합니다. basics 및 steps 속성에는 Azure Portal에서 표시할 [요소](create-uidefinition-elements.md)(예: 텍스트 상자 및 드롭다운)가 포함됩니다. outputs 속성은 지정된 요소의 출력값을 Azure Resource Manager 템플릿의 매개 변수에 매핑하는 데 사용됩니다.

`$schema`를 포함하는 것이 좋지만 선택 사항입니다. 지정하는 경우 `version`의 값은 `$schema` URI 내의 버전과 일치해야 합니다.

JSON 편집기를 사용하여 createUiDefinition을 만든 다음 [createUiDefinition 샌드박스](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)에서 테스트하여 미리 볼 수 있습니다. 샌드박스에 관한 자세한 내용은 [Azure Managed Applications의 포털 인터페이스 테스트](test-createuidefinition.md)를 참조하세요.

## <a name="config"></a>Config

`config` 속성은 선택 사항입니다. 기본 단계의 기본 동작을 재정의하거나 인터페이스를 단계별 마법사로 설정하는 데 사용합니다. `config`가 사용되는 경우 **createUiDefinition.json** 파일에 있는 `parameters` 섹션의 첫 번째 속성입니다. 다음 예제에서는 사용할 수 있는 속성을 보여 줍니다.

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(subscription().displayName, 'Test'))]",
                        "message": "Can't use test subscription."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualmachines/write",
                        "message": "Must have write permission for the virtual machine."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualMachines/extensions/write",
                        "message": "Must have write permission for the extension."
                    }
                ]
            },
            "resourceProviders": [
                "Microsoft.Compute"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(resourceGroup().name, 'test'))]",
                        "message": "Resource group name can't contain 'test'."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

`isValid` 속성의 경우 true 또는 false로 확인되는 식을 작성합니다. `permission` 속성의 경우 [리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md) 중 하나를 지정합니다.

### <a name="wizard"></a>마법사

`isWizard` 속성을 사용하면 다음 단계로 진행하기 전에 각 단계의 성공적인 유효성 검사를 요구할 수 있습니다. `isWizard` 속성이 지정되지 않으면 기본값은 **false** 이며 단계별 유효성 검사가 필요하지 않습니다.

`isWizard`가 사용되고 **true** 로 설정되면 **기본** 탭을 사용할 수 있으며 다른 모든 탭은 사용하지 않게 설정됩니다. **다음** 단추를 선택하면 탭의 아이콘이 탭의 유효성 검사가 통과했는지 아니면 실패했는지를 나타냅니다. 탭의 필수 필드를 완료하고 유효성을 검사하고 나면 **다음** 단추를 사용하여 다음 탭으로 이동할 수 있습니다. 모든 탭이 유효성 검사를 통과하면 **검토 및 만들기** 페이지로 이동하고 **만들기** 단추를 선택하여 배포를 시작할 수 있습니다.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="탭 마법사":::

### <a name="override-basics"></a>기본 사항 재정의

기본 구성에서는 기본 단계를 사용자 지정할 수 있습니다.

`description`에는 리소스를 설명하는 markdown 사용 문자열을 제공합니다. 여러 줄 형식 및 링크가 지원됩니다.

`subscription` 및 `resourceGroup` 요소를 사용하면 추가 유효성 검사를 지정할 수 있습니다. 유효성 검사를 지정하는 구문은 [텍스트 상자](microsoft-common-textbox.md)의 사용자 지정 유효성 검사와 같습니다. 구독 또는 리소스 그룹에 대한 `permission` 유효성 검사도 지정할 수 있습니다.  

구독 제어에서는 리소스 공급자 네임스페이스의 목록을 수락합니다. 예를 들어 **Microsoft.Compute** 를 지정할 수 있습니다. 사용자가 리소스 공급자를 지원하지 않는 구독을 선택하면 오류 메시지가 표시됩니다. 리소스 공급자가 해당 구독에 등록되어 있지 않고 사용자에게 리소스 공급자를 등록할 권한이 없는 경우 오류가 발생합니다.  

리소스 그룹 제어에는 `allowExisting`의 옵션이 있습니다. `true`이면 이미 리소스가 있는 리소스 그룹을 선택할 수 있습니다. 이 플래그는 기본 동작에 따라 사용자가 새 리소스 그룹이나 빈 리소스 그룹을 선택해야 하는 솔루션 템플릿에 가장 적합합니다. 대부분의 다른 시나리오에서는 이 속성을 지정할 필요가 없습니다.  

`location`에는 재정의할 위치 제어의 속성을 지정합니다. 재정의되지 않은 모든 속성은 기본값으로 설정됩니다. `resourceTypes`는 정규화된 리소스 종류 이름을 포함하는 문자열 배열을 허용합니다. 위치 옵션은 리소스 종류를 지원하는 지역으로만 제한됩니다.  `allowedValues`  는 지역 문자열 배열을 허용합니다. 해당 지역만 드롭다운에 표시됩니다.`allowedValues` 와 `resourceTypes`를 모두 설정할 수 있습니다. 결과적으로 두 목록의 교집합이 표시됩니다. 마지막으로 `visible` 속성을 사용하여 위치 드롭다운을 조건부 또는 완전히 사용하지 않게 설정할 수 있습니다.  

## <a name="basics"></a>기본 사항

**기본** 단계는 Azure Portal에서 파일을 구문 분석할 때 생성되는 첫 번째 단계입니다. 기본적으로 기본 단계에서는 사용자가 구독, 리소스 그룹 및 배포 위치를 선택할 수 있습니다.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="기본 기본값":::

이 섹션에서 요소를 추가할 수 있습니다. 가능하면 클러스터 또는 관리자 자격 증명의 이름과 같은 배포 전체 매개 변수를 쿼리하는 요소를 추가합니다.

다음 예제에서는 기본 요소에 추가된 텍스트 상자를 보여 줍니다.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>단계

단계 속성에는 기본 다음에 표시할 0개 이상의 단계가 포함되어 있습니다. 각 단계에는 하나 이상의 요소가 포함되어 있습니다. 배포할 애플리케이션의 역할별 또는 계층별로 단계를 추가하는 것이 좋습니다. 예를 들어 주 노드 입력 단계와 클러스터의 작업자 노드에 관한 단계를 추가합니다.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>출력

Azure Portal에서는 `outputs` 속성을 사용하여 `basics` 및 `steps`의 요소를 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑합니다. 이 사전의 키는 템플릿 매개 변수의 이름이며, 값은 참조되는 요소에 있는 출력 개체의 속성입니다.

관리되는 애플리케이션 리소스 이름을 설정하려면 출력 속성에 `applicationResourceName`이라는 값을 포함해야 합니다. 이 값을 설정하지 않으면 애플리케이션은 이름에 대해 GUID를 할당합니다. 사용자 이름을 요청하는 사용자 인터페이스에 텍스트 상자를 포함할 수 있습니다.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>리소스 종류

배포할 리소스 종류를 지원하는 위치로만 사용 가능한 위치를 필터링하려면 리소스 종류 배열을 제공합니다. 둘 이상의 리소스 종류를 제공하는 경우 모든 리소스 종류를 지원하는 위치만 반환됩니다. 이 속성은 선택 사항입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition은 요소의 입력과 출력 및 조건과 같은 기능을 사용하기 위한 [함수](create-uidefinition-functions.md)를 제공합니다. 이 함수는 구문과 기능 면에서 Azure Resource Manager 템플릿 함수와 비슷합니다.

## <a name="next-steps"></a>다음 단계

createUiDefinition.json 파일 자체에는 간단한 스키마가 있습니다. 실제 수준의 모든 지원되는 요소와 기능을 통해 제공됩니다. 이러한 항목은 다음에 자세히 설명되어 있습니다.

- [요소](create-uidefinition-elements.md)
- [함수](create-uidefinition-functions.md)

createUiDefinition에 대한 현재 JSON 스키마는 `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`에 사용할 수 있습니다.

예제 사용자 인터페이스 파일의 경우 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)을 참조하세요.
