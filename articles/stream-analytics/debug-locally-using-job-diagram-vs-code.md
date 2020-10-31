---
title: Visual Studio Code에서 작업 다이어그램을 사용 하 여 로컬에서 Azure Stream Analytics 쿼리 디버그
description: 이 문서에서는 Visual Studio Code의 Azure Stream Analytics 확장에서 작업 다이어그램을 사용 하 여 로컬로 쿼리를 디버깅 하는 방법을 설명 합니다.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 766d3f8d4d4c03fb4fe173dbb12eb85bb09cd71a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124172"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Visual Studio Code에서 작업 다이어그램을 사용 하 여 로컬에서 Azure Stream Analytics 쿼리 디버그

결과를 출력 하지 않거나 예기치 않은 결과를 출력 하는 스트리밍 작업은 문제 해결이 필요할 수도 있습니다. Azure Stream Analytics에 대 한 Visual Studio Code 확장은 작업 다이어그램, 메트릭, 진단 로그 및 중간 결과를 통합 하 여 문제의 원인을 신속 하 게 격리할 수 있도록 합니다. 쿼리를 로컬로 테스트 하는 동안 작업 다이어그램을 사용 하 여 각 단계에 대 한 중간 결과 집합과 메트릭을 검사할 수 있습니다.

## <a name="debug-a-query-using-job-diagram"></a>작업 다이어그램을 사용 하 여 쿼리 디버그

Azure Stream Analytics 스크립트는 입력 데이터를 출력 데이터로 변환 하는 데 사용 됩니다. 작업 다이어그램은 이벤트 허브 또는 IoT Hub와 같은 입력 원본에서 여러 쿼리 단계를 통해 출력 싱크로 데이터 흐름을 보여 줍니다. 각 쿼리 단계는 문을 사용 하 여 스크립트에 정의 된 임시 결과 집합에 매핑됩니다 `WITH` . 각 중간 결과 집합의 각 쿼리 단계에 대 한 메트릭 뿐만 아니라 데이터를 확인 하 여 문제의 원인을 찾을 수 있습니다.

> [!NOTE]
> 이 작업 다이어그램은 단일 노드의 로컬 테스트에 대 한 데이터 및 메트릭만 보여 줍니다. 성능 튜닝 및 문제 해결에 사용 하면 안 됩니다.

### <a name="start-local-testing"></a>로컬 테스트 시작

이 [빠른](quick-create-visual-studio-code.md) 시작을 사용 하 여 Visual Studio Code를 사용 하 여 Stream Analytics 작업을 만들거나 [기존 작업을 로컬 프로젝트로 내보내는](visual-studio-code-explore-jobs.md)방법을 알아봅니다. 입력 및 출력에 대 한 자격 증명은 내보낸 작업에 대해 자동으로 채워집니다.

로컬 입력 데이터로 쿼리를 테스트 하려면 다음 [지침](visual-studio-code-local-run.md)을 따르세요. 라이브 입력을 사용 하 여 테스트 하려면 다음 단계로 이동 하 여 [입력을 구성](stream-analytics-add-inputs.md) 합니다. 

*\. Script.asaql* 스크립트 파일을 열고 로컬에서 **실행** 을 선택 합니다. 그런 다음 **로컬 입력 사용** 또는 **라이브 입력 사용** 을 선택 합니다. 작업 다이어그램이 창의 오른쪽에 나타납니다.

### <a name="view-the-output-and-intermediate-result-set"></a>출력 및 중간 결과 집합 보기  

