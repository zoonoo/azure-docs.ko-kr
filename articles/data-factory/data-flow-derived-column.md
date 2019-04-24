---
title: Azure Data Factory 데이터 흐름 매핑 파생 열 변환
description: Azure 데이터 팩터리 매핑 데이터 흐름 파생 열 변환을 사용 하 여 대규모로 데이터를 변환 하는 방법
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: f53e122eb1b2a5b6dabb9a44aef42394d0c7edb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478722"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>파생 열 변환 데이터 흐름 매핑

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

파생 열 변환을 사용하여 데이터 흐름에 새 열을 생성하거나 기존 필드를 수정합니다.

![열 파생](media/data-flow/dc1.png "파생 열")

단일 파생 열 변환에서 여러 개의 파생 열 작업을 수행할 수 있습니다. 단일 변환 단계에서 두 개 이상의 열을 변환하려면 "열 추가"를 클릭하십시오.

열 필드에서 기존 열을 선택하여 새로 파생된 값으로 덮어쓰거나 "새 열 만들기"를 클릭하여 새로 파생된 값으로 새 열을 생성합니다.

식 텍스트 상자는 식 작성기를 열어 식 함수를 사용하여 파생 열에 대한 식을 빌드할 수 있습니다.

## <a name="column-patterns"></a>열 패턴

열 이름이 변수 원본의 경우 있습니다 빌드 하려는 내부 파생 열 변환을 열 패턴을 사용 하 여 명명 된 열을 사용 하는 대신 합니다. 참조 된 [스키마 드리프트](concepts-data-flow-schema-drift.md) 을 참조 하십시오.

![열 패턴](media/data-flow/columnpattern.png "열 패턴")

## <a name="next-steps"></a>다음 단계

에 대 한 자세한 정보는 [변환에 대 한 Data Factory 식 언어](http://aka.ms/dataflowexpressions) 고 [식 작성기](concepts-data-flow-expression-builder.md)
