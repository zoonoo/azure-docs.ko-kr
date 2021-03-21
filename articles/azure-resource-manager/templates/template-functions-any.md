---
title: 템플릿 함수-any
description: Bicep에서 형식을 변환 하는 데 사용할 수 있는 함수에 대해 설명 합니다.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745853"
---
# <a name="any-function-for-bicep"></a>Bicep에 대 한 Any 함수

Bicep `any()` 는 Bicep 형식 시스템의 형식 오류를 해결 하기 위해 라는 함수를 지원 합니다. 제공 하는 값의 형식이 시스템에서 예상 하는 형식과 일치 하지 않는 경우이 함수를 사용 합니다. 예를 들어 속성에 숫자가 필요 하지만와 같이 문자열로 제공 해야 하는 경우 `'0.5'` 함수를 사용 `any()` 하 여 형식 시스템에서 보고 하는 오류를 표시 하지 않습니다.

이 함수는 Azure Resource Manager 템플릿 런타임에 존재 하지 않습니다. Bicep 에서만 사용 되며, 빌드된 템플릿에 대 한 JSON에서 내보내지 않습니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>any

`any(value)`

모든 데이터 형식과 호환 되는 값을 반환 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| 값 | 예 | 모든 형식 | 호환 되는 형식으로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

모든 데이터 형식과 호환 되는 폼의 값입니다.

### <a name="examples"></a>예제

다음 예제 템플릿에서는 함수를 사용 하 여 숫자 값을 문자열로 제공 하는 방법을 보여 줍니다 `any()` .

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

함수는 Bicep에서 할당 된 값에 대해 작동 합니다. 다음 예에서는를 `any()` 삼항 식에 인수로 사용 합니다.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>다음 단계

함수를 좀 더 복잡 하 게 사용 하려면 `any()` 다음 예제를 참조 하세요.

* [특정 이름을 필요로 하는 자식 리소스](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [리소스 속성이 있는 경우에도 리소스의 형식에서 정의 되지 않습니다.](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

