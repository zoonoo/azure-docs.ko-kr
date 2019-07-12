---
title: Stream Analytics를 사용 하 여 Event Hubs Azure에서 데이터 처리 | Microsoft Docs
description: 이 문서에서는 Azure Stream Analytics 작업을 사용 하 여 Azure event hub에서 데이터를 처리 하는 방법을 보여 줍니다.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723416"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Azure Stream Analytics를 사용 하 여 이벤트 허브에서 데이터를 처리 합니다.
Azure Stream Analytics 서비스를 쉽게 수집, 처리 및 Azure Event Hubs의 스트리밍 데이터 분석을 실시간 작업을 실행 하는 강력한 정보를 사용 하도록 설정 합니다. 이 통합을 사용 하면 신속 하 게 실행 부하 과다 경로 분석 파이프라인을 만들 수 있습니다. 들어오는 데이터를 시각화 하 고 Stream Analytics 쿼리를 작성 하는 Azure 포털을 사용할 수 있습니다. 쿼리 준비 되 면만 몇 번의 클릭 만으로 프로덕션으로 이동할 수 있습니다. 

## <a name="key-benefits"></a>주요 이점
Azure Event Hubs 및 Azure Stream Analytics 통합의 주요 이점은 다음과 같습니다. 
- **데이터 미리 보기** – Azure portal에서 event hub에서 들어오는 데이터를 미리 볼 수 있습니다.
- **쿼리 테스트** – 변환 쿼리를 준비 하 고 Azure portal에서 직접 테스트 합니다. 쿼리 언어 구문에 대 한 참조 [Stream Analytics 쿼리 언어](/stream-analytics-query/built-in-functions-azure-stream-analytics) 설명서.
- **프로덕션 환경에 쿼리가 배포** -쿼리를 만들고 Azure Stream Analytics 작업을 시작 하 여 프로덕션 환경에 배포할 수 있습니다.

## <a name="end-to-end-flow"></a>종단 간 흐름

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. 로 이동 하 **Event Hubs 네임 스페이스** 로 이동한 다음 합니다 **이벤트 허브**, 들어오는 데이터입니다. 
1. 선택 **데이터 처리** 이벤트 허브 페이지입니다.  

    ![프로세스 데이터 타일](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 선택 **탐색** 에 **이벤트에서 실시간 정보를 사용 하도록 설정** 바둑판식으로 배열 합니다. 

    ![Stream Analytics 선택](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 다음 필드에 대해 이미 설정 된 값을 사용 하 여 쿼리 페이지를 참조 합니다.
    1. 프로그램 **이벤트 허브** 쿼리에 대 한 입력으로 합니다.
    1. 샘플 **SQL 쿼리** SELECT 문을 사용 하 여 합니다. 
    1. **출력** 테스트 결과 참조 하도록 별칭입니다. 

        ![쿼리 편집기](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  처음으로이 기능을 사용 하는 경우이 페이지 들어오는 데이터를 미리 보려면 소비자 그룹 및 이벤트 허브에 대 한 정책을 만들 수 있는 권한을 요청 합니다.
1. 선택 **만들기** 에 **입력 미리 보기** 이전 그림과에서 같이 창입니다. 
1. 이 탭에서 최신 들어오는 데이터의 스냅숏을 즉시 볼 수 있습니다.
    - 데이터의 serialization 형식이 자동으로 (JSON/CSV)를 검색 합니다. 수동으로 그 JSON/CSV/AVRO에도 변경할 수 있습니다.
    - 테이블 형식 또는 원시 형식으로 들어오는 데이터를 미리 볼 수 있습니다. 
    - 에 표시 된 데이터를 현재 없는 경우 선택 **새로 고침** 최신 이벤트를 확인 합니다. 

        다음은 데이터의 예는 **테이블 형식으로**:   ![테이블 형식 결과](./media/process-data-azure-stream-analytics/snapshot-results.png)

        다음은 데이터의 예는 **원시 형식으로**: 

        ![결과를 원시 형식](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 선택 **테스트 쿼리** 쿼리를 테스트 결과의 스냅숏을 참조 하는 **테스트 결과** 탭 합니다. 또한 결과 다운로드할 수 있습니다.

    ![쿼리 결과 테스트 합니다.](./media/process-data-azure-stream-analytics/test-results.png)
1. 데이터를 변환 하는 쿼리를 직접 작성 합니다. 참조 [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)합니다.
1. 쿼리를 테스트 한 후 프로덕션에서 선택 이동 하려는 **배포 쿼리**합니다. 쿼리를 배포 하려면 작업에 대해 출력을 설정 하 고 작업을 시작할 수 있는 Azure Stream Analytics 작업을 만듭니다. Stream Analytics 작업을 만들려면 작업에 대 한 이름을 지정 하 고 선택 **만들기**합니다.

      ![Azure Stream Analytics 작업 만들기](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  소비자 그룹 및 사용자가 만든 이벤트 허브 페이지에서 각 새 Azure Stream Analytics 작업에 대 한 정책을 만들어야 하는 것이 좋습니다. 해당 제한을 초과에서 발생할 수 있는 모든 오류를 방지는 각 작업에 대 한 전용된 소비자 그룹을 제공 하므로 5 명의 동시 판독기를 허용 하는 소비자 그룹입니다. 전용된 정책 키 회전 또는 기타 리소스에 영향을 주는 없이 사용 권한을 해지할 수 있습니다. 
1. 여기서 쿼리를 테스트 하는 동일 하 고 입력 이벤트 허브는 Stream Analytics 작업이 만들어집니다. 

9.  파이프라인을 완료 하려면 설정 합니다 **출력** 쿼리를 선택 **시작** 작업을 시작 합니다.

    > [!NOTE]
    > 작업을 시작 하기 전에 반드시 Azure Stream Analytics에서 만든 출력 이름으로 된 출력 별칭을 대체 합니다.

      ![출력을 설정 하 고 작업을 시작](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>알려진 제한 사항
쿼리를 테스트 하는 동안 테스트 결과를 로드 하는 약 6 초를 수행 합니다. 테스트의 성능 향상에 최선을 다하고 있습니다. 그러나 프로덕션에 배포한 경우 Azure Stream Analytics 초 대기를 해야 합니다.

## <a name="streaming-units"></a>스트리밍 단위
Azure Stream Analytics 작업 기본값은 3 명의 Su (스트리밍 단위)입니다. 조정 하려면이 설정을 선택 합니다 **크기 조정** 의 왼쪽된 메뉴에서를 **Stream Analytics 작업** Azure 포털에서 페이지입니다. 스트리밍 단위에 대 한 자세한 내용은 참조 하세요 [이해 하 고 스트리밍 단위 조정](../stream-analytics/stream-analytics-streaming-unit-consumption.md)합니다.

![스트리밍 단위](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>다음 단계
Stream Analytics 쿼리 하는 방법에 대 한 자세한 내용은를 참조 하세요. [Stream Analytics 쿼리 언어](/stream-analytics-query/built-in-functions-azure-stream-analytics)