1. 모든 작업 출력은 Visual Studio Code 창의 오른쪽 아래에 있는 결과 창에 표시 됩니다.

   > [!div class="mx-imgBorder"]
   > ![작업 출력 결과](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. 쿼리 단계를 선택 하 여 스크립트를 탐색 합니다. 왼쪽 편집기에서 해당 스크립트로 자동 전달 됩니다. 중간 결과가 Visual Studio Code 창의 오른쪽 아래에 있는 결과 창에 표시 됩니다.

   > [!div class="mx-imgBorder"]
   > ![작업 다이어그램 미리 보기 결과](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>메트릭 보기

이 섹션에서는 다이어그램의 각 부분에 사용할 수 있는 메트릭을 탐색 합니다.

1. Visual Studio Code 창의 오른쪽 아래에 있는 **결과** 탭 옆의 **메트릭** 탭을 선택 합니다.

2. 드롭다운에서 **작업** 을 선택 합니다. 그래프 노드에서 빈 공간을 선택 하 여 작업 수준 메트릭으로 이동할 수 있습니다. 이 보기에는 작업이 실행 중일 때 10 초 마다 업데이트 되는 모든 메트릭이 포함 됩니다. 오른쪽에서 메트릭을 선택 하거나 선택 취소 하 여 차트에서 볼 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![작업 다이어그램 메트릭](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. 드롭다운 목록에서 입력 데이터 원본의 이름을 선택 하 여 입력 메트릭을 표시 합니다. 아래 스크린샷에서 입력 소스를 *따옴표* 라고 합니다. 입력 메트릭에 대 한 자세한 내용은 [Stream Analytics 작업 모니터링 및 쿼리 모니터링 방법 이해](stream-analytics-monitoring.md)를 참조 하세요.

   > [!div class="mx-imgBorder"]
   > ![작업 다이어그램 입력 메트릭](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. 작업 다이어그램에서 쿼리 단계를 선택 하거나 드롭다운 목록에서 단계 이름을 선택 하 여 단계 수준 메트릭을 표시 합니다. 워터 마크 지연은 유일 하 게 사용할 수 있는 단계 메트릭입니다.

   > [!div class="mx-imgBorder"]
   > ![단계 메트릭](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. 다이어그램 또는 드롭다운에서 출력을 선택 하 여 출력 관련 메트릭을 표시 합니다. 출력 메트릭에 대 한 자세한 내용은 [Stream Analytics 작업 모니터링 및 쿼리 모니터링 방법 이해](stream-analytics-monitoring.md)를 참조 하세요. 라이브 출력 싱크는 지원 되지 않습니다.

   > [!div class="mx-imgBorder"]
   > ![출력 메트릭](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>진단 로그 보기

작업 수준 진단 로그에는 입력 데이터 원본 및 출력 싱크에 대 한 진단 정보가 포함 됩니다. 입력 노드 또는 출력 노드를 선택 하면 해당 로그만 표시 됩니다. 쿼리 단계를 선택 하면 로그가 표시 되지 않습니다. 작업 수준에서 모든 로그를 찾을 수 있으며 심각도 및 시간별로 로그를 필터링 할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![진단 로그](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   로그 항목을 선택 하 여 전체 메시지를 확인 합니다.

   > [!div class="mx-imgBorder"]
   > ![진단 로그 메시지](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>기타 작업 다이어그램 기능

필요에 따라 도구 모음에서 **중지** 또는 **일시 중지** 를 선택할 수 있습니다. 작업이 일시 중지 되 면 마지막 출력에서 다시 시작할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![작업 중지 또는 일시 중지](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

작업 다이어그램의 오른쪽 위에서 **작업 요약** 을 선택 하 여 로컬 작업에 대 한 속성 및 구성을 확인 합니다.

> [!div class="mx-imgBorder"]
> ![로컬 작업 요약](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>제한 사항

* 라이브 출력 싱크는 로컬 실행에서 지원 되지 않습니다.

* JavaScript 함수를 사용 하 여 작업을 로컬로 실행 하는 것은 Windows 운영 체제 에서만 지원 됩니다.

* C # 사용자 지정 코드 및 Azure Machine Learning 함수는 지원 되지 않습니다. 

* 클라우드 입력 옵션에는 [시간 정책이](./stream-analytics-time-handling.md) 지원 되지만 로컬 입력 옵션은 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code를 사용 하 여 Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
* [Visual Studio Code를 사용 하 여 Azure Stream Analytics 탐색](visual-studio-code-explore-jobs.md)
* [Visual Studio Code를 통해 샘플 데이터를 사용하여 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용 하 여 라이브 입력으로 Azure Stream Analytics 작업을 로컬로 테스트](visual-studio-code-local-run-live-input.md)