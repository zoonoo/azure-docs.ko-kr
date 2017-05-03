---
title: "작업 그룹에서 SMS 경고 동작 | Microsoft Docs"
description: "SMS 메시지 형식 및 SMS 메시지에 대한 응답으로 구독 취소, 재구독 또는 도움을 요청합니다."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 10f33898fb86bd2449994a153d99cb59dc6078d6
ms.lasthandoff: 03/31/2017


---
# <a name="sms-alert-behavior-in-action-groups"></a>작업 그룹에서 SMS 경고 동작
## <a name="overview"></a>개요 ##
작업 그룹을 사용하여 수신자 목록을 구성할 수 있습니다. 그런 후 활동 로그 경고를 정의할 때 이러한 그룹을 사용할 수 있습니다. 예를 들어 활동 로그 경고가 트리거될 때 특정 작업 그룹이 알림을 받도록 할 수 있습니다. 지원되는 경고 메커니즘 중 하나가 SMS인 경우 경고에서 양방향 통신을 지원합니다. 사용자는 다음과 같이 경고에 응답할 수 있습니다.

- **경고에서 구독 취소:** 사용자는 모든 작업 그룹 또는 단일 작업 그룹에 대한 모든 SMS 경고에서 구독을 취소할 수 있습니다.  
- **경고 재구독:** 사용자는 모든 작업 그룹 또는 단일 작업 그룹에 대한 모든 SMS 경고를 재구독할 수 있습니다.  
- **도움 요청:** 사용자는 SMS에 대한 자세한 정보를 요청할 수 있습니다. 사용자는 이 문서로 리디렉션됩니다.

이 문서에서는 SMS 경고 동작과 사용자가 사용자의 로캘을 기반으로 취할 수 있는 응답 작업에 대해 다룹니다.

## <a name="usacanada-sms-behavior"></a>미국/캐나다 SMS 동작
### <a name="receiving-an-sms-alert"></a>SMS 경고 받기
작업 그룹의 일부로 구성된 SMS 수신자는 경고가 발생할 때 SMS를 수신합니다. SMS는 다음의 정보를 전달합니다.
* 이 경고가 전달된 작업 그룹의 짧은 이름
- 경고 제목

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>한 작업 그룹에 대한 SMS 경고에서 구독 취소
사용자는 “DISABLE &lt;작업 그룹의 짧은 이름&gt;” 키워드로 짧은 코드 20873에 응답하여 한 작업 그룹의 경고에 대한 SMS에서 구독 취소할 수 있습니다.

예: 짧은 이름이 “Azure”인 작업 그룹에 대한 경고를 구독 취소하려는 사용자는 “DISABLE Azure”라고 표시된 짧은 코드 20873에 SMS를 보냅니다.

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>모든 작업 그룹에 대한 SMS 경고에서 구독 취소
사용자는 다음 키워드로 짧은 코드 20873에 응답하여 모든 작업 그룹에 대한 모든 SMS 경고에서 구독 취소할 수 있습니다.
* STOP

예: 모든 작업 그룹에 대한 모든 SMS 경고를 구독 취소하려는 사용자는 “STOP”이라고 표시된 짧은 코드 20873에 SMS를 보냅니다.

>[!NOTE]
>사용자가 SMS 경고를 구독 취소했지만 새 작업 그룹에 추가된 경우 새 작업 그룹에 대한 SMS 경고를 받게 되지만 이전의 모든 작업 그룹에서 구독 취소 상태로 남아 있습니다.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>한 작업 그룹에 대한 SMS 경고 재구독
사용자는 “ENABLE &lt;작업 그룹의 짧은 이름&gt;” 키워드로 짧은 코드 20873에 응답하여 한 작업 그룹의 경고에 대한 SMS를 재구독할 수 있습니다.

예: 짧은 이름이 “Azure”인 작업 그룹에 대한 경고를 재구독하려는 사용자는 “ENABLE Azure”라고 표시된 짧은 코드 20873에 SMS를 보냅니다.

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>모든 작업 그룹에 대한 SMS 경고 재구독
사용자는 다음 키워드로 짧은 코드 20873에 응답하여 모든 작업 그룹에 대한 모든 SMS 경고를 재구독할 수 있습니다.

* START

예: 모든 작업 그룹에 대한 모든 SMS 경고를 구독 취소하려는 사용자는 “START”라고 표시된 짧은 코드 20873에 SMS를 보냅니다.

### <a name="requesting-help-via-sms"></a>SMS를 통해 도움 요청
사용자는 다음 키워드로 짧은 코드 20873에 응답하여 수신한 SMS에 대한 자세한 정보를 요청할 수 있습니다.
* HELP

응답은 이 문서에 대한 링크와 함께 사용자에게 전송됩니다.

## <a name="next-steps"></a>다음 단계
[활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 알림을 받는 방법 알아보기  
[SMS 속도 제한](monitoring-alerts-rate-limiting.md)에 대해 자세히 알아보기  
[작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기

