---
title: Azure 데이터 팩터리를 시각적으로 모니터링
description: Azure 데이터 팩터리를 시각적으로 모니터링하는 방법 알아보기
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 85b1d6b532ba11819947558226291e62af6b5119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690918"
---
# <a name="visually-monitor-azure-data-factory"></a>Azure 데이터 팩터리를 시각적으로 모니터링

Azure Data Factory에서 파이프라인을 만들고 게시한 후에는 파이프라인을 트리거와 연결하거나 임시 실행을 수동으로 시작할 수 있습니다. Azure Data Factory 사용자 환경에서 기본적으로 실행되는 모든 파이프라인을 모니터링할 수 있습니다. 모니터링 환경을 열려면 [Azure 포털의](https://portal.azure.com/)데이터 팩터리 블레이드에서 & 관리 타일 **모니터링을** 선택합니다. 이미 ADF UX에 있는 경우 왼쪽 사이드바의 **모니터** 아이콘을 클릭합니다.

모든 데이터 팩터리 실행은 브라우저의 현지 표준 시간대에 표시됩니다. 표준 시간대를 변경하면 모든 날짜/시간 필드가 선택한 필드로 스냅됩니다.

## <a name="monitor-pipeline-runs"></a>파이프라인 실행 모니터링

기본 모니터링 보기는 선택한 기간 동안 실행되는 파이프라인 목록입니다. 표시되는 열은 다음과 같습니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인 이름입니다. |
| 동작 | 활동 세부 정보를 보거나 파이프라인을 취소하거나 다시 실행할 수 있는 아이콘 |
| 실행 시작 | 파이프라인 실행 시작 날짜 및 시간(MM/DD/YYY, HH:MM:SS AM/PM) |
| Duration | 실행 기간 (HH:MM:SS) |
| 트리거 주체 | 파이프라인을 시작한 트리거의 이름 |
| 상태 | **실패한**것 , **성공한**것 , **진행 중,** **취소됨,** 또는 **큐에 대기됨** |
| 주석 | 파이프라인과 연결된 필터링 가능한 태그  |
| 매개 변수 | 파이프라인 실행에 대한 매개 변수(이름/값 쌍) |
| Error | 파이프라인에 실패한 경우 실행 오류가 발생합니다. |
| 실행 ID | 파이프라인 실행 ID입니다. |

![파이프라인 실행을 모니터링하기 위한 목록 보기](media/monitor-visually/pipeline-runs.png)

파이프라인 및 활동 실행 목록을 새로 고치려면 **새로 고침** 단추를 수동으로 선택해야 합니다. 자동 새로 고침은 현재 지원되지 않습니다.

![새로 고침 단추](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>작업 실행 모니터링

각 파이프라인 실행에 대한 활동 실행을 보려면 **작업** 열 에서 **활동 실행 보기** 아이콘을 선택합니다. 목록 보기는 각 파이프라인 실행에 해당하는 활동 실행을 보여 주며, 이 에 해당합니다.

| **열 이름** | **설명** |
| --- | --- |
| 작업 이름 | 파이프라인 내의 활동 이름 |
| 작업 유형 | **복사**, **실행데이터 흐름**또는 **AzureML실행 파이프라인과** 같은 활동 유형 |
| 동작 | JSON 입력 정보, JSON 출력 정보 또는 자세한 활동별 모니터링 환경을 볼 수 있는 아이콘 | 
| 실행 시작 | 활동 실행 의 시작 날짜 및 시간 (MM / DD / YYY, HH : MM : SS AM / PM) |
| Duration | 실행 기간 (HH:MM:SS) |
| 상태 | **실패한**, **성공한**것, **진행 중 이면**또는 **취소됨** |
| 통합 런타임 | 활동이 실행된 통합 런타임 |
| 사용자 속성 | 활동의 사용자 정의 속성 |
| Error | 활동이 실패하면 실행 오류가 발생합니다. |
| 실행 ID | 활동 실행 ID입니다. |

![활동 실행을 모니터링하기 위한 목록 보기](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>모니터링하도록 사용자 속성 승격

모든 파이프라인 활동 속성을 사용자 속성으로 승격하여 모니터링하는 엔터티가 되도록 합니다. 예를 들어 파이프라인에서 복사 활동의 **소스** 및 **대상** 속성을 사용자 속성으로 승격할 수 있습니다. 복사 활동에 대한 **소스** 및 **대상** 사용자 속성을 생성하려면 **자동 생성을** 선택합니다.

![사용자 속성 만들기](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 최대 5개의 파이프라인 활동 속성만 사용자 속성으로 승격할 수 있습니다.

사용자 속성을 만든 후 모니터링 목록 보기에서 모니터링할 수 있습니다. 복사 활동의 원본이 테이블 이름인 경우 활동 실행에 대한 목록 보기의 열로 원본 테이블 이름을 모니터링할 수 있습니다.

![사용자 속성을 제외한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image2.png)

![활동 실행 목록에 사용자 속성에 대한 열 추가](media/monitor-visually/monitor-user-properties-image3.png)

![사용자 속성의 열을 포함한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>목록 보기 구성

### <a name="order-and-filter"></a>주문 및 필터

파이프라인 실행이 실행 시작 시간에 따라 내림차순또는 오름차순인지 를 전환합니다. 필터 파이프라인은 다음 열을 사용하여 실행됩니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인 이름을 필터링합니다. |
| 실행 시작 |  표시된 파이프라인 실행의 시간 범위를 결정합니다. 옵션에는 **지난 24시간,** **지난 주**및 지난 **30일 동안의** 빠른 필터또는 사용자 지정 날짜 및 시간을 선택하는 빠른 필터가 포함됩니다. |
| 실행 상태 | 필터는 상태별로 실행됩니다: **성공,** **실패,** **큐에 대기됨,** **취소됨,** 또는 **진행 중**. |
| 주석 | 각 파이프라인에 적용된 태그별 필터 |
| 실행 | 다시 실행된 파이프라인을 볼지 여부를 필터링합니다. |

![필터링 옵션](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>열 추가 또는 제거
목록 보기 헤더를 마우스 오른쪽 단추를 마우스 오른쪽 단추를 클릭하고 목록 보기에 표시할 열을 선택합니다.

![열 옵션](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>열 너비 조정
열 헤더 위로 마우스를 가져가서 목록 보기의 열 너비를 늘리고 줄입니다.

## <a name="rerun-activities-inside-a-pipeline"></a>파이프라인 내부에서 활동 다시 실행

파이프라인 내에서 활동을 다시 실행할 수 있습니다. **활동 실행 보기를**선택한 다음 파이프라인을 다시 실행할 시점부터 파이프라인의 활동을 선택합니다.

![활동 실행 보기](media/monitor-visually/rerun-activities-image1.png)

![활동 실행 선택](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>실패한 활동에서 다시 실행

활동이 실패하거나 시간이 초과되거나 취소된 경우 실패한 활동에서 다시 실행을 선택하여 실패한 활동에서 파이프라인을 다시 실행할 수 **있습니다.**

![실패한 활동 다시 실행](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>다시 실행 기록 보기

목록 보기에서 모든 파이프라인 실행의 다시 실행 기록을 볼 수 있습니다.

![기록 보기](media/monitor-visually/rerun-history-image1.png)

특정 파이프라인 실행의 다시 실행 기록을 볼 수도 있습니다.

![파이프라인 실행의 기록 보기](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>간트 뷰

Gantt 보기를 사용하여 파이프라인 및 활동 실행을 빠르게 시각화합니다.

![간트 차트의 예](media/monitor-visually/gantt1.png)

파이프라인또는그룹별로 Gantt 뷰를 파이프라인에서 만든 주석/태그로 볼 수 있습니다.

![간트 차트 주석](media/monitor-visually/gantt2.png)

막대의 길이는 파이프라인의 기간을 알려줍니다. 막대를 선택하여 자세한 내용을 볼 수도 있습니다.

![간트 차트 지속 시간](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>둘러보기
왼쪽 하단의 **정보** 아이콘을 선택합니다. 그런 다음 **가이드 투어를** 선택하여 파이프라인 및 활동 실행을 모니터링하는 방법에 대한 단계별 지침을 얻을 수 있습니다.

![둘러보기](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>경고

Data Factory의 지원되는 메트릭에 대해 경고를 발생시킬 수 있습니다. 데이터**팩터리** 모니터링 페이지에서 경고 & 메트릭 **모니터링을** > 선택하여 시작합니다.

![데이터 팩토리 모니터 페이지](media/monitor-visually/alerts01.png)

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>경고 만들기

1.  **새 경고 규칙을** 선택하여 새 경고를 만듭니다.

    ![새 경고 규칙 단추](media/monitor-visually/alerts02.png)

1.  규칙 이름을 지정하고 경고 심각도를 선택합니다.

    ![규칙 이름 및 심각도에 대한 상자](media/monitor-visually/alerts03.png)

1.  경고 조건을 선택합니다.

    ![대상 기준에 대한 상자](media/monitor-visually/alerts04.png)

    ![기준 목록](media/monitor-visually/alerts05.png)

1.  경고 논리를 구성합니다. 모든 파이프라인 및 해당 활동의 선택한 메트릭에 대해 경고를 만들 수 있습니다. 특정 활동 유형, 활동 이름, 파이프라인 이름 또는 실패 유형을 선택할 수도 있습니다.

    ![경고 논리 를 구성하기 위한 옵션](media/monitor-visually/alerts06.png)

1.  경고에 대한 이메일, SMS, 푸시 및 음성 알림을 구성합니다. 경고 알림에 대해 작업 그룹을 만들거나 기존 그룹을 선택합니다.

    ![알림 구성 옵션](media/monitor-visually/alerts07.png)

    ![알림을 추가하기 위한 옵션](media/monitor-visually/alerts08.png)

1.  경고 규칙을 만듭니다.

    ![경고 규칙을 만들기 위한 옵션](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [파이프라인을 프로그래밍 방식으로 모니터링 및 관리문서를](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) 참조하십시오.
