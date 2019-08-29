---
title: Azure Data Factory 시각적 모니터링 | Microsoft Docs
description: Azure 데이터 팩터리를 시각적으로 모니터링 하는 방법 알아보기
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 99ae0139d7b12c0bfb87030d2b749b12834a4f96
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141092"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure Data Factory를 시각적으로 모니터링하는 방법에 대해 알아봅니다
Azure Data Factory는 클라우드 기반 데이터 통합 서비스입니다. 데이터 이동 및 데이터 변환을 오케스트레이션 하 고 자동화 하기 위해 클라우드에서 데이터 기반 워크플로를 만드는 데 사용할 수 있습니다. Azure Data Factory를 사용 하 여 다음을 수행할 수 있습니다.

- 서로 다른 데이터 저장소의 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 예약합니다.
- Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning와 같은 계산 서비스를 사용 하 여 데이터를 처리/변환 합니다.
- 사용할 BI(비즈니스 인텔리전스) 애플리케이션용 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시합니다.

이 빠른 시작에서는 코드를 전혀 작성하지 않고 Data Factory 파이프라인을 시각적으로 모니터링하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="monitor-data-factory-pipelines"></a>Data Factory 파이프라인 모니터링

간단한 목록 뷰 인터페이스를 사용 하 여 파이프라인 및 작업 실행을 모니터링 합니다. 모든 실행은 브라우저의 현지 표준 시간대에 표시 됩니다. 표준 시간대를 변경 하는 경우 모든 날짜/시간 필드가 선택한 항목에 맞춰집니다.  

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 Data Factory UI는 이러한 두 웹 브라우저 에서만 지원 됩니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인 합니다.
3. Azure Portal에서 생성 된 데이터 팩터리에 대 한 블레이드로 이동 합니다. **모니터링 & 관리** 타일을 선택 하 여 Data Factory 시각적 모니터링 환경을 시작 합니다.

## <a name="monitor-pipeline-runs"></a>파이프라인 실행 모니터링
목록 뷰에는 Data Factory 파이프라인에 대 한 각 파이프라인 실행이 표시 됩니다. 여기에는 다음 열이 포함 됩니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인 이름입니다. |
| 동작 | 활동 실행을 보는 데 사용할 수 있는 단일 작업 |
| 실행 시작 | 파이프라인 실행에 대 한 시작 날짜 및 시간 (MM/DD/YYYY, HH: MM: SS AM/PM) |
| Duration | 실행 기간 (HH:MM:SS) |
| 트리거 기준 | 수동 트리거 또는 예약 된 트리거 |
| Status | **실패**, **성공**또는 **진행 중** |
| 매개 변수 | 파이프라인 실행에 대 한 매개 변수 (이름/값 쌍) |
| Error | 파이프라인 실행 오류 (있는 경우) |
| 실행 ID | 파이프라인 실행 ID입니다. |

