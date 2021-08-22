---
title: 매핑 데이터 흐름의 통합 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 매핑 데이터 흐름 새 분기 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 04/27/2020
ms.openlocfilehash: 2a81ffa5d73509a5bb47cda8bc2933894b3139f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642798"
---
# <a name="union-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 통합 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

공용 구조체는 여러 데이터 스트림을 하나로 결합하고 해당 스트림의 SQL 공용 구조체를 공용 구조체 변환의 새 출력으로 사용합니다. 조인 키가 없어도 각 입력 스트림의 모든 스키마가 데이터 흐름 내에서 결합됩니다.

원본 데이터와 데이터 흐름에 있는 기존 변환의 스트림을 모두 포함하여 구성된 각 행 옆에 있는 "+" 아이콘을 선택하여 설정 테이블에서 n개의 스트림을 결합할 수 있습니다.

ADF의 매핑 데이터 흐름에서의 통합 변환에 대한 짧은 비디오 연습은 다음과 같습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![통합 변환](media/data-flow/union.png "Union")

이 경우 여러 원본(이 예제에서는 3개의 다른 원본 파일)의 서로 다른 메타데이터를 결합하여 단일 스트림으로 결합할 수 있습니다.

![통합 변환 개요](media/data-flow/union111.png "Union 1")

이렇게 하려면 추가하려는 모든 원본을 포함하여 Union 설정에 추가 행을 추가합니다. 공통 조회 또는 조인 키가 필요하지 않습니다.

![통합 변환 설정](media/data-flow/unionsettings.png "Union 설정")

Union 뒤에 변환 선택을 설정하는 경우 헤더 없는 원본에서 이름이 지정되지 않은 겹치는 필드 또는 필드의 이름을 바꿀 수 있습니다. 이 예제에서는 "검사"를 클릭하여 세 개의 다른 원본에서 가져온 총 132개의 열과 메타데이터 결합을 확인합니다.

![통합 변환 최종](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>이름 및 위치

"이름별 통합"을 선택하면 각 열 값이 새로이 연결된 메타데이터 스키마와 함께 각 원본의 해당 열에 드롭됩니다.

"위치별 통합"을 선택하면 각 열 값이 각 해당 원본의 원래 위치로 이동하여 각 원본의 데이터가 동일한 스트림에 추가되는 새로운 결합 데이터 스트림이 생성됩니다.

![Union 출력](media/data-flow/unionoutput.png "Union 출력")

## <a name="next-steps"></a>다음 단계

[Join](data-flow-join.md) 및 [Exists](data-flow-exists.md)를 비롯한 유사한 변환을 탐색합니다.
