---
title: Azure Data Factory의 랭글링 데이터 흐름 시작
description: 랭글링 데이터 흐름을 사용하여 Azure Data Factory에서 데이터를 준비하는 방법에 대한 자습서
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: cf15d6f669718cca8b99d67a7912d3959d9c191f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732509"
---
# <a name="prepare-data-with-data-wrangling"></a>데이터 랭글링으로 데이터 준비

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

데이터 팩터리의 데이터 랭글링을 사용하면 대화형 파워 쿼리 매시업을 ADF에 기본적으로 빌드할 수 있으며 ADF 파이프라인 내에서 대규모로 실행할 수 있습니다.

> [!NOTE]
> ADF의 파워 쿼리 활동은 현재 공개 미리 보기로 제공됩니다.

## <a name="create-a-power-query-activity"></a>파워 쿼리 활동 만들기

Azure Data Factory에서 파워 쿼리를 만드는 방법에는 두 가지가 있습니다. 한 가지 방법은 더하기 아이콘을 클릭하여 팩터리 리소스 창에서 **Data Flow** 를 선택하는 것입니다.

> [!NOTE]
> 이전에는 데이터 랭글링 기능이 데이터 흐름 워크플로에 있었습니다. 이제 ```New > Power query```에서 데이터 랭글링 매시업을 빌드합니다.

![팩터리 리소스 창의 파워 쿼리 스크린샷.](media/data-flow/power-query-wrangling.png)

다른 방법은 파이프라인 캔버스의 작업 창에 있습니다. **파워 쿼리** 아코디언을 열고 **파워 쿼리** 활동을 캔버스로 끌어 놓습니다.

![데이터 랭글링 옵션을 강조 표시하는 스크린샷.](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>파워 쿼리 데이터 랭글링 활동 작성

파워 쿼리 매시업에 대한 **원본 데이터 세트** 를 추가합니다. 기존 데이터 세트를 선택하거나 새 데이터 세트를 만들 수 있습니다. 싱크 데이터 세트를 선택할 수도 있습니다. 하나 이상의 원본 데이터 세트를 선택할 수 있지만 지금은 싱크가 하나만 허용됩니다. 싱크 데이터 세트 선택은 필수가 아니지만 하나 이상의 원본 데이터 세트 선택은 필수입니다.

![랭글링](media/wrangling-data-flow/tutorial4.png)

**만들기** 를 클릭하여 파워 쿼리 온라인 매시업 편집기를 엽니다.

![파워 쿼리 온라인 메시업 편집기를 여는 만들기 버튼을 보여주는 스크린샷.](media/wrangling-data-flow/tutorial5.png)

코드 없는 데이터 준비를 사용하여 랭글링 파워 쿼리를 작성합니다. 사용 가능한 함수 목록은 [변환 함수](wrangling-functions.md)를 참조하세요. ADF는 M 스크립트를 데이터 흐름 스크립트로 변환하므로 Azure Data Factory 데이터 흐름 Spark 환경을 사용하여 대규모로 파워 쿼리를 실행할 수 있습니다.

![데이터 랭글링 파워 쿼리 작성 프로세스를 보여주는 스크린샷.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>파워 쿼리 데이터 랭글링 활동 실행 및 모니터링

파워 쿼리 활동의 파이프라인 디버그 실행을 실행하려면 파이프라인 캔버스에서 **디버그** 를 클릭합니다. 파이프라인을 게시하면 **지금 트리거** 로 마지막에 게시된 파이프라인의 주문형 실행을 실행합니다. 파워 쿼리 파이프라인은 모든 기존 Azure Data Factory 트리거를 사용하여 예약할 수 있습니다.

![파워 쿼리 데이터 랭글링 활동을 추가하는 방법을 보여주는 스크린샷.](media/wrangling-data-flow/tutorial3.png)

**모니터** 탭으로 이동하여 트리거된 파워 쿼리 활동 실행의 출력을 시각화합니다.

![트리거된 랭글링 파워 쿼리 활동 실행의 출력을 보여주는 스크린샷.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>다음 단계

[매핑 데이터 흐름 만들기](tutorial-data-flow.md) 방법에 대해 알아봅니다.
