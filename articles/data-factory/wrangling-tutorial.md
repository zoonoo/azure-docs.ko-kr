---
title: Azure Data Factory에서 랭 글 링 데이터 흐름 시작
description: 랭 글 링 데이터 흐름을 사용 하 여 Azure Data Factory에서 데이터를 준비 하는 방법에 대 한 자습서
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684025"
---
# <a name="prepare-data-with-data-wrangling"></a>데이터 랭 글 링를 사용 하 여 데이터 준비

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data factory의 데이터 랭 글 링를 사용 하면 ADF에서 기본적으로 대화형 파워 쿼리 매시업를 빌드하고 ADF 파이프라인 내에서 대규모로 실행할 수 있습니다.

> [!NOTE]
> ADF의 파워 쿼리 끌어올는 현재 공개 미리 보기로 제공 되는 가능한

## <a name="create-a-power-query-activity"></a>파워 쿼리 활동 만들기

Azure Data Factory에서 파워 쿼리를 만드는 방법에는 두 가지가 있습니다. 한 가지 방법은 더하기 아이콘을 클릭 하 고 팩터리 리소스 창에서 **데이터 흐름** 을 선택 하는 것입니다.

> [!NOTE]
> 이전에는 데이터 랭 글 링 기능이 데이터 흐름 워크플로에 있습니다. 이제 랭 글 링 매시업에서 데이터를 작성 합니다. ```New > Power query```

![팩터리 리소스 창에 파워 쿼리를 보여 주는 스크린샷](media/data-flow/power-query-wrangling.png)

다른 메서드는 파이프라인 캔버스의 작업 창에 있습니다. **파워 쿼리** 아코디언을 열고 **파워 쿼리** 작업을 캔버스로 끌어 놓습니다.

![데이터 랭 글 링 옵션을 강조 표시 하는 스크린샷](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>파워 쿼리 데이터 랭 글 링 작업 작성

파워 쿼리 매시업에 대 한 **원본 데이터 집합** 을 추가 합니다. 기존 데이터 집합을 선택 하거나 새 데이터 집합을 만들 수 있습니다. 싱크 데이터 집합을 선택할 수도 있습니다. 하나 이상의 원본 데이터 집합을 선택할 수 있지만 지금은 싱크가 하나만 허용 됩니다. 싱크 데이터 집합을 선택 하는 것은 선택 사항 이지만 하나 이상의 원본 데이터 집합이 필요 합니다.

![랭 글 링](media/wrangling-data-flow/tutorial4.png)

**만들기** 를 클릭 하 여 파워 쿼리 온라인 매시업 편집기를 엽니다.

![파워 쿼리 온라인 매시업 편집기를 여는 만들기 단추를 보여 주는 스크린샷](media/wrangling-data-flow/tutorial5.png)

코드 없는 데이터 준비를 사용 하 여 랭 글 링 파워 쿼리를 작성 합니다. 사용 가능한 함수 목록은 [변환 함수](wrangling-functions.md)를 참조 하세요. ADF는 M 스크립트를 데이터 흐름 스크립트로 변환 하므로 Azure Data Factory 데이터 흐름 Spark 환경을 사용 하 여 대규모로 파워 쿼리를 실행할 수 있습니다.

![데이터 랭 글 링 파워 쿼리을 작성 하는 프로세스를 보여 주는 스크린샷](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>파워 쿼리 데이터 랭 글 링 작업 실행 및 모니터링

파워 쿼리 활동의 파이프라인 디버그 실행을 실행 하려면 파이프라인 캔버스에서 **디버그** 를 클릭 합니다. 파이프라인을 게시 하면 **이제 트리거가** 마지막으로 게시 된 파이프라인의 주문형 실행을 실행 합니다. 파워 쿼리 파이프라인은 모든 기존 Azure Data Factory 트리거를 사용 하 여 예약할 수 있습니다.

![파워 쿼리 data 랭 글 링 활동을 추가 하는 방법을 보여 주는 스크린샷](media/wrangling-data-flow/tutorial3.png)

**모니터** 탭으로 이동 하 여 트리거된 파워 쿼리 활동 실행의 출력을 시각화 합니다.

![트리거된 랭 글 링 파워 쿼리 작업 실행의 출력을 보여 주는 스크린샷](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>다음 단계

[매핑 데이터 흐름을 만드는](tutorial-data-flow.md)방법에 대해 알아봅니다.
