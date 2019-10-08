---
title: 데이터 흐름 매핑-Azure Data Factory의 파생 열 변환 Microsoft Docs
description: 데이터 흐름 파생 열 변환을 사용 하 여 Azure Data Factory에서 대규모로 데이터를 변환 하는 방법에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026840"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 파생 열 변환

파생 열 변환을 사용하여 데이터 흐름에 새 열을 생성하거나 기존 필드를 수정합니다.

## <a name="derived-column-settings"></a>파생 열 설정

기존 열을 재정의 하려면 열 드롭다운을 통해 선택 합니다. 그렇지 않으면 열 선택 필드를 textbox로 사용 하 고 새 열의 이름을 입력 합니다. 파생 열의 식을 작성 하려면 ' 식 입력 ' 상자를 클릭 하 여 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)를 엽니다.

![파생 열 설정](media/data-flow/dc1.png "파생 열 설정")

파생 열을 더 추가 하려면 기존 파생 열 위로 마우스를 이동 하 고 ' + '를 클릭 합니다. 그런 다음 ' 열 추가 ' 또는 ' 열 패턴 추가 ' 중 하나를 선택 합니다. 열 이름이 원본의 변수인 경우 열 패턴을 유용 하 게 사용할 수 있습니다. 자세한 내용은 [열 패턴](concepts-data-flow-column-pattern.md)을 참조 하세요.

![새 파생 열 선택](media/data-flow/columnpattern.png "새 파생 열 선택")

## <a name="next-steps"></a>다음 단계

- [매핑 데이터 흐름 식 언어](data-flow-expression-functions.md)에 대해 자세히 알아보세요.
