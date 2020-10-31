---
title: 자동 크기 조정 Stream Analytics 작업
description: 이 문서에서는 미리 정의 된 일정 또는 작업 메트릭의 값을 기반으로 Stream Analytics 작업을 자동 크기 조정 하는 방법을 설명 합니다.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: 8e5bcdaeaf1ec99387a708199f4353736b6bc60f
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129850"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Azure Automation를 사용 하 여 Stream Analytics 작업 자동 크기 조정

자동 크기 조정을 구성 하 여 Stream Analytics 작업의 비용을 최적화할 수 있습니다. 자동 크기 조정은 입력 로드의 변경과 일치 하도록 작업의 스트리밍 단위 (SUs)를 늘리거나 줄입니다. 작업을 과도 하 게 프로 비전 하는 대신 필요에 따라 확장 하거나 축소할 수 있습니다. 자동 크기 조정을 위해 작업을 구성 하는 방법에는 두 가지가 있습니다.
1. 예측 가능한 입력 로드가 있는 경우 **일정을 미리 정의** 합니다. 예를 들어 주간에는 더 높은 요금의 입력 이벤트를 사용 하 고 작업을 더 많은 su로 실행 하려고 합니다.
2. 예측 가능한 입력 로드가 없는 경우 **작업 메트릭에 따라 확장 및 축소 작업을 트리거합니다** . 입력 이벤트 수 또는 백로그 된 입력 이벤트와 같은 작업 메트릭에 따라 자동으로 SUs 수를 변경할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
작업에 대 한 자동 크기 조정을 구성 하기 전에 다음 단계를 완료 합니다.
1. 작업은 [병렬 토폴로지](./stream-analytics-parallelization.md)를 갖도록 최적화 되어 있습니다. 작업을 실행 하는 동안 작업의 크기를 변경할 수 있는 경우 작업에 병렬 토폴로지가 있으며 자동 크기 조정을 구성할 수 있습니다.
2. "RunAsAccount" 옵션을 사용 하도록 설정 하 여 [Azure Automation 계정을 만듭니다](../automation/automation-create-standalone-account.md) . 이 계정에는 Stream Analytics 작업을 관리할 수 있는 권한이 있어야 합니다.

## <a name="set-up-azure-automation"></a>Azure Automation 설정
### <a name="configure-variables"></a>변수 구성
Azure Automation 계정 내에 다음 변수를 추가 합니다. 이러한 변수는 다음 단계에서 설명 하는 runbook에 사용 됩니다.

| Name | Type | 값 |
| --- | --- | --- |
| **jobName** | String | 자동 크기 조정 하려는 Stream Analytics 작업의 이름입니다. |
| **resourceGroupName** | String | 작업이 있는 리소스 그룹의 이름입니다. |
| **subId** | String | 작업이 있는 구독 ID입니다. |
| **increasedSU** | 정수 | 작업을 일정에 따라 크기를 조정 하려는 상위 SU 값입니다. 이 값은 실행 중인 작업의 **크기 조정** 설정에 표시 되는 유효한 SU 옵션 중 하나 여야 합니다. |
| **decreasedSU** | 정수 | 일정에 따라 작업의 크기를 조정 하려는 하위 SU 값입니다. 이 값은 실행 중인 작업의 **크기 조정** 설정에 표시 되는 유효한 SU 옵션 중 하나 여야 합니다. |
| **maxSU** | 정수 | 부하가 자동으로 조정 될 때 작업을 수행 하려는 작업의 최대 SU 값입니다. 이 값은 실행 중인 작업의 **크기 조정** 설정에 표시 되는 유효한 SU 옵션 중 하나 여야 합니다. |
| **minSU** | 정수 | 부하가 자동으로 조정 될 때 작업을 수행 하려는 작업의 최소 SU 값입니다. 이 값은 실행 중인 작업의 **크기 조정** 설정에 표시 되는 유효한 SU 옵션 중 하나 여야 합니다. |

