---
title: Visual Studio에서 작업 다이어그램을 사용하여 로컬로 Azure Stream Analytics 쿼리 디버깅
description: 이 문서에서는 Visual Studio용 Azure Stream Analytics 도구에서 작업 다이어그램을 사용하여 로컬로 쿼리를 디버깅하는 방법을 설명합니다.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019451"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Visual Studio에서 작업 다이어그램을 사용하여 로컬로 Azure Stream Analytics 쿼리 디버깅

결과를 출력하지 않거나 예기치 않은 결과를 출력하는 작업은 스트리밍 쿼리에 대한 일반적인 문제 해결 시나리오입니다. Visual Studio에서 쿼리를 로컬로 테스트하는 동안 작업 다이어그램을 사용하여 각 단계에 대한 중간 결과 집합 및 메트릭을 검사할 수 있습니다. 작업 다이어그램은 문제를 해결할 때 문제의 원인을 신속하게 격리하는 데 도움이 될 수 있습니다.

## <a name="debug-a-query-using-job-diagram"></a>작업 다이어그램을 사용하여 쿼리 디버깅

Azure Stream Analytics 스크립트는 입력 데이터를 출력 데이터로 변환하는 데 사용됩니다. 작업 다이어그램은 입력 원본(Event Hub, IoT Hub 등)에서 여러 쿼리 단계를 거쳐 최종적으로 출력 싱크까지의 데이터 흐름 방식을 보여 줍니다. 각 쿼리 단계는 `WITH` 문을 사용하여 스크립트에 정의된 임시 결과 집합에 매핑됩니다. 각 중간 결과 집합의 각 쿼리 단계에 대한 메트릭뿐만 아니라 데이터를 보고 이슈의 원인을 찾을 수 있습니다.

> [!NOTE]
> 이 작업 다이어그램에 표시되는 데이터 및 메트릭은 단일 노드의 로컬 테스트 용도로만 사용해야 합니다. 성능 튜닝 및 문제 해결에는 사용하지 않아야 합니다.

### <a name="start-local-testing"></a>로컬 테스트 시작

