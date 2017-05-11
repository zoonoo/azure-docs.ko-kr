---
title: "Azure Stream Analytics로 이벤트 순서 및 지연 처리 | Microsoft Docs"
description: "Stream Analytics가 데이터 스트림의 잘못된 순서 또는 지연 이벤트에 어떻게 작동하는지 알아봅니다."
keywords: "순서 비지정, 지연, 이벤트"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 02c854727e5185968dba49233df82ea6ff336ec8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/01/2017


---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure Stream Analytics 이벤트 순서 처리

이벤트의 임시 데이터 스트림에서 각 이벤트는 이벤트가 수신되는 시간이 함께 기록됩니다. 일부 조건에서는 이벤트 스트림이 경우에 따라 일부 이벤트를 전송된 것과는 다른 순서로 수신할 수 있습니다. 간단한 TCP 재전송 또는 전송 장치와 수신 이벤트 허브 간 시계 기울이기에서 이 같은 현상이 발생할 수 있습니다. 또한 “문장 부호” 이벤트도 수신된 이벤트 스트림에 추가되어 이벤트가 도착하지 않을 때 시간을 앞당깁니다. 이러한 사항은 “3분 동안 로그인이 발생하지 않을 때 알림”과 같은 시나리오에 필요합니다.

순서가 올바르지 않은 입력 스트림은 다음 중 하나입니다.
* 정렬되었습니다(따라서 **지연됨**).
* 사용자 지정 정책에 따라 시스템에서 조정되었습니다.


## <a name="lateness-tolerance"></a>지연 허용 오차
Stream Analytics는 이러한 시나리오 형식을 허용합니다. Stream Analytics는 “잘못된 순서” 및 “지연” 이벤트를 처리합니다. 다음과 같은 방법으로 이러한 이벤트를 처리합니다.

* 순서가 올바르지 않으나 설정된 허용 오차 내로 도착하는 이벤트는 **타임스탬프 기준으로 다시 정렬**합니다.
* 허용 오차보다 나중에 도착하는 이벤트는 **삭제 또는 조정**합니다.
    * **조정**: 최신 허용 가능한 시간에 도착한 것처럼 조정합니다.
    * **삭제**: 삭제합니다.

![Stream Analytics 이벤트 처리](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>잘못된 순서 이벤트의 수 줄이기

Stream Analytics는 들어오는 이벤트를 처리할 때 임시 변환을 적용하기 때문에(예: 기간 이동 집계 또는 임시 연결) Stream Analytics는 들어오는 이벤트를 타임스탬프 순서대로 정렬합니다.

“타임스탬프 기준” 키워드가 사용되지 **않을** 경우 Azure Event Hubs 이벤트 큐에 넣기 시간이 기본적으로 사용됩니다. 이벤트 허브는 이벤트 허브의 각 파티션에 있는 타임스탬프의 단조성을 보장합니다. 또한 모든 파티션의 이벤트가 타임스탬프 순서대로 병합되는 것을 보장합니다. 이러한 두 이벤트 허브 보증을 통해 잘못된 순서 이벤트를 없앨 수 있습니다.

경우에 따라 보낸 사람의 타임스탬프를 사용하는 것이 중요합니다. 이 경우 이벤트 페이로드의 타임스탬프가 “타임스탬프 기준”을 사용하여 선택됩니다. 이러한 시나리오에서는 잘못 정렬된 이벤트의 원본이 하나 이상 도입될 수도 있습니다.

* 이벤트 생산자에 클록 오차가 있습니다. 이는 생산자가 다른 컴퓨터에 있어 다른 클록을 사용할 때 일반적입니다.
* 이벤트 원본에서 대상 이벤트 허브로 네트워크 지연이 발생합니다.
* 클록 오차는 이벤트 허브 파티션 간에 존재합니다. Stream Analytics는 먼저 모든 이벤트 허브 파티션의 이벤트를 이벤트 큐에 넣기 시간으로 정렬합니다. 그런 다음 순서가 잘못된 이벤트에 대해 데이터 스트림을 검사합니다.

구성 탭에 다음과 같은 기본값이 표시됩니다.

![Stream Analytics의 잘못된 순서 처리](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

잘못된 순서 허용 시간으로 0초를 사용하는 경우 모든 이벤트를 항상 순서대로 되어 있도록 어설션합니다. 순서가 잘못된 세 개의 이벤트 원본을 고려해 볼 때 그렇게 한 것 같지는 않습니다. 

Stream Analytics가 이벤트 순서 이상을 수정하도록 하려면 0이 아닌 잘못된 순서 허용 시간을 지정할 수 있습니다. Stream Analytics는 이벤트를 해당 창까지 버퍼링한 다음 사용자가 선택한 타임스탬프를 사용하여 순서를 다시 정렬합니다. 그런 다음 임시 변환을 적용합니다. 3초 창에서 시작할 수 있으며 값을 조정하여 시간이 조정된 이벤트의 수를 줄일 수 있습니다. 

버퍼링의 부작용은 출력이 **동일한 시간만큼 지연**된다는 것입니다. 값을 조정하여 잘못된 순서 이벤트의 수를 줄이고 작업 대기 시간을 낮게 유지할 수 있습니다.

## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-get-started.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
