---
title: Stream Analytics 작업 자동 크기 조정
description: 이 문서에서는 미리 정의된 일정 또는 작업 메트릭 값을 기반으로 Stream Analytics 작업을 자동 크기 조정하는 방법을 설명합니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016289"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Azure Automation을 사용하여 Stream Analytics 작업 자동 크기 조정

자동 크기 조정을 구성하여 Stream Analytics 작업의 비용을 최적화할 수 있습니다. 자동 크기 조정은 입력 로드의 변경과 일치 하도록 작업의 스트리밍 단위(SU)를 늘리거나 줄입니다. 작업을 과도하게 프로 비전하는 대신 필요에 따라 스케일 업하거나 스케일 다운할 수 있습니다. 자동 크기 조정을 위해 작업을 구성하는 두 가지 방법이 있습니다.
1. 예측 가능한 입력 로드가 있는 경우 **일정을 미리 정의** 합니다. 예를 들어 주간의 더 높은 입력 이벤트 비율을 예상하고 더 많은 SU로 작업을 실행하려고 합니다.
2. 예측 가능한 입력 로드가 없는 경우 **작업 메트릭에 따라 스케일 업 및 스케일 다운 작업을 트리거합니다**. 입력 이벤트 수 또는 백로그된 입력 이벤트와 같은 작업 메트릭에 따라 동적으로 SU 수를 변경할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
작업에 대한 자동 크기 조정을 구성하기 전에 다음 단계를 완료합니다.
1. 작업은 [병렬 토폴로지](./stream-analytics-parallelization.md)를 갖도록 최적화되어 있습니다. 작업을 실행하는 동안 작업의 크기를 변경할 수 있는 경우 작업에 병렬 토폴로지가 있으며 자동 크기 조정을 구성할 수 있습니다.
2. "RunAsAccount" 옵션을 사용하도록 설정하여 [Azure Automation 계정을 만듭니다](../automation/automation-create-standalone-account.md). 이 계정에는 Stream Analytics 작업을 관리할 수 있는 권한이 있어야 합니다.

## <a name="set-up-azure-automation"></a>Azure Automation 설정
### <a name="configure-variables"></a>변수 구성
Azure Automation 계정 내에 다음 변수를 추가합니다. 이러한 변수는 다음 단계에서 설명하는 Runbook에 사용됩니다.

| 이름 | 유형 | 값 |
| --- | --- | --- |
| **jobName** | String | 자동 크기 조정하려는 Stream Analytics 작업의 이름입니다. |
| **resourceGroupName** | String | 작업이 있는 리소스 그룹의 이름입니다. |
| **subId** | String | 작업이 있는 구독 ID입니다. |
| **increasedSU** | 정수 | 일정에 따라 작업의 크기를 조정하려는 상위 SU 값입니다. 이 값은 작업 실행 중 작업의 **크기 조정** 설정에 표시되는 유효한 SU 옵션 중 하나여야 합니다. |
| **decreasedSU** | 정수 | 일정에 따라 작업의 크기를 조정하려는 하위 SU 값입니다. 이 값은 작업 실행 중 작업의 **크기 조정** 설정에 표시되는 유효한 SU 옵션 중 하나여야 합니다. |
| **maxSU** | 정수 | 로드를 통해 자동 크기 조정하는 단계에서 작업 크기를 조정하려는 최대 SU 값입니다. 이 값은 작업 실행 중 작업의 **크기 조정** 설정에 표시되는 유효한 SU 옵션 중 하나여야 합니다. |
| **minSU** | 정수 | 로드를 통해 자동 크기 조정하는 단계에서 작업 크기를 조정하려는 최소 SU 값입니다. 이 값은 작업 실행 중 작업의 **크기 조정** 설정에 표시되는 유효한 SU 옵션 중 하나여야 합니다. |