![파이프라인 실행 모니터링에 대 한 목록 보기](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>작업 실행 모니터링
목록 뷰에는 각 파이프라인 실행에 해당 하는 작업 실행이 표시 됩니다. 각 파이프라인 실행에 대 한 활동 실행을 보려면 **작업** 열에서 **활동 실행** 아이콘을 선택 합니다. 목록 뷰에는 다음 열이 포함 됩니다.

| **열 이름** | **설명** |
| --- | --- |
| 작업 이름 | 파이프라인 내의 활동 이름 |
| 작업 유형 | 활동의 유형 (예: **Copy**, **HDInsightSpark**또는 **HDInsightHive** ) |
| 실행 시작 | 작업 실행에 대 한 시작 날짜 및 시간 (MM/DD/YYYY, HH: MM: SS AM/PM) |
| Duration | 실행 기간 (HH:MM:SS) |
| Status | **실패**, **성공**또는 **진행 중** |
| 입력 | 활동 입력을 설명 하는 JSON 배열입니다. |
| 출력 | 활동 출력을 설명 하는 JSON 배열입니다. |
| Error | 작업 실행 오류 (있는 경우) |

![작업 실행 모니터링에 대 한 목록 보기](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> 파이프라인 및 활동 실행 목록을 새로 고치려면 위쪽의 **새로 고침** 단추를 선택 해야 합니다. 자동 새로 고침은 현재 지원되지 않습니다.

![새로 고침 단추](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>모니터링할 데이터 팩터리 선택
왼쪽 위에 있는 **Data Factory** 아이콘을 마우스로 가리킵니다. 화살표 아이콘을 선택 하 여 모니터링할 수 있는 azure 구독 및 데이터 팩터리에 대 한 목록을 표시 합니다.

![데이터 팩터리를 선택 합니다.](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>목록 보기 구성

### <a name="apply-rich-ordering-and-filtering"></a>풍부한 정렬 및 필터링 적용

순서 파이프라인은 실행 시작 시간에 따라 DESC/ASC로 실행 됩니다. 다음 열을 사용 하 여 필터 파이프라인이 실행 됩니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인의 이름입니다. 옵션에는 **지난 24 시간**, **지난 주**및 **지난 30 일**에 대 한 빠른 필터가 포함 됩니다. 또는 사용자 지정 날짜 및 시간을 선택 합니다. |
| 실행 시작 | 파이프라인 실행의 시작 날짜 및 시간입니다. |
| 실행 상태 | 상태별로 실행 필터: **성공**, **실패**또는 **진행 중**입니다. |

![필터링 옵션](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>열 추가 또는 제거
목록 뷰 헤더를 마우스 오른쪽 단추로 클릭 하 고 목록 뷰에 표시할 열을 선택 합니다.

![열 옵션](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>열 너비 조정
열 머리글을 마우스로 가리키면 목록 뷰에서 열 너비를 늘리거나 줄입니다.

## <a name="promote-user-properties-to-monitor"></a>모니터링하도록 사용자 속성 승격

모니터링할 수 있는 엔터티가 되도록 파이프라인 작업 속성을 사용자 속성으로 승격할 수 있습니다. 예를 들어 파이프라인에 있는 복사 작업의 **원본** 및 **대상** 속성을 사용자 속성으로 승격할 수 있습니다. **자동 생성** 을 선택 하 여 복사 작업의 **원본** 및 **대상** 사용자 속성을 생성할 수도 있습니다.

![사용자 속성 만들기](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 최대 5 개의 파이프라인 활동 속성만 사용자 속성으로 승격할 수 있습니다.

사용자 속성을 만든 후 모니터링 목록 보기에서 모니터링할 수 있습니다. 복사 활동에 대 한 원본이 테이블 이름인 경우 활동 실행에 대해 목록 뷰의 열로 원본 테이블 이름을 모니터링할 수 있습니다.

![사용자 속성을 제외한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image2.png)

![사용자 속성 열을 활동 실행 목록에 추가](media/monitor-visually/monitor-user-properties-image3.png)

![사용자 속성의 열을 포함한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>파이프라인 내부에서 활동 다시 실행

이제 파이프라인 내부에서 활동을 다시 실행할 수 있습니다. **활동 실행 보기**를 선택한 다음 파이프라인을 다시 실행 하려는 파이프라인에서 활동을 선택 합니다.

![활동 실행 보기](media/monitor-visually/rerun-activities-image1.png)

![활동 실행 선택](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>다시 실행 기록 보기

목록 보기에서 모든 파이프라인 실행의 다시 실행 기록을 볼 수 있습니다.

![기록 보기](media/monitor-visually/rerun-history-image1.png)

특정 파이프라인 실행의 다시 실행 기록을 볼 수도 있습니다.

![파이프라인 실행의 기록 보기](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt 보기

Gantt 보기를 사용 하 여 파이프라인 및 활동 실행을 빠르게 시각화할 수 있습니다. 파이프라인 별로 Gantt 보기를 표시 하거나 파이프라인에서 만든 주석/태그를 기준으로 그룹을 볼 수 있습니다.

![Gantt 차트의 예](media/monitor-visually/gantt1.png)

![Gantt 차트 주석](media/monitor-visually/gantt2.png)

막대의 길이는 파이프라인의 기간을 알려 줍니다. 막대를 선택 하 여 자세한 내용을 볼 수도 있습니다.

![Gantt 차트 기간](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>둘러보기
왼쪽 아래에서 **정보** 아이콘을 선택 합니다. 그런 다음 단계별 **둘러보기** 를 선택 하 여 파이프라인 및 작업 실행을 모니터링 하는 방법에 대 한 단계별 지침을 가져옵니다.

![둘러보기](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>사용자 의견
**사용자 의견** 아이콘을 선택 하 여 다양 한 기능이 나 발생할 수 있는 문제에 대 한 피드백을 제공 합니다.

![사용자 의견](media/monitor-visually/feedback.png)

## <a name="alerts"></a>,

Data Factory의 지원되는 메트릭에 대해 경고를 발생시킬 수 있습니다. Data Factory 모니터링 페이지에서**경고 & 메트릭**  **모니터링** > 을 선택 하 여 시작 합니다.

![데이터 팩터리 모니터 페이지](media/monitor-visually/alerts01.png)

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>경고 만들기

1.  새 경고 **규칙** 을 선택 하 여 새 경고를 만듭니다.

    ![새 경고 규칙 단추](media/monitor-visually/alerts02.png)

1.  규칙 이름을 지정 하 고 경고 심각도를 선택 합니다.

    ![규칙 이름 및 심각도 상자](media/monitor-visually/alerts03.png)

1.  경고 조건을 선택 합니다.

    ![대상 조건을 위한 상자](media/monitor-visually/alerts04.png)

    ![조건 목록](media/monitor-visually/alerts05.png)

1.  경고 논리를 구성 합니다. 모든 파이프라인 및 해당 활동의 선택한 메트릭에 대해 경고를 만들 수 있습니다. 특정 활동 유형, 활동 이름, 파이프라인 이름 또는 실패 유형을 선택할 수도 있습니다.

    ![경고 논리를 구성 하기 위한 옵션](media/monitor-visually/alerts06.png)

1.  경고에 대 한 전자 메일, SMS, 푸시 및 음성 알림을 구성 합니다. 경고 알림에 대해 작업 그룹을 만들거나 기존 작업 그룹을 선택 합니다.

    ![알림 구성 옵션](media/monitor-visually/alerts07.png)

    ![알림 추가 옵션](media/monitor-visually/alerts08.png)

1.  경고 규칙을 만듭니다.

    ![경고 규칙을 만드는 옵션](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대 한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) 문서를 참조 하세요.
