---
title: 템플릿 구조 및 구문
description: 선언적 JSON 구문을 사용하여 Azure Resource Manager 템플릿의 구조 및 속성을 설명합니다.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 4e8334e4ddfaee52c5d1aa68fb8689fcde0a6cbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459993"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>ARM 템플릿의 구조 및 구문 이해

이 문서에서는 ARM(Azure 리소스 관리자) 템플릿의 구조를 설명합니다. 여기서는 템플릿의 다른 섹션 및 해당 섹션에서 사용할 수 있는 속성을 보여 줍니다.

이 문서는 ARM 템플릿에 익숙한 사용자를 위한 것입니다. 템플릿의 구조에 대한 자세한 정보를 제공합니다. 템플릿을 만드는 과정을 안내하는 단계별 자습서는 [자습서: 첫 번째 Azure 리소스 관리자 템플릿 만들기 및 배포를](template-tutorial-create-first-template.md)참조하세요.

## <a name="template-format"></a>템플릿 형식

가장 간단한 구조의 템플릿에 포함되는 요소는 다음과 같습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| $schema |yes |템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다. 사용하는 버전 번호는 배포 범위와 JSON 편집기의 범위에 따라 다릅니다.<br><br>[Azure 리소스 관리자 도구 확장과 함께 VS 코드를](use-vs-code-to-create-template.md)사용하는 경우 리소스 그룹 배포에 대한 최신 버전을 사용합니다.<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Visual Studio를 포함한 다른 편집기에서는 이 스키마를 처리하지 못할 수 있습니다. 이러한 편집자의 경우 다음을 사용합니다.<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>구독 배포의 경우 다음을 사용합니다.<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>관리 그룹 배포의 경우 다음을 사용합니다.<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>테넌트 배포의 경우 다음을 사용합니다.<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |yes |템플릿의 버전입니다(예: 1.0.0.0). 이 요소에 값을 제공할 수 있습니다. 이 값을 사용하여 템플릿에서 중요한 변경 내용을 문서화할 수 있습니다. 템플릿을 사용하여 리소스를 배포할 때 이 값을 사용하면 정확한 템플릿이 사용되도록 할 수 있습니다. |
| apiProfile |예 | 리소스 형식에 대 한 API 버전의 컬렉션으로 사용 되는 API 버전입니다. 템플릿의 각 리소스에 대한 API 버전을 지정하지 않아도 하려면 이 값을 사용합니다. API 프로필 버전을 지정하고 리소스 유형에 대한 API 버전을 지정하지 않으면 Resource Manager는 프로필에 정의된 해당 리소스 유형에 대해 API 버전을 사용합니다.<br><br>API 프로필 속성은 Azure 스택 및 전역 Azure와 같은 다른 환경에 템플릿을 배포할 때 특히 유용합니다. API 프로필 버전을 사용하여 템플릿이 두 환경에서 지원되는 버전을 자동으로 사용하도록 합니다. 현재 API 프로필 버전 및 프로필에 정의된 리소스 API 버전 목록은 [API 프로필](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)을 참조하십시오.<br><br>자세한 내용은 [API 프로필을 사용하여 버전 추적을](templates-cloud-consistency.md#track-versions-using-api-profiles)참조하십시오. |
| [매개 변수](#parameters) |예 |배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값입니다. |
| [변수](#variables) |예 |템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값입니다. |
| [함수](#functions) |예 |템플릿 내에서 사용할 수 있는 사용자 정의 함수입니다. |
| [리소스](#resources) |yes |리소스 그룹 또는 구독에 배포되거나 업데이트되는 리소스 종류입니다. |
| [출력](#outputs) |예 |배포 후 반환되는 값입니다. |

각 요소에는 사용자가 설정할 수 있는 속성이 있습니다. 이 기사에서는 템플릿의 섹션에 대해 자세히 설명합니다.

## <a name="parameters"></a>매개 변수

템플릿의 매개 변수 섹션에서는 리소스를 배포할 때 입력할 수 있는 값을 지정합니다. 템플릿에서 매개 변수는 256개로 제한됩니다. 여러 속성을 포함하는 객체를 사용하여 매개 변수 수를 줄일 수 있습니다.

매개 변수에 사용할 수 있는 속성은 다음과 같습니다.

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| 매개 변수 이름 |yes |매개 변수의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| type |yes |매개 변수 값의 유형입니다. 허용되는 유형 및 값은 **string**, **securestring**, **int**, **bool**, **object**, **secureObject** 및 **array**입니다. [데이터 형식을](#data-types)참조하십시오. |
| defaultValue |예 |매개 변수 값을 제공하지 않는 경우 매개 변수의 기본값입니다. |
| allowedValues |예 |올바른 값을 제공하도록 매개 변수에 대해 허용되는 값의 배열입니다. |
| minValue |예 |Int 형식 매개 변수의 최소값이며, 이 값이 포함됩니다. |
| maxValue |예 |Int 형식 매개 변수의 최대값이며, 이 값이 포함됩니다. |
| minLength |예 |string, securestring 및 array 형식 매개 변수의 최소 길이이며, 이 값이 포함됩니다. |
| maxLength |예 |string, securestring 및 array 형식 매개 변수의 최대 길이이며, 이 값이 포함됩니다. |
| description |예 |포털에서 사용자에게 표시되는 매개 변수의 설명입니다. 자세한 내용은 [템플릿의 주석](#comments)을 참조하세요. |

매개 변수를 사용하는 방법의 예는 [Azure 리소스 관리자 템플릿의 매개 변수를](template-parameters.md)참조하십시오.

### <a name="data-types"></a>데이터 형식

인라인 매개 변수로 전달된 정수의 경우 배포에 사용하는 SDK 또는 명령줄 도구에 의해 값 범위가 제한될 수 있습니다. 예를 들어 PowerShell을 사용하여 템플릿을 배포하는 경우 정수 형식의 범위는 -2147483648에서 2147483647사이입니다. 이러한 제한을 방지하려면 [매개 변수 파일에](parameter-files.md)큰 정수 값을 지정합니다. 리소스 형식은 정수 속성에 대해 고유한 제한을 적용합니다.

템플릿에서 부울 및 정수 값을 지정할 때 따옴표로 값을 둘러싸지 마십시오. 이중 따옴표로 문자열 값을 시작하고 종료합니다.

오브젝트는 왼쪽 중괄호로 시작하여 오른쪽 중괄호로 끝납니다. 배열은 왼쪽 대괄호로 시작하여 오른쪽 대괄호로 끝납니다.

리소스 배포 후에는 보안 문자열 및 보안 개체를 읽을 수 없습니다.

데이터 형식 의 샘플은 [매개 변수 형식 형식을](parameter-files.md#parameter-type-formats)참조하십시오.

## <a name="variables"></a>변수

변수 섹션에서 템플릿을 통해 사용할 수 있는 값을 생성합니다. 변수를 정의할 필요는 없지만 종종 변수를 통해 복잡한 식을 줄이면 템플릿이 단순화됩니다.

다음 예제에서는 변수를 정의하는 데 사용할 수 있는 옵션을 보여 주며 있습니다.

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

변수에 대한 `copy` 여러 값을 만드는 데 사용하는 것에 대한 자세한 내용은 [변수 반복](copy-variables.md)을 참조하십시오.

변수를 사용하는 방법의 예는 [Azure 리소스 관리자 템플릿의 변수를](template-variables.md)참조하십시오.

## <a name="functions"></a>함수

템플릿 내에서 함수를 직접 만들 수 있습니다. 이러한 함수는 템플릿에서 사용할 수 있습니다. 일반적으로 템플릿 전체에서 반복하지 않으려는 복잡한 식을 정의합니다. 템플릿에서 지원되는 식 및 [함수](template-functions.md)에서 사용자 정의 함수를 만듭니다.

사용자 함수를 정의할 때는 다음과 같은 몇 가지 제한 사항이 있습니다.

* 함수는 변수에 액세스할 수 없습니다.
* 함수는 함수에 정의된 매개 변수만 사용할 수 있습니다. 사용자 정의 함수 내에서 [매개 변수 함수를](template-functions-deployment.md#parameters) 사용하면 해당 함수의 매개 변수로 제한됩니다.
* 함수는 다른 사용자 정의 함수를 호출할 수 없습니다.
* 함수는 [참조 함수](template-functions-resource.md#reference)를 사용할 수 없습니다.
* 함수의 매개 변수는 기본값을 가질 수 없습니다.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| namespace |yes |사용자 지정 함수의 네임스페이스입니다. 템플릿 함수와 의 이름 충돌을 방지하는 데 사용합니다. |
| 함수 이름 |yes |사용자 지정 함수의 이름입니다. 함수를 호출할 때 함수 이름을 네임스페이스와 결합합니다. 예를 들어 namespace contoso에서 uniqueName이라는 함수를 `"[contoso.uniqueName()]"`호출하려면 을 사용합니다. |
| 매개 변수 이름 |예 |사용자 지정 함수 내에서 사용할 매개 변수의 이름입니다. |
| 매개 변수 값 |예 |매개 변수 값의 유형입니다. 허용되는 유형 및 값은 **string**, **securestring**, **int**, **bool**, **object**, **secureObject** 및 **array**입니다. |
| 출력 유형 |yes |출력 값의 유형입니다. 출력 값은 함수 입력 매개 변수와 동일한 형식을 지원합니다. |
| 출력 값 |yes |함수에서 평가되고 반환되는 템플릿 언어 식입니다. |

사용자 지정 함수를 사용하는 방법의 예는 [Azure 리소스 관리자 템플릿에서 사용자 정의 함수를](template-user-defined-functions.md)참조하십시오.

## <a name="resources"></a>리소스

리소스 섹션에서 배포되거나 업데이트되는 리소스를 정의합니다.

다음과 같은 구조를 사용하여 리소스를 정의합니다.

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| condition(조건) | 예 | 리소스가 이 배포 중 프로비전되는지 여부를 나타내는 부울 값입니다. `true`인 경우 리소스는 배포하는 동안 만들어집니다. `false`인 경우 리소스는 이 배포에 대해 건너뛰어집니다. [조건을](conditional-resource-deployment.md)참조하십시오. |
| type |yes |리소스 유형입니다. 이 값은 리소스 공급자의 네임스페이스와 리소스 형식을 조합한 값입니다(예: **Microsoft.Storage/storageAccounts**). 사용 가능한 값을 확인하려면 [템플릿 참조](/azure/templates/)를 참조하십시오. 자식 리소스의 경우 형식형식은 상위 리소스 내에 중첩되는지 또는 상위 리소스 외부에서 정의된지 여부에 따라 달라집니다. [자식 리소스에 대한 이름 및 형식 설정](child-resource-name-type.md)을 참조하세요. |
| apiVersion |yes |리소스를 만들 때 사용하는 REST API의 버전입니다. 사용 가능한 값을 확인하려면 [템플릿 참조](/azure/templates/)를 참조하십시오. |
| name |yes |리소스의 이름입니다. 이 이름은 RFC3986에 정의된 URI 구성 요소 제한을 따라야 합니다. 외부 당사자에 리소스 이름을 노출하는 Azure 서비스는 다른 ID를 스푸칭하려는 시도가 아닌지 확인하기 위해 이름의 유효성을 검사합니다. 자식 리소스의 경우 이름의 형식은 상위 리소스 내에 중첩되는지 또는 상위 리소스 외부에서 정의된지 여부에 따라 달라집니다. [자식 리소스에 대한 이름 및 형식 설정](child-resource-name-type.md)을 참조하세요. |
| comments |예 |템플릿에서 리소스를 문서화하는 내용에 대한 참고입니다. 자세한 내용은 [템플릿의 주석](template-syntax.md#comments)을 참조하세요. |
| 위치 |다양함 |제공된 리소스의 지역적 위치를 지원합니다. 사용 가능한 위치 중 하나를 선택할 수 있지만 대개는 사용자에게 가까운 하나를 선택하는 것이 좋습니다. 일반적으로 동일한 지역에서 서로 상호 작용하도록 리소스를 배치하는 것도 좋습니다. 대부분의 리소스 종류에는 위치가 필요하지만 일부 종류(예: 역할 할당)에는 위치가 필요하지 않습니다. [리소스 위치 설정을](resource-location.md)참조하십시오. |
| dependsOn |예 |이 리소스를 배포하기 전에 배포해야 하는 리소스입니다. Resource Manager는 리소스 간의 종속성을 평가한 후 올바른 순서에 따라 리소스를 배포합니다. 리소스는 서로 종속되지 않을 경우, 병렬로 배포됩니다. 이 값은 리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록입니다. 이 템플릿에 배포된 리소스만 나열합니다. 이 템플릿에 정의되지 않은 리소스는 이미 존재해야 합니다. 불필요한 종속성은 배포 속도를 느리게 만들고 순환 종속성을 만들기 때문에 추가하지 않습니다. 종속성 설정에 대한 지침은 [Azure Resource Manager 템플릿에서 종속성 정의](define-resource-dependency.md)를 참조하세요. |
| tags |예 |리소스와 연결된 태그입니다. 태그를 적용하여 구독에서 리소스를 논리적으로 구성합니다. |
| sku | 예 | 일부 리소스에서는 SKU를 정의하는 값을 허용합니다. 예를 들어 스토리지 계정에 대한 중복 유형을 지정할 수 있습니다. |
| kind | 예 | 일부 리소스에서는 배포하는 리소스 종류를 정의하는 값을 허용합니다. 예를 들어 만들 Cosmos DB 종류를 지정할 수 있습니다. |
| copy |예 |인스턴스가 둘 이상 필요한 경우 만드는 리소스의 수입니다. 기본 모드는 병렬입니다. 모든 리소스를 동시에 배포하지 않으려면 직렬 모드를 지정합니다. 자세한 내용은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](copy-resources.md)를 참조하세요. |
| 계획 | 예 | 일부 리소스에서는 배포할 계획을 정의하는 값을 허용합니다. 예를 들어 가상 머신에 대한 마켓플레이스 이미지를 지정할 수 있습니다. |
| properties |예 |리소스별 구성 설정입니다. 속성의 값은 리소스를 만들기 위해 REST API 작업(PUT 메서드)에 대한 요청 본문에 제공하는 값과 동일합니다. 복사 배열을 지정하여 속성의 여러 인스턴스를 만들 수도 있습니다. 사용 가능한 값을 확인하려면 [템플릿 참조](/azure/templates/)를 참조하십시오. |
| 리소스 |예 |정의 중인 리소스에 종속되는 하위 리소스입니다. 부모 리소스의 스키마에서 허용되는 리소스 유형만 제공합니다. 부모 리소스에 대한 종속성은 암시되지 않습니다. 해당 종속성을 명시적으로 정의해야 합니다. [자식 리소스에 대한 이름 및 형식 설정](child-resource-name-type.md)을 참조하세요. |

## <a name="outputs"></a>outputs

Outputs 섹션에서, 배포에서 반환되는 값을 지정합니다. 일반적으로 배포된 리소스에서 값을 반환합니다.

다음 예제에서는 출력 정의의 구조를 보여줍니다.

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| 출력 이름 |yes |출력 값의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| condition(조건) |예 | 이 출력 값의 반환 여부를 나타내는 부울 값입니다. `true`이면 해당 값이 배포의 출력에 포함됩니다. `false`이면 이 배포에 대한 출력 값을 건너뜁니다. 지정하지 않으면 기본값은 `true`입니다. |
| type |yes |출력 값의 유형입니다. 출력 값은 템플릿 입력 매개 변수와 동일한 유형을 지원합니다. 출력 유형에 대한 **securestring을** 지정하면 배포 기록에 값이 표시되지 않으며 다른 템플릿에서 검색할 수 없습니다. 두 개 이상의 템플릿에서 비밀 값을 사용하려면 키 볼트에 비밀을 저장하고 매개 변수 파일의 비밀을 참조합니다. 자세한 내용은 [Azure 키 자격 증명 모음 을 사용하여 배포 하는 동안 보안 매개 변수 값을 전달](key-vault-parameter.md)합니다. |
| value |예 |출력 값으로 계산되어 반환되는 템플릿 언어 식입니다. **값** 또는 **복사**를 지정합니다. |
| copy |예 | 출력에 대해 두 개 이상의 값을 반환하는 데 사용됩니다. **값** 또는 **복사를**지정합니다. 자세한 내용은 [Azure 리소스 관리자 템플릿의 출력 반복을](copy-outputs.md)참조하십시오. |

출력을 사용하는 방법의 예는 [Azure 리소스 관리자 템플릿의 출력을](template-outputs.md)참조하십시오.

<a id="comments" />

## <a name="comments-and-metadata"></a>주석 및 메타데이터

템플릿에 주석 및 메타데이터를 추가하는 몇 가지 옵션이 있습니다.

### <a name="comments"></a>주석

인라인 주석의 경우 `//` 사용하거나 `/* ... */` 사용할 수 있지만 이 구문은 모든 도구에서 작동하지 않습니다. 포털 템플릿 편집기를 사용하여 인라인 주석이 있는 템플릿에서 작업할 수 없습니다. 이 주석 스타일을 추가하는 경우 사용하는 도구가 반드시 인라인 JSON 주석을 지원해야 합니다.

> [!NOTE]
> Azure CLI를 사용하여 주석이 있는 템플릿을 `--handle-extended-json-format` 배포하려면 스위치를 사용해야 합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Visual Studio 코드에서 [Azure 리소스 관리자 도구 확장은](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) 리소스 관리자 템플릿을 자동으로 검색하고 그에 따라 언어 모드를 변경할 수 있습니다. VS 코드의 오른쪽 하단에 **Azure 리소스 관리자 템플릿이** 표시되면 인라인 주석을 사용할 수 있습니다. 그러면 인라인 주석이 더 이상 유효하지 않음으로 표시되지 않습니다.

![비주얼 스튜디오 코드 Azure 리소스 관리자 템플릿 모드](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>메타데이터

`metadata` 개체는 템플릿의 거의 모든 위치에 추가할 수 있습니다. Azure Resource Manager는 해당 개체를 무시하지만 JSON 편집기가 해당 속성이 유효하지 않음을 경고할 수 있습니다. 개체 내에 필요한 속성을 정의합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

**매개 변수**에 대해 `description` 속성과 함께 `metadata` 개체를 추가합니다.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

포털을 통해 템플릿을 배포하는 경우 설명에 제공하는 텍스트가 자동으로 해당 매개 변수에 대한 팁으로 사용됩니다.

![매개 변수 팁 표시](./media/template-syntax/show-parameter-tip.png)

**리소스**에 대해 `comments` 요소 또는 메타데이터 개체를 추가합니다. 다음 예제에서는 주석 요소와 메타데이터 개체를 모두 보여 줍니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

**outputs**의 경우 출력 값에 메타데이터 개체를 추가합니다.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

사용자 정의 함수에 메타데이터 개체를 추가할 수 없습니다.

## <a name="multi-line-strings"></a>다중 줄 문자열

문자열을 여러 줄로 나누면 됩니다. 예를 들어 다음 JSON 예제의 위치 속성 및 주석 중 하나를 참조하십시오.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Azure CLI를 사용하여 여러 줄 문자열이 있는 템플릿을 `--handle-extended-json-format` 배포하려면 스위치를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](template-functions.md)를 참조하세요.
* 배포 중 여러 템플릿을 결합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](linked-templates.md)을 참조하세요.
* 템플릿을 만드는 방법에 대한 권장 사항은 [Azure Resource Manager 템플릿 모범 사례](template-best-practices.md)를 참조하세요.
* 모든 Azure 환경 및 Azure Stack에서 사용할 수 있는 Resource Manager 템플릿을 만드는 방법에 대한 권장 사항은 [클라우드 일관성을 위한 Azure Resource Manager 템플릿 개발](templates-cloud-consistency.md)을 참조하세요.
