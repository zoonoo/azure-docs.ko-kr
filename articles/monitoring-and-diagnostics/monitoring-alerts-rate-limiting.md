---
title: "SMS, 전자 메일 및 웹후크에 대한 속도 제한 | Microsoft Docs"
description: "활동 로그에서 특정 이벤트가 발생하면 SMS, 웹후크 및 전자 메일을 통해 알림을 받습니다."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 1b4196d3b1d41458c7dd20b6986cc09100ae318c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/02/2017


---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>SMS, 전자 메일 및 웹후크에 대한 속도 제한
속도 제한은 특정 전화 번호나 전자 메일로 너무 많이 전송될 때 발생하는 알림 일시 중단입니다. 이러한 제한을 통해 활동 로그 경고 및 서비스 상태 경고에 대한 통신을 관리하고 조치를 수행할 수 있습니다.

SMS 및 전자 메일에 대한 규칙은 동일합니다. 속도 제한 임계값
 - SMS - 1시간에 10개 메시지
 - 전자 메일 - 1시간에 100개 메시지

## <a name="rate-limit-rules"></a>속도 제한 규칙
- 특정 전화 번호 또는 전자 메일은 임계값보다 더 많이 수신될 때 속도가 제한됩니다.
- 전화 번호 또는 전자 메일은 많은 구독에서 작업 그룹에 속할 수 있습니다. 속도 제한은 모든 구독에서 적용됩니다. 즉, 여러 구독에서 전송되더라도 임계값에 도달하는 즉시 적용됩니다.  
- 전화 번호 또는 전자 메일의 속도가 제한되면 동일한 유형의 추가 메시지가 속도 제한을 알리기 위해 전송됩니다. 이 SMS 또는 전자 메일은 속도 제한이 만료되는 시점을 알려 줍니다.

## <a name="rate-limit-of-webhooks"></a>웹후크의 속도 제한 ##
현재 웹후크에 대한 속도 제한은 없습니다.

## <a name="next-steps"></a>다음 단계 ##
[SMS 경고 동작](monitoring-sms-alert-behavior.md)에 대해 자세히 알아보기  
[활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 알림을 받는 방법 알아보기  
[서비스 상태 알림이 게시될 때마다 경고를 구성하는](monitoring-activity-log-alerts-on-service-notifications.md) 방법

