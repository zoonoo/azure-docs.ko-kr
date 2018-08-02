---
title: Azure Monitor(미리 보기)를 사용하여 Splunk와 Azure Active Directory 로그를 통합하는 방법 | Microsoft Docs
description: Azure Monitor(미리 보기)를 사용하여 Splunk와 Azure Active Directory 로그를 통합하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240572"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Azure Monitor(미리 보기)를 사용하여 Splunk와 Azure Active Directory 로그 통합

이 문서에서는 Azure Monitor를 사용하여 Splunk와 Azure Active Directory 로그를 통합하는 방법을 알아봅니다. 먼저 Azure 이벤트 허브에 로그를 라우팅한 다음, Splunk와 통합해야 합니다.

## <a name="prerequisites"></a>필수 조건

1. Azure AD 활동 로그를 포함하는 Azure 이벤트 허브 [활동 로그를 이벤트 허브로 스트림](reporting-azure-monitor-diagnostics-azure-event-hub.md)하는 방법을 알아봅니다. 
2. 다음 [지침](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)을 사용하여 Splunk용 Azure Monitor 추가 기능을 다운로드하고 Splunk 인스턴스를 구성합니다.

## <a name="tutorial"></a>자습서 

1. Splunk 인스턴스를 열고 **데이터 요약**을 클릭합니다.
    ![데이터 요약](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "데이터 요약")

2. **Sourcetypes** 탭으로 이동하고 **amal: aadal:audit** ![Sourcetypes 탭](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes 탭")을 선택합니다.

3. 다음 그림에 나와 있는 것처럼 Azure AD 활동 로그가 표시됩니다.
    ![활동 로그](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "활동 로그")

> [!NOTE]
> Splunk 인스턴스에 추가 기능을 설치할 수 없는 경우(예: 프록시를 사용 중이거나 Splunk Cloud에서 실행 중인 경우) [이벤트 허브의 새 메시지로 트리거되는 이 Azure 함수](https://github.com/Microsoft/AzureFunctionforSplunkVS)를 사용하여 이러한 이벤트를 Splunk HTTP 이벤트 수집기로 전달할 수 있습니다. 
>

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Azure Monitor에서 로그인 로그 스키마 해석](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [질문과 대답 및 알려진 문제](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)