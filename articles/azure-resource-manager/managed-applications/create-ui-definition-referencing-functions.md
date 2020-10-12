---
title: 함수를 참조 하는 UI 정의 만들기
description: 다른 개체를 참조 하는 Azure Portal에 대 한 UI 정의를 생성할 때 사용할 함수에 대해 설명 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098176"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition 함수 참조

CreateUiDefinition 파일의 속성 또는 컨텍스트에서 출력을 참조할 때 사용할 함수입니다.

## <a name="basics"></a>기본 사항

[기본 사항](create-uidefinition-overview.md#basics) 단계에서 정의 된 요소의 출력 값을 반환 합니다. 요소 이름을이 함수에 대 한 매개 변수로 전달 합니다.

다른 단계에서 요소의 출력 값을 가져오려면 [단계 ()](#steps) 함수를 사용 합니다.

다음 예제에서는 기본 사항 단계의 `clusterName` 요소 출력을 반환합니다.

```json
"[basics('clusterName')]"
```

반환 되는 값은 검색 되는 요소의 유형에 따라 달라 집니다.

## <a name="location"></a>위치

기본 사항 단계 또는 현재 컨텍스트에서 선택한 위치를 반환합니다.

다음 예에서는와 같은 값을 반환 합니다 `"westus"` .

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

기본 단계 또는 현재 컨텍스트에서 선택한 resourceGroup에 대 한 세부 정보를 반환 합니다.

다음 예제가 하는 일:

```json
"[resourceGroup()]"
```

는 다음 속성을 반환 합니다.

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

점 표기법을 사용 하 여 특정 값을 가져올 수 있습니다.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>단계

지정 된 단계에서 요소를 반환 합니다. 이 함수에 대 한 매개 변수로 단계 이름을 전달 합니다. 반환 된 요소에서 특정 속성 값을 가져올 수 있습니다.

기본 사항 단계에서 요소의 출력 값을 가져오려면 [기본 ()](#basics) 함수를 사용 합니다.

다음 예에서는 라는 단계를 반환 합니다 `vmParameters` . 이 단계에서는 라는 요소가 지정 됩니다 `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>subscription

기본 단계 또는 현재 컨텍스트에서 선택한 구독의 속성을 반환 합니다.

다음 예제가 하는 일:

```json
"[subscription()]"
```

는 다음 속성을 반환 합니다.

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>다음 단계

* 포털 인터페이스를 개발 하는 방법에 대 한 소개는 [ Azure 관리 되는 응용 프로그램의 만들기 환경을 위한CreateUiDefinition.js](create-uidefinition-overview.md)를 참조 하세요.
