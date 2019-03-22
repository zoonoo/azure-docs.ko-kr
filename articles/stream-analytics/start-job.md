---
title: Azure Stream Analytics 작업을 시작 하는 방법
description: 이 문서에서는 Stream Analytics 작업을 시작 하는 방법을 설명 합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886850"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업을 시작 하는 방법

Azure portal, Visual Studio 및 PowerShell을 사용 하 여 Azure Stream Analytics 작업을 시작할 수 있습니다. 작업을 시작 하는 경우 작업 출력을 만들기 시작 시간을 선택 합니다. Azure portal, Visual Studio 및 각 PowerShell에는 시작 시간을 설정 하는 것에 대 한 가지 방법이 있습니다. 이러한 메서드는 다음과 같습니다.

## <a name="azure-portal"></a>Azure portal

Azure 포털 및 select 작업을 이동할 **시작** 개요 페이지에 있습니다. 선택 된 **작업 출력 시작 시간** 선택한 후 **시작**합니다.

에 대 한 세 가지 **작업 출력 시작 시간**: *이제*하십시오 *사용자 지정*, 및 *마지막으로 중지 된 경우*합니다. 선택 *이제* 현재 시간에 작업을 시작 합니다. 선택 *사용자 지정* 이전 또는 미래 작업을 사용자 지정 시간을 설정할 수 있습니다. 데이터 손실 없이 중지 된 작업을 다시 시작 하려면 *마지막으로 중지 된 경우*합니다.

## <a name="visual-studio"></a>Visual Studio

작업 보기에서 작업을 시작 하려면 녹색 화살표 단추를 선택 합니다. 설정 된 **작업 출력 시작 모드** 선택한 **시작**합니다. 작업 상태가으로 변경 됩니다 **실행**합니다.

에 대 한 세 가지 **작업 출력 시작 모드**: *JobStartTime*하십시오 *CustomTime*, 및 *LastOutputEventTime*합니다. 기본값은이 속성이 없으면 *JobStartTime*합니다.

*JobStartTime* 작업이 시작 될 때와 동일 하 게 스트림 출력 이벤트의 시작 지점을 만듭니다.

*CustomTime* 출력에 지정 된 사용자 지정 시간에 시작 합니다 *OutputStartTime* 매개 변수입니다.

*LastOutputEventTime* 마지막 이벤트로는 동일한 시간을 출력 하는 출력 이벤트 스트림의 시작 지점 수 있습니다.

## <a name="powershell"></a>PowerShell

PowerShell을 사용 하 여 작업을 시작 하려면 다음 cmdlet을 사용 합니다.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

에 대 한 세 가지 **OutputStartMode**: *JobStartTime*하십시오 *CustomTime*, 및 *LastOutputEventTime*합니다. 기본값은이 속성이 없으면 *JobStartTime*합니다.

*JobStartTime* 작업이 시작 될 때와 동일 하 게 스트림 출력 이벤트의 시작 지점을 만듭니다.

*CustomTime* 출력에 지정 된 사용자 지정 시간에 시작 합니다 *OutputStartTime* 매개 변수입니다.

*LastOutputEventTime* 마지막 이벤트로는 동일한 시간을 출력 하는 출력 이벤트 스트림의 시작 지점 수 있습니다.

에 대 한 자세한 합니다 `Start-AzStreamAnalyitcsJob` cmdlet을 보기는 [시작 AzStreamAnalyticsJob 참조](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure PowerShell을 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Azure Stream Analytics Tools for Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
