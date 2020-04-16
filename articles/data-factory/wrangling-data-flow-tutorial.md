---
title: Azure 데이터 팩터리에서 데이터 흐름 랭글링 시작
description: 랭글링 데이터 흐름을 사용하여 Azure 데이터 팩터리에서 데이터를 준비하는 방법에 대한 자습서
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409001"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>랭글링 데이터 흐름으로 데이터 준비

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>랭글링 데이터 흐름 생성

Azure 데이터 팩터리에서 랭글링 데이터 흐름을 만드는 방법에는 두 가지가 있습니다. 한 가지 방법은 더하기 아이콘을 클릭하고 공장 리소스 창에서 **데이터 흐름을** 선택하는 것입니다.

![막을](media/wrangling-data-flow/tutorial7.png)

다른 방법은 파이프라인 캔버스의 활동 창에 있습니다. 이동 **및 변환** 아코디언을 열고 **데이터 흐름** 활동을 캔버스로 끕니다.

두 방법 모두 열리는 측면 창에서 **새 데이터 흐름 만들기를** 선택하고 **위글링 데이터 흐름을**선택합니다. 확인을 클릭합니다.

![막을](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>랭글링 데이터 흐름 작성

랭글링 데이터 흐름에 대한 **원본 데이터 집합을** 추가합니다. 기존 데이터 집합을 선택하거나 새 데이터 집합을 만들 수 있습니다. 싱크 데이터 집합을 선택할 수도 있습니다. 하나 이상의 원본 데이터 집합을 선택할 수 있지만 현재 는 하나의 싱크만 허용됩니다. 싱크 데이터 집합을 선택하는 것은 선택 사항이지만 하나 이상의 원본 데이터 집합이 필요합니다.

> [!NOTE]
> 제한된 미리 보기에는 ADLS Gen 2 구분된 텍스트만 지원됩니다. 

![막을](media/wrangling-data-flow/tutorial4.png)

**만들기를** 클릭하여 전원 쿼리 온라인 매시업 편집기를 엽니다.

![막을](media/wrangling-data-flow/tutorial5.png)

코드가 없는 데이터 준비를 사용하여 랭글링 데이터 흐름을 작성합니다. 사용 가능한 함수 목록은 [변환 함수를](wrangling-data-flow-functions.md) 참조하십시오./

![막을](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>랭글링 데이터 흐름 실행 및 모니터링

랭글링 데이터 흐름의 파이프라인 디버그 실행을 실행하려면 파이프라인 캔버스에서 **디버그를** 클릭합니다. 데이터 흐름을 게시하면 **Trigger는 이제** 마지막으로 게시된 파이프라인의 온디맨드 실행을 실행합니다. 데이터 흐름을 랭글링하는 것은 모든 기존 Azure 데이터 팩터리 트리거와 함께 예약할 수 있습니다.

![막을](media/wrangling-data-flow/tutorial3.png)

**모니터** 탭으로 이동하여 트리거된 랭글링 데이터 흐름 활동 실행의 출력을 시각화합니다.

![막을](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>다음 단계

[매핑 데이터 흐름을 만드는](tutorial-data-flow.md)방법에 대해 알아봅니다.