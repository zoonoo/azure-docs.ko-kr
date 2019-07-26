---
title: Azure Data Factory Mapping Data Flow 열 패턴
description: 데이터 흐름 매핑 시 Azure Data Factory 열 패턴을 사용 하 여 일반화 된 데이터 변환 패턴 만들기
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 076c3318a68a50e6bd1b4f9f2a4a4b9a034533c6
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68346581"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Azure data factory 매핑 데이터 흐름 열 패턴

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

하드 코드된 열 이름 대신 패턴을 기준으로 템플릿 열을 만들 수 있도록 “열 패턴” 아이디어를 지원하는 Azure Data Factory Data Flow 변환도 있습니다. 식 작성기 내에서이 기능을 사용 하 여 정확한 특정 필드 이름을 요구 하는 대신 변환에 대 한 열과 일치 하는 패턴을 정의할 수 있습니다. 패턴은 특히 텍스트 파일 또는 NoSQL 데이터베이스에서 열을 변경 하는 경우 들어오는 원본 필드가 자주 변경 되는 경우에 유용 합니다. 이 상태를 "스키마 드리프트" 라고도 합니다.

![열 패턴](media/data-flow/columnpattern2.png "열 패턴")

열 패턴은 스키마 드리프트 시나리오와 일반 시나리오 둘 다를 처리하는 데 유용합니다. 각 열 이름을 완전히 알 수 없는 상황에 적합합니다. 열 이름과 열 데이터 형식에 패턴 일치를 적용하고 `name` & `type` 패턴과 일치하는 데이터 스트림의 필드에 대해 해당 작업을 수행할 변환 식을 작성할 수 있습니다.

패턴을 허용하는 변환에 식을 추가하는 경우 “열 패턴 추가”를 선택합니다. 열 패턴을 사용하면 스키마 드리프트 열 일치 패턴이 허용됩니다.

템플릿 열 패턴을 빌드할 때는 식에 `$$`를 사용하여 입력 데이터 스트림에서 일치된 각 필드에 대한 참조를 나타냅니다.

식 작성기 regex 함수 중 하나를 사용하는 경우 나중에 $1, $2, $3...을 사용하여 regex 식에서 일치된 하위 패턴을 참조할 수 있습니다.

열 패턴 시나리오의 예는 일련의 수신 필드에 SUM을 사용하는 경우입니다. 집계 SUM 계산은 집계 변환에 있습니다. 그런 다음 "integer"와 일치 하는 모든 필드 형식의 일치 항목에 대 한 합계를 사용 하 고 $ $를 사용 하 여 식에서 각 일치 항목을 참조할 수 있습니다.

## <a name="match-columns"></a>열 일치
![열 패턴 유형](media/data-flow/pattern2.png "패턴 유형")

열을 기반으로 패턴을 작성 하려면 열 이름, 형식, 스트림 또는 위치를 일치 시키고 식 함수 및 정규식을 사용 하 여 원하는 조합을 사용할 수 있습니다.

![열 위치](media/data-flow/position.png "열 위치")

## <a name="next-steps"></a>다음 단계
데이터 변환에 대 한 ADF 매핑 데이터 흐름 [식 언어](https://aka.ms/dataflowexpressions) 에 대 한 자세한 정보