이 [빠른 시작](stream-analytics-quick-create-vs.md)에서 Visual Studio를 사용하여 Stream Analytics 작업을 만들거나 [기존 작업을 로컬 프로젝트로 내보내는](stream-analytics-vs-tools.md#export-jobs-to-a-project) 방법을 알아봅니다. 로컬 입력 데이터로 쿼리를 테스트하려면 다음 [지침](stream-analytics-live-data-local-testing.md)을 따르세요. 라이브 입력을 사용하여 테스트하려면 다음 단계로 이동합니다.

> [!NOTE]
> 작업을 로컬 프로젝트로 내보내고 라이브 입력 스트림에 대해 테스트하려면 모든 입력에 대한 자격 증명을 다시 지정해야 합니다.  

스크립트 편집기에서 입력 및 출력 원본을 선택한 다음, **로컬에서 실행** 을 선택합니다. 작업 다이어그램이 오른쪽에 나타납니다.

### <a name="view-the-intermediate-result-set"></a>중간 결과 집합 보기  

1. 쿼리 단계를 선택하여 스크립트로 이동합니다. 왼쪽의 편집기에 있는 해당 스크립트로 자동으로 이동됩니다.

   ![작업 다이어그램 탐색 스크립트](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 쿼리 단계를 선택하고 팝업 대화 상자에서 **미리 보기** 를 선택합니다. 결과 집합은 아래쪽 결과 창의 탭에 표시됩니다.

   ![작업 다이어그램 미리 보기 결과](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>단계 메트릭 보기

이 섹션에서는 다이어그램의 각 부분에 사용할 수 있는 메트릭을 살펴봅니다.

#### <a name="input-sources-live-stream"></a>입력 원본(라이브 스트림)

![작업 다이어그램 라이브 입력 원본](./media/debug-locally-using-job-diagram/live-input.png)

|메트릭|Description|
|-|-|
|**TaxiRide**| 입력의 이름입니다.|
|**이벤트 허브** | 입력 원본 유형입니다.|
|**이벤트**|읽은 이벤트 수입니다.|
|**백로그된 이벤트 원본**|Event Hubs 입력과 IoT Hub 입력에 대해 더 읽어야 하는 메시지 수입니다.|
|**이벤트(바이트)**|읽은 바이트 수입니다.|
| **저하된 이벤트**|deserialization이 아닌 다른 문제가 있는 이벤트 수입니다.|
|**초기 이벤트**| 상위 워터마크 앞에 애플리케이션 타임스탬프가 있는 이벤트 수입니다.|
|**늦은 이벤트**| 상위 워터마크 뒤에 애플리케이션 타임스탬프가 있는 이벤트 수입니다.|
|**이벤트 원본**| 읽은 데이터 단위 수입니다. 예를 들어 Blob의 수입니다.|

#### <a name="input-sources-local-input"></a>입력 원본(로컬 입력)

![작업 다이어그램 로컬 입력 원본](./media/debug-locally-using-job-diagram/local-input.png)

|메트릭|Description|
|-|-|
|**TaxiRide**| 입력의 이름입니다.|
|**행 개수**| 단계에서 생성된 행 수입니다.|
|**데이터 크기**| 이 단계에서 생성된 데이터 크기입니다.|
|**로컬 입력**| 로컬 데이터를 입력으로 사용합니다.|

#### <a name="query-steps"></a>쿼리 단계

![작업 다이어그램 쿼리 단계](./media/debug-locally-using-job-diagram/query-step.png)

|메트릭|Description|
|-|-|
|**TripData**|임시 결과 집합의 이름입니다.|
|**행 개수**| 단계에서 생성된 행 수입니다.|
|**데이터 크기**| 이 단계에서 생성된 데이터 크기입니다.|
  
#### <a name="output-sinks-live-output"></a>출력 싱크(라이브 출력)

![로컬 출력 싱크를 표시하는 작업 다이어그램입니다.](./media/debug-locally-using-job-diagram/live-output.png)

|메트릭|Description|
|-|-|
|**regionaggEH**|출력의 이름입니다.|
|**이벤트**|싱크에 대한 이벤트 출력 수입니다.|

#### <a name="output-sinks-local-output"></a>출력 싱크(로컬 출력)

![작업 다이어그램 로컬 출력 싱크](./media/debug-locally-using-job-diagram/local-output.png)

|메트릭|Description|
|-|-|
|**regionaggEH**|출력의 이름입니다.|
|**로컬 출력**| 로컬 파일에 출력되는 결과입니다.|
|**행 개수**| 로컬 파일에 출력되는 행 수입니다.|
|**데이터 크기**| 로컬 파일에 출력되는 데이터 크기입니다.|

### <a name="close-job-diagram"></a>작업 다이어그램 닫기

작업 다이어그램이 더 이상 필요하지 않은 경우 오른쪽 상단에서 **닫기** 를 선택합니다. 다이어그램 창을 닫은 후에는 로컬 테스트를 다시 시작하여 확인해야 합니다.

### <a name="view-job-level-metrics-and-stop-running"></a>작업 수준 메트릭 보기 및 실행 중지

다른 작업 수준 메트릭은 팝업 콘솔에 표시됩니다. 작업을 중지하려면 콘솔에서 **Ctrl+C** 를 누릅니다.

![작업 다이어그램 중지 작업](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>제한 사항

* Power BI 및 Azure Data Lake Storage Gen1 출력 싱크는 인증 모델 제한 사항으로 인해 지원되지 않습니다.

* 클라우드 입력 옵션만 [시간 정책](./stream-analytics-time-handling.md)을 지원하고, 로컬 입력 옵션은 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트(미리 보기)](stream-analytics-live-data-local-testing.md)