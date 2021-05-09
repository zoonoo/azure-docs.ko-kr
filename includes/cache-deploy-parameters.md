---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67182012"
---
### <a name="cacheskuname"></a>cacheSKUName

새 Azure Cache for Redis의 가격 책정 계층입니다.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

이 매개 변수에 허용되는 값(기본, 표준 또는 프리미엄)을 정의하고, 값이 지정되지 않은 경우에는 기본값(기본)을 할당하는 템플릿입니다. Basic은 최대 53GB를 사용할 수 있는 다양한 크기의 단일 노드를 제공합니다. Standard는 최대 53GB를 사용할 수 있으며 99.9%의 SLA를 제공하는 다양한 크기의 2-노드 주/복제본을 제공합니다.

### <a name="cacheskufamily"></a>cacheSKUFamily

SKU 제품군입니다.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

새 Azure Cache for Redis 인스턴스의 크기입니다.

기본 및 표준 제품군의 경우:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

허용되는 값이 0~6이 아닌 1~5까지 실행되는 경우를 제외하고 프리미엄 값 캐시 용량은 동일하게 정의됩니다.

템플릿은 이 매개 변수에 허용되는 정수 값을 정의합니다(기본 및 표준 제품군의 경우 0~6, 프리미엄 제품군의 경우 1~5). 값을 지정하지 않으면 템플릿은 기본 및 표준에 대해 기본값 0을 할당하고 프리미엄에 대해 1을 할당합니다.

값은 다음 캐시 크기에 해당합니다.

| 값 | 기본 및 표준<br>캐시 크기 | Premium<br>캐시 크기 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250MB(기본값)                 | 해당 없음                   |
| 1     | 1GB                             | 6GB(기본값)        |
| 2     | 2.5GB                           | 13GB                 |
| 3     | 6GB                             | 26GB                 |
| 4     | 13GB                            | 53GB                 |
| 5     | 26GB                            | 120GB                |
| 6     | 53GB                            | 해당 없음                   |
