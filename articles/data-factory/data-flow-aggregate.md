---
title: 데이터 흐름 매핑의 집계 변환-Azure Data Factory | Microsoft Docs
description: 매핑 데이터 흐름 집계 변환을 사용 하 여 Azure Data Factory에서 대규모로 데이터를 집계 하는 방법에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 0201cbdd05cd8aae4afb92b459bf58fb5ff6a142
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026983"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 집계 변환 



집계 변환에서는 데이터 스트림의 열 집계를 정의합니다. 식 작성기를 사용 하면 SUM, MIN, MAX 및 COUNT와 같은 여러 유형의 집계를 정의 하 여 기존 또는 계산 된 열을 기준으로 그룹화 할 수 있습니다.

## <a name="group-by"></a>Group By
기존 열을 선택 하거나 집계에 대해 group by 절로 사용할 새 계산 열을 만듭니다. 기존 열을 사용 하려면 드롭다운에서 원하는 열을 선택 합니다. 새 계산 열을 만들려면 절 위로 마우스를 이동 하 고 ' 계산 열 '을 클릭 합니다. 이렇게 하면 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)가 열립니다. 계산 열을 만들었으면 ' 이름으로 ' 필드 아래에 출력 열 이름을 입력 합니다. 추가 group by 절을 추가 하려는 경우 기존 절 위로 마우스를 이동 하 고 ' + '를 클릭 합니다.

설정 ![별 집계 변환 그룹화](media/data-flow/agg.png "설정 별 집계 변환 그룹화")

> [!NOTE]
> Group by 절은 집계 변환에서 선택 사항입니다.

## <a name="aggregate-column"></a>집계 열 
' 집계 ' 탭을 선택 하 여 집계 식을 작성 합니다. 기존 열을 선택 하 고 집계를 사용 하 여 값을 덮어쓰거나 새 이름을 사용 하 여 새 필드를 만들 수 있습니다. 열 이름 선택기 옆의 오른쪽 상자에 집계 식이 입력 됩니다. 식을 편집 하려면 입력란을 클릭 하 여 식 작성기를 엽니다. 추가 집계를 추가 하려면 기존 식 위로 마우스를 이동 하 고 ' + '를 클릭 하 여 새 집계 열 또는 [열 패턴](concepts-data-flow-column-pattern.md)을 만듭니다.

![집계 변환 집계 설정](media/data-flow/agg2.png "집계 변환 집계 설정")

> [!NOTE]
> 각 집계 식에는 집계 함수가 하나 이상 포함 되어야 합니다.

> [!NOTE]
> 디버그 모드에서 식 작성기는 집계 함수를 사용 하 여 데이터 미리 보기를 생성할 수 없습니다. 집계 변환에 대 한 데이터 미리 보기를 보려면 식 작성기를 닫고 ' 데이터 미리 보기 ' 탭을 통해 데이터를 확인 합니다.

## <a name="reconnect-rows-and-columns"></a>행 및 열 다시 연결
집계 변환은 SQL 집계 select 쿼리와 거의 동일 합니다. Group By 절 또는 집계 함수에 포함 되지 않은 열은 집계 변환의 출력으로 이동 하지 않습니다. 집계 된 행 출력과 함께 포함할 다른 열이 있는 경우 다음 중 하나를 수행 해야 합니다.

* Aggregate 함수를 사용 하 여 Last () 또는 First ()와 같은 추가 열을 포함 합니다.
* [자체 조인 패턴](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)을 사용 하 여 집계 앞에 열을 다시 조인 합니다.

## <a name="next-steps"></a>다음 단계

* [창 변환을](data-flow-window.md) 사용 하 여 창 기반 집계 정의
