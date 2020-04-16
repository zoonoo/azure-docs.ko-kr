---
title: 데이터 흐름 연합 변환 매핑
description: Azure 데이터 팩터리 매핑 데이터 흐름 새 분기 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 3bdf443a052ba8884a361774154f4d68cc180dfb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417830"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure 데이터 팩터리 매핑 데이터 흐름 연합 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

공용 구조체는 여러 데이터 스트림을 하나로 결합하고 해당 스트림의 SQL 공용 구조체를 공용 구조체 변환의 새 출력으로 사용합니다. 각 입력 스트림의 모든 스키마는 조인 키를 가질 필요 없이 데이터 흐름 내부에 결합됩니다.

데이터 흐름의 기존 변환의 스트림뿐만 아니라 소스 데이터를 포함하여 구성된 각 행 옆에 있는 "+" 아이콘을 선택하여 설정 테이블에서 n수의 스트림을 결합할 수 있습니다.

![통합 변환](media/data-flow/union.png "Union")

이 경우 여러 소스의 서로 다른 메타데이터를 결합하고(이 예제에서는 세 개의 서로 다른 원본 파일) 단일 스트림으로 결합할 수 있습니다.

![공용 구조형 변환 개요](media/data-flow/union111.png "유니온 1")

이렇게 하려면 추가하려는 모든 소스를 포함하여 연합 설정에 행을 추가합니다. 일반적인 조회 또는 조인 키가 필요하지 않습니다.

![연합변형 설정](media/data-flow/unionsettings.png "연합설정")

Union 다음에 Select 변환을 설정하면 헤더없는 원본에서 이름이 지정되지 않은 겹치는 필드 나 필드의 이름을 바꿀 수 있습니다. 세 가지 소스에서 이 예제에서 메타데이터 결합과 총 열 132개가 있는 것을 보려면 "검사"를 클릭합니다.

![유니온 변환 최종](media/data-flow/union333.png "유니온 3")

## <a name="name-and-position"></a>이름 및 위치

"이름으로 연합"을 선택하면 각 열 값이 각 원본의 해당 열에 드롭되고 새 연결메타데이터 스키마가 있습니다.

"위치별 결합"을 선택하면 각 열 값이 각 해당 소스의 원래 위치로 떨어지므로 각 소스의 데이터가 동일한 스트림에 추가되는 새 결합된 데이터 스트림이 생성됩니다.

![유니온 출력](media/data-flow/unionoutput.png "공용 구조로 출력")

## <a name="next-steps"></a>다음 단계

[조인](data-flow-join.md) 및 [존재](data-flow-exists.md)를 포함한 유사한 변환을 탐색합니다.
