---
title: Azure Data Factory Mapping Data Flow 조회 변환
description: Azure Data Factory Mapping Data Flow 조회 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029332"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory Mapping Data Flow 조회 변환



조회를 사용하여 다른 원본의 참조 데이터를 Data Flow에 추가합니다. 조회 변환에는 참조 테이블을 가리키고 키 필드가 일치하는 정의된 원본이 필요합니다.

![조회 변환](media/data-flow/lookup1.png "조회")

수신 스트림 필드와 참조 원본 필드 간의 일치에 사용할 키 필드를 선택합니다. Data Flow 디자인 캔버스에서 조회의 오른쪽 항목으로 사용할 새 원본을 먼저 만들어야 합니다.

일치 항목이 발견되면 참조 원본의 결과 행과 열이 데이터 흐름에 추가됩니다. Data Flow의 끝에서 싱크에 포함할 관심 필드를 선택할 수 있습니다.

## <a name="match--no-match"></a>일치/일치 하지 않음

조회 변환 후에는 다음 변환을 사용 하 여 식 @no__t 함수를 사용 하 여 각 일치 행의 결과를 검사할 수 있습니다.-0은 조회를 통해 행이 일치 하는지 여부에 따라 논리를 추가로 선택할 수 있도록 합니다.

## <a name="optimizations"></a>최적화

Data Factory에서 데이터 흐름은 스케일 아웃 Spark 환경에서 실행 됩니다. 데이터 집합을 작업자 노드 메모리 공간에 맞출 수 있는 경우 조회 성능을 최적화할 수 있습니다.

![브로드캐스트 조인](media/data-flow/broadcast.png "브로드캐스트 조인")

### <a name="broadcast-join"></a>브로드캐스트 조인

왼쪽 및/또는 오른쪽 브로드캐스트 조인을 선택 하 여 ADF를 요청 하 여 조회 관계의 양쪽에 있는 전체 데이터 집합을 메모리에 푸시합니다.

### <a name="data-partitioning"></a>데이터 분할

조회 변환의 최적화 탭에서 "분할 설정"을 선택 하 여 데이터 분할을 지정할 수도 있습니다 .이를 통해 작업자 당 메모리에 더 적합 한 데이터 집합을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

[조인](data-flow-join.md) 및 [존재](data-flow-exists.md) 변환은 ADF 매핑 데이터 흐름에서 유사한 작업을 수행 합니다. 다음으로 이러한 변환을 살펴보세요.
