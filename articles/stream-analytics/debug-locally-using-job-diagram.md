---
title: Visual Studio에서 작업 다이어그램을 사용 하 여 로컬에서 Azure Stream Analytics 쿼리 디버그
description: 이 문서에서는 Visual Studio 용 Azure Stream Analytics 도구에서 작업 다이어그램을 사용 하 여 로컬로 쿼리를 디버깅 하는 방법을 설명 합니다.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: e9d6bbdf325c0da96b81c1f2146da284ea744def
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218258"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Visual Studio에서 작업 다이어그램을 사용 하 여 로컬에서 Azure Stream Analytics 쿼리 디버그

결과를 출력 하지 않거나 예기치 않은 결과를 출력 하는 작업은 스트리밍 쿼리를 위한 일반적인 문제 해결 시나리오입니다. Visual Studio에서 로컬로 쿼리를 테스트 하는 동안 작업 다이어그램을 사용 하 여 각 단계에 대 한 중간 결과 집합과 메트릭을 검사할 수 있습니다. 작업 다이어그램은 문제를 해결할 때 문제의 원인을 신속 하 게 격리 하는 데 도움이 될 수 있습니다.

## <a name="debug-a-query-using-job-diagram"></a>작업 다이어그램을 사용 하 여 쿼리 디버그

Azure Stream Analytics 스크립트는 입력 데이터를 출력 데이터로 변환 하는 데 사용 됩니다. 작업 다이어그램은 여러 쿼리 단계를 통해 입력 원본 (이벤트 허브, IoT Hub 등)에서 데이터 흐름을 표시 하 고, 마지막으로 출력 싱크로 이동 하는 방법을 보여 줍니다. 각 쿼리 단계는 문을 사용 하 여 스크립트에 정의 된 임시 결과 집합에 매핑됩니다 `WITH` . 각 중간 결과 집합의 각 쿼리 단계에 대 한 메트릭 뿐만 아니라 데이터를 확인 하 여 문제의 원인을 찾을 수 있습니다.

> [!NOTE]
> 이 작업 다이어그램은 단일 노드의 로컬 테스트에 대 한 데이터 및 메트릭만 보여 줍니다. 성능 튜닝 및 문제 해결에 사용 하면 안 됩니다.

### <a name="start-local-testing"></a>로컬 테스트 시작

