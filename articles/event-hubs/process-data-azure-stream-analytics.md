---
title: 스트림 분석을 사용하여 이벤트 허브 Azure의 데이터 처리 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Stream Analytics 작업을 사용하여 Azure 이벤트 허브에서 데이터를 처리하는 방법을 보여 줍니다.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991939"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Azure Stream 분석을 사용하여 이벤트 허브에서 데이터 처리 
Azure Stream Analytics 서비스를 사용하면 Azure Event Hubs에서 스트리밍 데이터를 쉽게 수집, 처리 및 분석할 수 있으므로 강력한 통찰력을 통해 실시간 작업을 수행할 수 있습니다. 이러한 통합을 통해 핫 경로 분석 파이프라인을 빠르게 만들 수 있습니다. Azure 포털을 사용하여 들어오는 데이터를 시각화하고 스트림 분석 쿼리를 작성할 수 있습니다. 쿼리가 준비되면 몇 번의 클릭만으로 프로덕션으로 이동할 수 있습니다. 

## <a name="key-benefits"></a>주요 이점
Azure 이벤트 허브 및 Azure 스트림 분석 통합의 주요 이점은 다음과 같습니다. 
- **데이터 미리 보기** - Azure 포털의 이벤트 허브에서 들어오는 데이터를 미리 볼 수 있습니다.
- **쿼리 테스트** - 변환 쿼리를 준비하고 Azure 포털에서 직접 테스트합니다. 쿼리 언어 구문은 [스트림 분석 쿼리 언어](/stream-analytics-query/built-in-functions-azure-stream-analytics) 설명서를 참조하십시오.
- **프로덕션에 쿼리 배포** - Azure Stream Analytics 작업을 만들고 시작하여 쿼리를 프로덕션에 배포할 수 있습니다.

## <a name="end-to-end-flow"></a>종단 간 흐름

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 
1. **이벤트 허브 네임스페이스로** 이동한 다음 들어오는 데이터가 있는 **이벤트 허브로**이동합니다. 
1. 이벤트 허브 페이지에서 **데이터 프로세스를** 선택합니다.  

    ![프로세스 데이터 타일](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. **이벤트 타일에서 실시간 인사이트 활성화에서** **탐색을** 선택합니다. 

    ![Stream Analytics 선택](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 다음 필드에 대해 값이 이미 설정된 쿼리 페이지가 표시됩니다.
    1. **쿼리에** 대한 입력으로 이벤트 허브입니다.
    1. SELECT 문을 사용하여 **SQL 쿼리를** 샘플링합니다. 
    1. 쿼리 테스트 결과를 참조하는 **출력** 별칭입니다. 

        ![쿼리 편집기](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  이 기능을 처음 사용할 때 이 페이지에서는 소비자 그룹을 만들 수 있는 권한과 이벤트 허브가 들어오는 데이터를 미리 볼 수 있는 정책을 요청합니다.
1. 이전 이미지와 같이 **입력 미리 보기** 창에서 **만들기를** 선택합니다. 
1. 이 탭에서 최신 수신 데이터의 스냅샷이 즉시 표시됩니다.
    - 데이터의 직렬화 유형이 자동으로 검색됩니다(JSON/CSV). JSON/CSV/AVRO로도 수동으로 변경할 수 있습니다.
    - 테이블 형식 또는 원시 형식으로 들어오는 데이터를 미리 볼 수 있습니다. 
    - 표시된 데이터가 최신 데이터가 아닌 경우 **새로 고침을** 선택하여 최신 이벤트를 확인합니다. 

        다음은 **테이블 형식의**데이터의 예입니다: ![테이블 형식의 결과](./media/process-data-azure-stream-analytics/snapshot-results.png)

        다음은 **원시 형식의**데이터의 예입니다. 

        ![원시 형식의 결과](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. **테스트 쿼리를** 선택하여 **테스트 결과** 탭에서 쿼리의 테스트 결과의 스냅샷을 확인합니다. 결과를 다운로드할 수도 있습니다.

    ![테스트 쿼리 결과](./media/process-data-azure-stream-analytics/test-results.png)
1. 사용자 고유의 쿼리를 작성하여 데이터를 변환합니다. [스트림 분석 쿼리 언어 참조를](/stream-analytics-query/stream-analytics-query-language-reference)참조하십시오.
1. 쿼리를 테스트하고 프로덕션으로 이동하려면 **쿼리 배포를**선택합니다. 쿼리를 배포하려면 작업에 대한 출력을 설정하고 작업을 시작할 수 있는 Azure Stream Analytics 작업을 만듭니다. [분석 스트림] 작업을 만들려면 작업의 이름을 지정하고 **을**선택합니다.

      ![Azure Stream Analytics 작업 만들기](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  이벤트 허브 페이지에서 만드는 각 새 Azure Stream Analytics 작업에 대한 소비자 그룹 및 정책을 만드는 것이 좋습니다. 소비자 그룹은 5개의 동시 판독기만 허용하므로 각 작업에 대해 전용 소비자 그룹을 제공하면 해당 제한을 초과하여 발생할 수 있는 오류를 방지할 수 있습니다. 전용 정책을 사용하면 다른 리소스에 영향을 주지 않고 키를 회전하거나 권한을 취소할 수 있습니다. 
1. 이제 쿼리가 테스트한 것과 동일한 위치에 Stream Analytics 작업이 만들어지고 입력이 이벤트 허브입니다. 

9.  파이프라인을 완료하려면 쿼리 **출력을** 설정하고 작업을 시작하려면 **시작을** 선택합니다.

    > [!NOTE]
    > 작업을 시작하기 전에 Azure Stream Analytics에서 만든 출력 이름으로 outputalias를 대체하는 것을 잊지 마십시오.

      ![출력 설정 및 작업 시작](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>알려진 제한 사항
쿼리를 테스트하는 동안 테스트 결과를 로드하는 데 약 6초가 걸립니다. 테스트 성능을 개선하기 위해 노력하고 있습니다. 그러나 프로덕션 환경에서 배포할 때 Azure Stream Analytics는 초단위로 대기 시간을 갖습니다.

## <a name="streaming-units"></a>스트리밍 단위
Azure 스트림 분석 작업은 기본적으로 세 개의 스트리밍 단위(SUS)로 설정됩니다. 이 설정을 조정하려면 Azure 포털의 **검색 분석 작업** 페이지에서 왼쪽 메뉴의 **배율을** 선택합니다. 스트리밍 단위에 대한 자세한 내용은 [스트리밍 단위 이해 및 조정을](../stream-analytics/stream-analytics-streaming-unit-consumption.md)참조하십시오.

![스케일 스트리밍 단위](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>다음 단계
스트림 분석 쿼리에 대해 자세히 알아보려면 [스트림 분석 쿼리 언어를](/stream-analytics-query/built-in-functions-azure-stream-analytics) 참조하세요.
