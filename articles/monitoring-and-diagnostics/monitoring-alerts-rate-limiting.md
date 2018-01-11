---
title: "SMS, 메일, Azure 앱 푸시 알림 및 웹후크에 대한 속도 제한 | Microsoft Docs"
description: "작업 그룹에서 가능한 SMS, 메일, Azure 앱 푸시 또는 웹후크 알림의 수를 Azure에서 제한하는 방법을 알아봅니다."
author: dukek
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>SMS 메시지, 메일, Azure 앱 푸시 알림 및 웹후크 게시물에 대한 속도 제한
속도 제한은 특정 전화 번호, 메일 주소 또는 장치로 너무 많은 알림이 전송될 때 발생하는 알림의 일시 중단입니다. 속도를 제한하면 경고를 관리하고 실행할 수 있게 됩니다.

속도 제한 임계값은 다음과 같습니다.

 - **SMS**: 5분마다 SMS 1개 이하
 - **전자 메일**: 1시간에 100개 메시지
 - **Azure 앱 푸시 알림**: 푸시 알림에 대한 속도 제한은 없습니다.
 - **웹후크**: 웹후크에 대한 속도 제한은 없습니다.

## <a name="rate-limit-rules"></a>속도 제한 규칙
- 허용된 임계값보다 더 많은 메시지를 수신하는 경우 특정 전화 번호 또는 전자 메일의 속도가 제한됩니다.
- 전화 번호 또는 전자 메일은 많은 구독에서 작업 그룹에 속할 수 있습니다. 속도 제한은 모든 구독에 걸쳐 적용됩니다. 여러 구독에서 메시지가 전송되는 경우에도 임계값에 도달하면 즉시 적용됩니다.  
- 메일 주소의 속도가 제한되면 속도 제한을 알리기 위한 추가 알림이 전송됩니다. 속도 제한이 만료되면 알림에 표시됩니다.

## <a name="next-steps"></a>다음 단계 ##
* [SMS 경고 동작](monitoring-sms-alert-behavior.md)에 대해 자세히 알아보세요.
* [활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.  
* [서비스 상태 알림이 게시될 때마다 경고를 구성](monitoring-activity-log-alerts-on-service-notifications.md)하는 방법을 알아보세요.