이 [빠른](stream-analytics-quick-create-vs.md) 시작을 사용 하 여 Visual Studio를 사용 하 여 Stream Analytics 작업을 만드는 방법을 알아보거나 [기존 작업을 로컬 프로젝트로 내보냅니다](stream-analytics-vs-tools.md#export-jobs-to-a-project). 로컬 입력 데이터로 쿼리를 테스트 하려면 다음 [지침](stream-analytics-live-data-local-testing.md)을 따르세요. 라이브 입력으로 테스트 하려면 다음 단계로 이동 합니다.

> [!NOTE]
> 작업을 로컬 프로젝트로 내보내고 라이브 입력 스트림에 대해 테스트 하려면 모든 입력에 대 한 자격 증명을 다시 지정 해야 합니다.  

스크립트 편집기에서 입력 및 출력 원본을 선택 하 고 로컬에서 **실행**을 선택 합니다. 작업 다이어그램이 오른쪽에 나타납니다.

### <a name="view-the-intermediate-result-set"></a>중간 결과 집합 보기  

1. 쿼리 단계를 선택 하 여 스크립트를 탐색 합니다. 왼쪽 편집기에서 해당 스크립트로 자동으로 이동 됩니다.

   ![작업 다이어그램 탐색 스크립트](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 쿼리 단계를 선택 하 고 팝업 대화 상자에서 **미리 보기** 를 선택 합니다. 결과 집합은 아래쪽 결과 창의 탭에 표시 됩니다.

   ![작업 다이어그램 미리 보기 결과](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>단계 메트릭 보기

이 섹션에서는 다이어그램의 각 부분에 사용할 수 있는 메트릭을 탐색 합니다.

#### <a name="input-sources-live-stream"></a>입력 소스 (라이브 스트림)

![작업 다이어그램 라이브 입력 원본](./media/debug-locally-using-job-diagram/live-input.png)

|메트릭|Description|
|-|-|
|**TaxiRide**| 입력의 이름입니다.|
|**이벤트 허브** | 입력 원본 유형입니다.|
|**이벤트**|읽은 이벤트 수입니다.|
|**백로그 이벤트 원본**|Event Hubs 및 IoT Hub 입력에 대해 읽어야 할 메시지의 수입니다.|
|**이벤트 (바이트)**|읽은 바이트 수입니다.|
| **저하 된 이벤트**|Deserialization과는 다른 문제가 발생 한 이벤트의 수입니다.|
|**초기 이벤트**| 상위 워터 마크 앞에 응용 프로그램 타임 스탬프가 있는 이벤트의 수입니다.|
|**후기 이벤트**| 상위 워터 마크 이후에 응용 프로그램 타임 스탬프를 포함 하는 이벤트의 수입니다.|
|**이벤트 원본**| 읽은 데이터 단위의 수입니다. 예를 들어 Blob의 수입니다.|

#### <a name="input-sources-local-input"></a>입력 소스 (로컬 입력)

![작업 다이어그램 로컬 입력 소스](./media/debug-locally-using-job-diagram/local-input.png)

|메트릭|Description|
|-|-|
|**TaxiRide**| 입력의 이름입니다.|
|**행 개수**| 단계에서 생성 된 행 수입니다.|
|**데이터 크기**| 이 단계에서 생성 된 데이터의 크기입니다.|
|**로컬 입력**| 로컬 데이터를 입력으로 사용 합니다.|

#### <a name="query-steps"></a>쿼리 단계

![작업 다이어그램 쿼리 단계](./media/debug-locally-using-job-diagram/query-step.png)

|메트릭|Description|
|-|-|
|**Hubway-tripdata**|임시 결과 집합의 이름입니다.|
|**행 개수**| 단계에서 생성 된 행 수입니다.|
|**데이터 크기**| 이 단계에서 생성 된 데이터의 크기입니다.|
  
#### <a name="output-sinks-live-output"></a>출력 싱크 (실시간 출력)

![로컬 출력 싱크를 표시 하는 작업 다이어그램](./media/debug-locally-using-job-diagram/live-output.png)

|메트릭|Description|
|-|-|
|**지역 Aggeh**|출력의 이름입니다.|
|**이벤트**|싱크에 대 한 이벤트 출력 수입니다.|

#### <a name="output-sinks-local-output"></a>출력 싱크 (로컬 출력)

![작업 다이어그램 로컬 출력 싱크](./media/debug-locally-using-job-diagram/local-output.png)

|메트릭|Description|
|-|-|
|**지역 Aggeh**|출력의 이름입니다.|
|**로컬 출력**| 로컬 파일에 결과를 출력 합니다.|
|**행 개수**| 로컬 파일에 출력 되는 행 수입니다.|
|**데이터 크기**| 로컬 파일에 대 한 데이터 출력의 크기입니다.|

### <a name="close-job-diagram"></a>작업 다이어그램 닫기

작업 다이어그램이 더 이상 필요 하지 않은 경우 오른쪽 위 모서리에서 **닫기** 를 선택 합니다. 다이어그램 창을 닫은 후에는 로컬 테스트를 다시 시작 하 여 확인 해야 합니다.

### <a name="view-job-level-metrics-and-stop-running"></a>작업 수준 메트릭 보기 및 실행 중지

다른 작업 수준 메트릭은 팝업 콘솔에 표시 됩니다. 작업을 중지 하려면 콘솔에서 **ctrl + C** 를 누릅니다.

![작업 다이어그램 작업 중지](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>제한 사항

* Power BI 및 Azure Data Lake Storage Gen1 출력 싱크는 인증 모델 제한으로 인해 지원 되지 않습니다.

* 클라우드 입력 옵션만 [시간 정책](stream-analytics-out-of-order-and-late-events.md)을 지원하고, 로컬 입력 옵션은 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트(미리 보기)](stream-analytics-live-data-local-testing.md)