![Azure Automation에서 변수 추가](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Runbook 만들기
다음 단계는 두 개의 PowerShell runbook을 만드는 것입니다. 하나는 규모 확장을 위한 것이 고 다른 하나는 규모 축소 작업을 위한 것입니다.
1. Azure Automation 계정에서 **프로세스 자동화** 아래에 있는 **runbook** 으로 이동 하 고 **runbook 만들기** 를 선택 합니다.
2. PowerShell로 설정 된 유형으로 첫 번째 runbook의 이름을 *ScaleUpRunbook* 로 설정 합니다. GitHub에서 사용할 수 있는 [ScaleUpRunbook PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) 를 사용 합니다. 저장 하 고 게시 합니다.
3. PowerShell 유형을 사용 하 여 *ScaleDownRunbook* 라는 다른 runbook을 만듭니다. GitHub에서 사용할 수 있는 [ScaleDownRunbook PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) 를 사용 합니다. 저장 하 고 게시 합니다.

![Azure Automation의 자동 크기 조정 runbook](./media/autoscale/runbooks.png)

이제 Stream Analytics 작업에서 확장 및 축소 작업을 자동으로 트리거할 수 있는 runbook이 있습니다. 이러한 runbook은 미리 정의 된 일정을 사용 하 여 트리거하거나 작업 메트릭에 따라 동적으로 설정할 수 있습니다.

## <a name="autoscale-based-on-a-schedule"></a>일정에 따라 자동 크기 조정
Azure Automation를 사용 하 여 runbook을 트리거하기 위한 일정을 구성할 수 있습니다.
1. Azure Automation 계정에서 **공유 리소스** 아래에 있는 **일정** 을 선택 합니다. 그런 다음 **일정 추가** 를 선택 합니다.
2. 예를 들어 두 개의 일정을 만들 수 있습니다. 작업 규모를 확장 하 고 작업의 규모를 조정 하려는 경우를 나타내는 다른 하나를 표시 합니다. 이러한 일정에 대 한 되풀이를 정의할 수 있습니다.

   ![Azure Automation의 일정](./media/autoscale/schedules.png)

3. **ScaleUpRunbook** 을 열고 **리소스** 에서 **일정** 을 선택 합니다. 그런 다음 이전 단계에서 만든 일정에 runbook을 연결할 수 있습니다. 동일한 runbook을 사용 하 여 여러 일정을 연결 하 여 다른 시간에 동일한 크기 조정 작업을 실행 하려는 경우에 유용할 수 있습니다.

![Azure Automation에서 runbook 예약](./media/autoscale/schedulerunbook.png)

1. **ScaleDownRunbook** 에 대해 이전 단계를 반복 합니다.

## <a name="autoscale-based-on-load"></a>부하에 따라 자동 크기 조정
입력 로드를 예측할 수 없는 경우가 있을 수 있습니다. 이러한 경우에는 최소 및 최대 범위 내에서 단계를 확장/축소 하는 것이 더 적합 합니다. 작업 메트릭이 임계값을 초과 하거나 이하로 이동할 때 runbook을 트리거하기 위해 Stream Analytics 작업에서 경고 규칙을 구성할 수 있습니다.
1. Azure Automation 계정에서 **minsu** 및 **Maxsu** 라는 정수 변수를 두 개 더 만듭니다. 이렇게 하면 작업의 단계를 확장할 수 있는 범위가 설정 됩니다.
2. 두 개의 새 runbook을 만듭니다. **Maxsu** 값까지 작업의 SUs를 증가 시키는 [StepScaleUp PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) 를 사용할 수 있습니다. **Minsu** 값에 도달할 때까지 단계에서 작업의 su를 줄이는 [StepScaleDown PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) 를 사용할 수도 있습니다. 또는 크기를 조정 하려는 특정 SU 값이 있는 경우 이전 섹션의 runbook을 사용할 수 있습니다.
3. Stream Analytics 작업의 **모니터링** 아래에서 **경고 규칙** 을 선택 합니다. 
4. 두 개의 작업 그룹을 만듭니다. 하나는 규모 확장 작업에 사용 되 고 다른 하나는 규모 축소 작업에 사용 됩니다. **작업 관리** 를 선택 하 고 **작업 그룹 추가** 를 클릭 합니다. 
5. 필수 필드를 입력 합니다. **작업 유형을** 선택 하는 경우 **Automation Runbook** 을 선택 합니다. 경고가 발생 했을 때 트리거할 runbook을 선택 합니다. 그런 다음 작업 그룹을 만듭니다.

   ![작업 그룹 만들기](./media/autoscale/create-actiongroup.png)
6. 작업에서 [**새 경고 규칙**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) 을 만듭니다. 선택한 메트릭에 따라 조건을 지정 합니다. [ *입력 이벤트* , *SU% 사용률* 또는 *백로그 입력 이벤트*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) 는 자동 크기 조정 논리를 정의 하는 데 사용 하는 데 권장 되는 메트릭입니다. 또한 확장 작업을 트리거하는 경우 1 분 *집계 세분성* 및 *평가 빈도* 를 사용 하는 것이 좋습니다. 이렇게 하면 작업에 많은 양의 리소스가 포함 되어 입력 볼륨의 급격 한 급증을 처리할 수 있습니다.
7. 마지막 단계에서 만든 작업 그룹을 선택 하 고 경고를 만듭니다.
8. 작업 메트릭의 조건에 따라 트리거할 추가 크기 조정 작업을 수행 하려면 2 ~ 4 단계를 반복 합니다.

프로덕션 환경에서 작업을 실행 하기 전에 규모 테스트를 실행 하는 것이 가장 좋습니다. 다양 한 입력 로드에 대해 작업을 테스트 하는 경우 각 입력 처리량에 대해 작업에 필요한 SUs의 수를 알 수 있습니다. 이는 규모 확장 및 규모 축소 작업을 트리거하는 경고 규칙에서 정의한 조건을 알릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics에서 병렬 처리 가능 쿼리 만들기](stream-analytics-parallelization.md)
* [처리량을 늘리기 위해 Azure Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)