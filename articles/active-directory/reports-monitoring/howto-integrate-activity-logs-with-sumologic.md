---
title: Azure Monitor(미리 보기)를 사용하여 SumoLogic과 Azure Active Directory 로그를 통합하는 방법 | Microsoft Docs
description: Azure Monitor(미리 보기)를 사용하여 SumoLogic과 Azure Active Directory 로그를 통합하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d13eb22bd58dc7e680a27738549665bc2b691898
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392213"
---
# <a name="integrate-azure-ad-logs-with-sumologic-by-using-azure-monitor-preview"></a>Azure Monitor(미리 보기)를 사용하여 SumoLogic과 Azure AD 로그 통합

이 문서에서는 Azure Monitor를 사용하여 SumoLogic과 Azure AD(Azure Active Directory) 로그를 통합하는 방법을 알아봅니다. 먼저 Azure 이벤트 허브에 로그를 라우트한 다음, SumoLogic과 이벤트 허브를 통합합니다.

## <a name="prerequisites"></a>필수 조건

이 기능을 사용하려면 다음이 필요합니다.
* Azure AD 활동 로그를 포함하는 Azure 이벤트 허브입니다. [활동 로그를 이벤트 허브로 스트림](quickstart-azure-monitor-stream-logs-to-event-hub.md)하는 방법을 알아봅니다. 
* SumoLogic Single Sign-On이 설정된 구독

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>SumoLogic과 Azure AD 로그를 통합하는 단계 

1. 먼저 [Azure 이벤트 허브로 Azure AD 로그를 스트리밍](quickstart-azure-monitor-stream-logs-to-event-hub.md)합니다.
2. [Azure Active Directory에 대한 로그를 수집](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)하도록 SumoLogic 인스턴스를 구성합니다.
3. [Azure AD SumoLogic 앱을 설치](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)하여 환경의 실시간 분석을 제공하는 미리 구성된 대시보드를 사용합니다.

 ![대시보드](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor에서 로그인 로그 스키마 해석](reference-azure-monitor-sign-ins-log-schema.md)
* [질문과 대답 및 알려진 문제](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
