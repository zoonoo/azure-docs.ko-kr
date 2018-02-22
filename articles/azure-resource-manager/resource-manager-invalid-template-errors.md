---
title: "잘못된 Azure 템플릿 오류 | Microsoft Docs"
description: "잘못된 템플릿 오류를 해결하는 방법을 설명합니다."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 87bc6e4def624785c5052a9a25f579b022c940ec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="resolve-errors-for-invalid-template"></a>잘못된 템플릿 오류 해결

이 문서에서는 잘못된 템플릿 오류를 해결하는 방법에 대해 설명합니다.

## <a name="symptom"></a>증상

템플릿을 배포할 때 다음 오류가 표시됩니다.

```
Code=InvalidTemplate
Message=<varies>
```

오류 메시지는 오류 유형에 따라 다릅니다.

## <a name="cause"></a>원인

이 오류로 인해 별도의 몇 가지 유형의 오류가 발생할 수 있습니다. 일반적으로 템플릿에는 구문 또는 구조 오류가 있습니다.

## <a name="solution"></a>해결 방법

### <a name="solution-1---syntax-error"></a>해결 방법 1 - 구문 오류

템플릿 유효성 검사 실패를 나타내는 오류 메시지가 표시되면 템플릿 구문에 문제가 있습니다.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

이 오류는 템플릿 식이 복잡할 수 있기 때문에 쉽게 발생합니다. 예를 들어 저장소 계정에 대한 다음 이름 할당에는 대괄호 집합 1개, 함수 3개, 괄호 집합 3개, 작은 따옴표 집합 1개, 속성 1개가 포함됩니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

일치하는 구문을 제공하지 않으면 템플릿에서 의도와는 다른 값을 생성합니다.

이러한 오류 유형을 수신하면 식 구문을 주의 깊게 검토합니다. [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 또는 [Visual Studio Code](resource-manager-vs-code.md)와 같이 구문 오류에 대해 경고할 수 있는 JSON 편집기를 사용하는 것을 고려해 보세요.

### <a name="solution-2---incorrect-segment-lengths"></a>해결 방법 2 - 잘못된 세그먼트 길이

리소스 이름이 올바른 형식이 아닐 경우 다른 잘못된 템플릿 오류가 발생합니다.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

루트 수준 리소스에는 리소스 형식에 포함된 세그먼트보다 이름에 포함된 세그먼트가 1개 더 적어야 합니다. 각 세그먼트는 슬래시로 구분됩니다. 다음 예제에서는 2개 세그먼트가 형식에 있고 1개 세그먼트가 이름에 있으므로 **유효한 이름**입니다.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

하지만 그 다음 예제의 경우 형식과 이름의 세그먼트 수가 같으므로 **유효한 이름이 아닙니다** .

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

자식 리소스의 경우 형식과 이름의 세그먼트 수는 같아야 합니다. 자식 리소스의 이름과 형식 전체에 부모 리소의 이름과 형식이 포함되기 때문에 이 세그먼트 수는 적합합니다. 이에 따라 전체 이름에는 여전히 전체 형식보다 하나가 적은 세그먼트가 있습니다.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

리소스 공급자 간에 적용되는 Resource Manager 형식에서 세그먼트를 제대로 갖추는 것이 까다로울 수 있습니다. 예를 들어 웹 사이트에 리소스 잠금을 적용하려면 4개 세그먼트가 있는 형식이 필요합니다. 따라서 이름에는 다음과 같이 3개 세그먼트가 있습니다.

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>해결 방법 3 - 잘못된 매개 변수

템플릿에서 매개 변수에 허용되는 값을 지정했지만 이러한 값 중 하나가 아닌 값을 제공하는 경우 다음과 비슷한 오류 메시지가 표시됩니다.

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

템플릿에 허용되는 값을 다시 한 번 확인하고 배포 시 값 하나를 제공합니다.

### <a name="solution-4---too-many-target-resource-groups"></a>해결 방법 4 - 너무 많은 대상 리소스 그룹

단일 배포에 6개 이상의 대상 리소스 그룹을 지정하면 이 오류가 표시됩니다. 배포에서 리소스 그룹 수를 통합하거나 일부 템플릿을 별도의 배포로 배포하는 방법을 고려하세요. 자세한 내용은 [둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포](resource-manager-cross-resource-group-deployment.md)를 참조하세요.

### <a name="solution-5---circular-dependency-detected"></a>해결 방법 5 - 순환 종속성이 발견됨

이 오류 메시지는 배포가 시작될 수 없도록 리소스가 서로 종속되어 있는 경우에 표시됩니다. 상호 종속성의 조합은 둘 이상의 리소스가 이미 대기 중인 다른 리소스를 기다리게 만듭니다. 예를 들어 리소스1은 리소스3에 종속되고, 리소스2는 리소스1에 종속되고, 리소스3은 리소스2에 종속됩니다. 일반적으로 이런 문제는 불필요한 종속성을 제거하여 해결할 수 있습니다.
