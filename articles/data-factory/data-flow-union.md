---
title: Azure Data Factory Mapping Data Flow 새 분기 변환
description: Azure Data Factory Mapping Data Flow 새 분기 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35d5b2250cb5f2f5bd5b3a0073dc2e3c655ceccb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029924"
---
# <a name="mapping-data-flow-union-transformation"></a>데이터 흐름 통합 변환 매핑



공용 구조체는 여러 데이터 스트림을 하나로 결합하고 해당 스트림의 SQL 공용 구조체를 공용 구조체 변환의 새 출력으로 사용합니다. 조인 키가 없어도 각 입력 스트림의 모든 스키마가 데이터 흐름 내에 결합 됩니다.

설정 테이블에서 구성 된 각 행 옆에 있는 "+" 아이콘을 선택 하 여 설정 테이블에서 n-수의 스트림을 결합할 수 있습니다. 여기에는 원본 데이터와 데이터 흐름에 있는 기존 변환의 스트림이 모두 포함 됩니다.

![Union 변환](media/data-flow/union.png "union")

이 경우 여러 원본 (이 예제에서는 3 개의 다른 소스 파일)에서 서로 다른 메타 데이터를 결합 하 여 단일 스트림으로 결합할 수 있습니다.

![Union 변환 개요](media/data-flow/union111.png "union 1")

이렇게 하려면 추가 하려는 모든 원본을 포함 하 여 Union 설정에서 추가 행을 추가 합니다. 공통 조회 또는 조인 키가 필요 하지 않습니다.

![Union 변환 설정](media/data-flow/unionsettings.png "union 설정")

공용 구조체 뒤에 Select 변환을 설정 하는 경우 headerless 원본에서 이름이 지정 되지 않은 겹치는 필드 또는 필드의 이름을 바꿀 수 있습니다. "검사"를 클릭 하 여이 예제에서 세 개의 다른 원본에서 메타 데이터를 132 총 열과 결합 합니다.

![Union 변환 최종](media/data-flow/union333.png "공용 구조체 3")

## <a name="name-and-position"></a>이름 및 위치

"이름별로 공용 구조체"를 선택 하면 각 열 값이 새 연결 된 메타 데이터 스키마를 사용 하 여 각 원본의 해당 열에 저장 됩니다.

"위치에 따라 통합"을 선택 하면 각 열 값이 해당 하는 각 원본에서 원래 위치로 이동 하 여 각 소스의 데이터가 동일한 스트림에 추가 되는 새로운 결합 된 데이터 스트림이 생성 됩니다.

![Union output](media/data-flow/unionoutput.png "union 출력")

## <a name="next-steps"></a>다음 단계

[Join](data-flow-join.md) 및 [Exists](data-flow-exists.md)를 비롯 한 유사한 변환을 탐색 합니다.
