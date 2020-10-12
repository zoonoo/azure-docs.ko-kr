---
title: Azure Monitor 통합 문서-JSON 데이터를 JSONPath로 변환
description: Azure Monitor 통합 문서에서 JSONPath를 사용 하 여 쿼리 된 끝점이 반환 하는 JSON 데이터의 결과를 원하는 형식으로 변환 하는 방법입니다.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: efa54933ac7d57ec0dcff9ae11b6fb5c2c87a897
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081392"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>JSONPath를 사용 하 여 통합 문서에서 JSON 데이터를 변환 하는 방법

통합 문서는 여러 원본의 데이터를 쿼리할 수 있습니다. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 또는 사용자 지정 끝점과 같은 일부 끝점은 JSON으로 결과를 반환할 수 있습니다. 쿼리 된 끝점에서 반환 된 JSON 데이터가 원하는 형식으로 구성 되지 않은 경우 JSONPath를 사용 하 여 결과를 변환할 수 있습니다.

JSONPath는 XML에 대 한 XPath와 유사한 JSON에 대 한 쿼리 언어입니다. XPath와 마찬가지로 JSONPath는 JSON 구조에서 데이터를 추출 하 고 필터링 할 수 있도록 합니다.

통합 문서 작성자는 JSONPath 변환을 사용 하 여 JSON을 테이블 구조로 변환할 수 있습니다. 그런 다음 테이블을 사용 하 여 [통합 문서 시각화](workbooks-visualizations.md)를 그릴 수 있습니다.

## <a name="using-jsonpath"></a>JSONPath 사용

1. *편집* 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. 쿼리 추가 *Add*  >  *Add query* 링크를 사용 하 여 통합 문서에 쿼리 컨트롤을 추가 합니다.
3. 데이터 원본을 *JSON*으로 선택 합니다.
4. JSON 편집기를 사용 하 여 다음 JSON 코드 조각을 입력 합니다.
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

위의 JSON 개체가 상점의 재고의 표현으로 제공 된다고 가정 하겠습니다. 작업은 제목, 저자 및 가격을 나열 하 여 상점의 사용 가능한 책 테이블을 만드는 것입니다.

1. *결과 설정* 탭을 선택 하 고 결과 형식을 *JSON 경로*로 전환 합니다.
2. 다음 JSON 경로 설정을 적용 합니다.

    JSON 경로 `$.store.books` 테이블: 이 필드는 테이블 루트의 경로를 나타냅니다. 이 경우 스토어의 책 인벤토리에 주의 해야 합니다. 테이블 경로는 JSON을 책 정보로 필터링 합니다.

   | 열 Id | 열 JSON 경로 |
   |:-----------|:-----------------|
   | 제목      | `$.title`        |
   | 작성자     | `$.author`       |
   | 가격      | `$.price`        |

    열 Id는 열 머리글이 됩니다. 열 JSON 경로 필드는 테이블의 루트에서 열 값 까지의 경로를 나타냅니다.

1. *쿼리 실행* 을 클릭 하 여 위의 설정을 적용 합니다.

![ JSON 데이터 원본 및 JSON 경로 결과 형식을 사용 하 여 쿼리 항목 편집](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>다음 단계
- [통합 문서 개요](workbooks-overview.md)
- [Azure Monitor 통합 문서에 있는 그룹](workbooks-groups.md)
