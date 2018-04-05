---
title: Stream Analytics에서 쿼리에 대한 경고 설정 | Microsoft Docs
description: Stream Analytics 경고 이해
keywords: 경고 설정
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: jeanb
ms.openlocfilehash: b88d7e82ffcd2fab1845faf92f6d7a53b72cb54d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업에 대한 경고 설정
## <a name="introduction-monitor-page"></a>소개: 모니터 페이지
메트릭이 지정한 조건에 도달하면 경고를 트리거하도록 경고를 설정할 수 있습니다. 예를 들어 다음과 같은 조건에 대한 경고를 설정할 수 있습니다.

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

포털을 통해 메트릭에 대한 규칙을 설정하거나 [프로그래밍 방식](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)으로 작업 로그 데이터에 대한 규칙을 구성할 수 있습니다.

## <a name="set-up-alerts-in-the-azure-portal"></a>Azure Portal에서 경고 설정
1. Azure Portal에서 경고를 만들려는 Stream Analytics 작업을 엽니다. 

2. **작업** 블레이드에서 **모니터링** 섹션을 클릭합니다.  

3. **메트릭** 블레이드에서 **경고 추가** 명령을 클릭합니다.

      ![Azure Portal 설치](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. 이름과 설명을 입력합니다.

5. 경고가 전송될 조건을 정의하는 데 선택기를 사용합니다.

6. 경고를 전달할 위치에 대한 정보를 제공합니다.

      ![Azure Streaming Analytics 작업에 대한 경고 설정](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Azure Portal에서 경고를 구성에 대한 자세한 내용은 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조하세요.  


## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