![Azure Automation에서 변수 추가](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Runbook 만들기
다음 단계는 두 개의 PowerShell Runbook을 만드는 것입니다. 하나는 스케일 업 작업용이고 다른 하나는 스케일 다운 작업용입니다.
1. Azure Automation 계정에서 **프로세스 자동화** 의 **Runbook** 으로 이동하고 **Runbook 만들기** 를 선택합니다.
2. 형식이 PowerShell로 설정된 첫 번째 Runbook인 *ScaleUpRunbook* 의 이름을 지정합니다. GitHub에서 사용할 수 있는 [ScaleUpRunbook PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1)를 사용합니다. 저장하고 게시합니다.
3. PowerShell 형식의 *ScaleDownRunbook* 이라는 다른 Runbook을 만듭니다. GitHub에서 사용할 수 있는 [ScaleDownRunbook PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1)를 사용합니다. 저장하고 게시합니다.

![Azure Automation에서 Runbook 자동 크기 조정](./media/autoscale/runbooks.png)

이제 Stream Analytics 작업에서 스케일 업 및 스케일 다운 작업을 자동으로 트리거할 수 있는 Runbook이 있습니다. 이러한 Runbook은 미리 정의된 일정을 사용하여 트리거하거나 작업 메트릭에 따라 동적으로 설정할 수 있습니다.

## <a name="autoscale-based-on-a-schedule"></a>일정에 따라 자동 크기 조정
Azure Automation을 사용하면 Runbook을 트리거하기 위한 일정을 구성할 수 있습니다.
1. Azure Automation 계정의 **공유 리소스** 에서 **일정** 을 선택합니다. 그런 다음 **일정 추가** 를 선택합니다.
2. 예를 들어 두 개의 일정을 만들 수 있습니다. 하나는 작업을 스케일 업할 때를 나타내고 다른 하나는 작업을 스케일 다운할 때를 나타냅니다. 이러한 일정에 대한 되풀이를 정의할 수 있습니다.

   ![Azure Automation의 일정](./media/autoscale/schedules.png)

3. **ScaleUpRunbook** 을 열고 **리소스** 에서 **일정** 을 선택합니다. 그런 다음 이전 단계에서 만든 일정에 Runbook을 연결할 수 있습니다. 동일한 Runbook을 사용하여 여러 일정을 연결할 수 있으며 다른 시간에 동일한 크기 조정 작업을 실행하려는 경우에 유용할 수 있습니다.

![Azure Automation에서 Runbook 일정 예약](./media/autoscale/schedulerunbook.png)

1. **ScaleDownRunbook** 에 대해 이전 단계를 반복합니다.

## <a name="autoscale-based-on-load"></a>로드에 따른 자동 크기 조정
입력 로드를 예측할 수 없는 사례가 있을 수 있습니다. 이러한 경우에는 최소 및 최대 경계 내에서 단계를 스케일 업/스케일 다운하는 것이 더 적합합니다. 작업 메트릭이 임계값을 초과하거나 이하로 이동할 때 Runbook을 트리거하기 위해 Stream Analytics 작업에서 경고 규칙을 구성할 수 있습니다.
1. Azure Automation 계정에서 **minSU** 및 **maxSU** 라는 정수 변수를 두 개 더 만듭니다. 이렇게 하면 작업의 단계를 스케일 인할 수 있는 범위가 설정됩니다.
2. 두 개의 새 Runbook을 만듭니다. [StepScaleUp PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1)를 사용하여 **maxSU** 값까지 작업 SU를 증분 단위로 늘릴 수 있습니다. [StepScaleDown PowerShell 스크립트](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1)를 사용하여 **minSU** 값에 도달할 때까지 단계별로 작업 SU도 줄일 수 있습니다. 또는 크기를 조정하려는 특정 SU 값이 있는 경우 이전 섹션의 Runbook을 사용할 수 있습니다.
3. Stream Analytics 작업의 **모니터링** 에서 **경고 규칙** 을 선택합니다. 
4. 두 개의 작업 그룹을 만듭니다. 하나는 스케일 업에 사용되고 다른 하나는 스케일 다운에 사용됩니다. **작업 관리** 를 선택한 다음 **작업 그룹 추가** 를 클릭합니다. 
5. 필수 필드를 입력합니다. **작업 형식** 을 선택하는 경우 **Automation Runbook** 을 선택합니다. 경고가 발생할 때 트리거하려는 Runbook을 선택합니다. 그런 다음 작업 그룹을 만듭니다.

   ![작업 그룹 만들기](./media/autoscale/create-actiongroup.png)
6. 작업에 [**새 경고 규칙**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal)을 만듭니다. 선택한 메트릭에 따라 조건을 지정합니다. [*입력 이벤트*, *SU% 사용률* 또는 *백로그된 입력 이벤트*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)는 자동 조정 논리를 정의하는 데 사용할 권장 메트릭입니다. 또한 스케일 업 작업을 트리거할 때 1분 *집계 세분성* 및 *평가 빈도* 를 사용하는 것이 좋습니다. 이렇게 하면 입력 볼륨의 큰 급증에 대처할 수 있는 충분한 리소스가 작업에 확보됩니다.
7. 마지막 단계에서 만든 작업 그룹을 선택하고 경고를 만듭니다.
8. 작업 메트릭 조건에 따라 트리거하려는 추가 크기 조정 작업에 대해 2~4단계를 반복합니다.

프로덕션에서 작업을 실행하기 전에 크기 조정 테스트를 실행하는 것이 모범 사례입니다. 다양한 입력 로드에 대해 작업을 테스트할 때 다양한 입력 처리량에 대해 작업에 필요한 SU 수를 알 수 있습니다. 이렇게 하면 스케일 업 및 스케일 다운 작업을 트리거하는 경고 규칙에 정의한 조건을 알릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics에서 병렬 처리 가능 쿼리 만들기](stream-analytics-parallelization.md)
* [처리량을 높이기 위한 Azure Stream Analytics 작업 비율 크기 조정](stream-analytics-scale-jobs.md)