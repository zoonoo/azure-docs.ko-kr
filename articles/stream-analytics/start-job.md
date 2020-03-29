---
title: Azure 스트림 분석 작업을 시작하는 방법
description: 이 문서에서는 Azure 포털, PowerShell 및 Visual Studio에서 스트림 분석 작업을 시작하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426459"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure 스트림 분석 작업을 시작하는 방법

Azure 포털, Visual Studio 및 PowerShell을 사용하여 Azure 스트림 분석 작업을 시작할 수 있습니다. 작업을 시작할 때 작업에서 출력 만들기를 시작할 시간을 선택합니다. Azure 포털, Visual Studio 및 PowerShell은 각각 시작 시간을 설정하는 서로 다른 메서드를 가지고 있습니다. 이러한 방법은 아래에 설명되어 있습니다.

## <a name="start-options"></a>시작 옵션
작업을 시작하는 데 사용할 수 있는 세 가지 옵션을 사용할 수 있습니다. 아래에 언급된 모든 시간은 [타임스탬프 BY에](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)지정된 시간입니다. 타임스탬프 BY를 지정하지 않은 경우, 도착 시간이 사용됩니다.
* **이제**: 출력 이벤트 스트림의 시작점을 작업이 시작될 때와 동일하게 만듭니다. 임시 연산자(예: 시간 창, LAG 또는 JOIN)를 사용하는 경우 Azure Stream Analytics는 입력 소스의 데이터를 자동으로 다시 살펴봅니다. 예를 들어 작업을 "Now"로 시작하고 쿼리에서 5분 텀블링 창을 사용하는 경우 Azure Stream Analytics는 입력에서 5분 전의 데이터를 검색합니다.
가능한 첫 번째 출력 이벤트는 현재 시간과 같거나 큰 타임스탬프를 가지며 ASA는 출력에 논리적으로 기여할 수 있는 모든 입력 이벤트가 고려되었음을 보장합니다. 예를 들어, 부분 창이 있는 집계가 생성되지 않습니다. 항상 전체 집계 값입니다.

* **사용자 지정**: 출력의 시작 점을 선택할 수 있습니다. **지금** 옵션과 마찬가지로 Azure Stream Analytics는 임시 연산자가 사용되는 경우 이 시간 이전에 자동으로 데이터를 읽습니다. 

* **마지막으로 중지된 경우**. 이 옵션은 작업이 이전에 시작되었지만 수동으로 중지되거나 실패했을 때 사용할 수 있습니다. 이 옵션을 선택하면 Azure Stream Analytics는 마지막 출력 시간을 사용하여 작업을 다시 시작하여 데이터가 손실되지 않도록 합니다. 이전 옵션과 마찬가지로 Azure Stream Analytics는 임시 연산자가 사용되는 경우 이 시간 이전에 자동으로 데이터를 읽습니다. 여러 입력 파티션에 시간이 다를 수 있기 때문에 모든 파티션의 초기 중지 시간이 사용되므로 출력에서 일부 중복이 나타날 수 있습니다. 정확히 한 번 처리에 대한 자세한 내용은 이벤트 배달 보장 페이지에서 확인할 수 [있습니다.](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)


## <a name="azure-portal"></a>Azure portal

Azure 포털에서 작업으로 이동하여 개요 페이지에서 **시작을** 선택합니다. 작업 **출력 시작 시간을** 선택한 다음 **시작을**선택합니다.

작업 출력 시작 시간에 대한 옵션 중 하나를 **선택합니다.** 옵션은 *지금*, *사용자 지정*및 작업이 이전에 실행된 경우 마지막으로 *중지된 경우*입니다. 이러한 옵션에 대한 자세한 내용은 위를 참조하십시오.

## <a name="visual-studio"></a>Visual Studio

작업 보기에서 녹색 화살표 단추를 선택하여 작업을 시작합니다. 작업 **출력 시작 모드를** 설정하고 **시작**을 선택합니다. 작업 상태가 실행 **중으로**변경됩니다.

**작업 출력 시작 모드에는**작업 시작 *시간,* *사용자 지정 시간*및 *LastOutputEventTime의*세 가지 옵션이 있습니다. 이 속성이 없는 경우 기본값은 *JobStartTime*입니다. 이러한 옵션에 대한 자세한 내용은 위를 참조하십시오.


## <a name="powershell"></a>PowerShell

다음 cmdlet을 사용하여 PowerShell을 사용하여 작업을 시작합니다.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode**: *작업 시작 시간,* *사용자 지정 시간*및 *LastOutputEventTime에*대한 세 가지 옵션이 있습니다. 이 속성이 없는 경우 기본값은 *JobStartTime*입니다. 이러한 옵션에 대한 자세한 내용은 위를 참조하십시오.

`Start-AzStreamAnalyitcsJob` cmdlet에 대한 자세한 내용은 [Start-AzStreamAnalyticsJob 참조를](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure PowerShell을 사용하여 스트림 분석 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio용 Azure Stream Analytics 도구를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
