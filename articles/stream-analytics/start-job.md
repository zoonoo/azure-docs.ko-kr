---
title: Azure Stream Analytics 작업을 시작하는 방법
description: 이 문서에서는 Azure Portal, PowerShell 및 Visual Studio에서 Stream Analytics 작업을 시작하는 방법을 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79149d8e9862ece24b4b2da4c2ca4afcceb23d63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016255"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업을 시작하는 방법

Azure Stream Analytics 작업은 Azure Portal, Visual Studio 및 PowerShell을 사용하여 시작할 수 있습니다. 작업을 시작할 때 작업이 출력 생성을 시작하는 시간을 선택합니다. Azure Portal, Visual Studio 및 PowerShell은 각각 시작 시간을 설정하는 방법이 다릅니다. 이러한 방법은 아래에 설명되어 있습니다.

## <a name="start-options"></a>시작 옵션
작업을 시작하는 데 사용할 수 있는 세 가지 옵션은 다음과 같습니다. 아래에 언급된 모든 시간은 [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics)에 지정된 것입니다. TIMESTAMP BY를 지정하지 않으면 도착 시간이 사용됩니다.
* **Now**: 출력 이벤트 스트림의 시작 지점을 작업이 시작될 때와 동일하게 만듭니다. 시간 연산자(예: 시간 창, LAG 또는 JOIN)를 사용하는 경우 Azure Stream Analytics는 입력 원본의 데이터를 자동으로 다시 확인합니다. 예를 들어 "Now" 작업을 시작하고 쿼리가 5분 연속 창을 사용하는 경우 Azure Stream Analytics는 입력에서 5분 전의 데이터를 검색합니다.
첫 번째 가능한 출력 이벤트는 현재 시간보다 크거나 같은 타임스탬프를 가지며 ASA는 논리적으로 출력에 기여할 수 있는 모든 입력 이벤트가 고려되었음을 보장합니다. 예를 들어 부분 기간 집계는 생성되지 않습니다. 부분 기간 집계는 항상 완전한 집계 값입니다.

* **사용자 지정**: 출력의 시작 지점을 선택할 수 있습니다. **Now** 옵션과 마찬가지로 임시 연산자를 사용하는 경우 이 시간 전에 Azure Stream Analytics에서 데이터를 자동으로 읽습니다. 

* **마지막으로 중지된 시간**. 이 옵션은 작업이 이전에 시작되었지만 수동으로 중지되었거나 실패한 경우에만 사용할 수 있습니다. 이 옵션을 선택하면 Azure Stream Analytics는 마지막 출력 시간을 사용하여 데이터가 손실되지 않도록 작업을 다시 시작합니다. 이전 옵션과 마찬가지로 임시 연산자를 사용하는 경우 이 시간 전에 Azure Stream Analytics에서 데이터를 자동으로 읽습니다. 여러 입력 파티션의 시간이 다를 수 있기 때문에 모든 파티션의 가장 빠른 중지 시간이 사용되므로 일부 중복이 출력에 표시될 수 있습니다. 정확히 한 번 처리에 대한 자세한 내용은 [이벤트 배달 보장](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)에서 확인할 수 있습니다.


## <a name="azure-portal"></a>Azure portal

Azure Portal 작업으로 이동하고 개요 페이지에서 **시작** 을 선택합니다. **작업 출력 시작 시간** 을 선택한 다음, **시작** 을 선택합니다.

**작업 출력 시작 시간** 옵션 중 하나를 선택합니다. 옵션은 *지금*, *사용자 지정* 이며, 작업이 이전에 실행된 경우 *마지막으로 중지된 때* 입니다. 이 옵션에 대한 자세한 내용은 위를 참조하세요.

## <a name="visual-studio"></a>Visual Studio

작업 보기에서 녹색 화살표 단추를 선택하여 작업을 시작합니다. **작업 출력 시작 모드** 를 설정하고 **시작** 을 선택합니다. 작업 상태가 **실행 중** 으로 변경됩니다.

**작업 출력 시작 모드** 에는 *JobStartTime,* *CustomTime* 및 *LastOutputEventTime* 의 세 가지 옵션이 있습니다. 이 속성이 없는 경우 기본값은 *JobStartTime* 입니다. 이 옵션에 대한 자세한 내용은 위를 참조하세요.


## <a name="powershell"></a>PowerShell

PowerShell을 사용하여 작업을 시작하려면 다음 cmdlet을 사용합니다.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode** 에는 *JobStartTime*, *CustomTime* 및 *LastOutputEventTime* 의 세 가지 옵션이 있습니다. 이 속성이 없는 경우 기본값은 *JobStartTime* 입니다. 이 옵션에 대한 자세한 내용은 위를 참조하세요.

`Start-AzStreamAnalyitcsJob` cmdlet에 대한 자세한 내용은 [Start-AzStreamAnalyticsJob 참조](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Azure Stream Analytics Tools for Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)