---
title: "SMS, 전자 메일 및 웹후크에 대한 속도 제한 | Microsoft Docs"
description: "활동 로그에서 특정 이벤트가 발생하면 SMS, 웹후크 및 전자 메일을 통해 알림을 받습니다."
author: anirudhcavale
manager: orenr
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b2e954405500921c0c1e9c7cd71ce57130c98d64
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>SMS, 전자 메일 및 웹후크에 대한 속도 제한
속도 제한은 특정 전화 번호나 전자 메일로 너무 많이 전송될 때 발생하는 알림 일시 중단입니다. 속도를 제한하면 경고를 관리하고 실행할 수 있게 됩니다.

SMS 및 전자 메일에 대한 규칙은 동일합니다. 속도 제한 임계값
 - SMS - 1시간에 10개 메시지
 - 전자 메일 - 1시간에 100개 메시지

## <a name="rate-limit-rules"></a>속도 제한 규칙
- 특정 전화 번호 또는 전자 메일은 임계값보다 더 많이 수신될 때 속도가 제한됩니다.
- 전화 번호 또는 전자 메일은 많은 구독에서 작업 그룹에 속할 수 있습니다. 속도 제한은 모든 구독에서 적용됩니다. 즉, 여러 구독에서 전송되더라도 임계값에 도달하는 즉시 적용됩니다.  
- 전화 번호 또는 전자 메일의 속도가 제한되면 속도 제한을 알리기 위한 추가 알림이 전송됩니다. 속도 제한이 만료되면 알림에 표시됩니다.

## <a name="rate-limit-of-webhooks"></a>웹후크의 속도 제한 ##
현재 웹후크에 대한 속도 제한은 없습니다.

## <a name="next-steps"></a>다음 단계 ##
[SMS 경고 동작](monitoring-sms-alert-behavior.md)에 대해 자세히 알아보기  
[활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 알림을 받는 방법 알아보기  
[서비스 상태 알림이 게시될 때마다 경고를 구성하는](monitoring-activity-log-alerts-on-service-notifications.md) 방법

