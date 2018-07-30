---
title: 자습서 - Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍(미리 보기) | Microsoft Docs
description: 이벤트 허브로 Azure Active Directory 로그를 푸시하도록 Azure 진단을 설정하는 방법 알아보기(미리 보기)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240220"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>자습서: Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍(미리 보기)

이 자습서에서는 Azure 이벤트 허브로 Azure Active Directory 로그를 스트리밍하도록 Azure Monitor 진단 설정을 지정하는 방법을 알아봅니다. 이 메커니즘을 사용하여 Splunk 및 QRadar와 같은 타사 SIEM 도구와 로그를 통합합니다.

## <a name="prerequisites"></a>필수 조건 

다음 작업을 수행해야 합니다.

* Azure 구독. Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure Active Directory 테넌트
* 테넌트의 전역 관리자 또는 보안 관리자인 사용자
* Azure 구독의 Event Hubs 네임스페이스 및 이벤트 허브. [여기](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md)에서 생성 방법을 알아보세요.

## <a name="archive-logs-to-event-hub"></a>이벤트 허브에 로그 보관

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory** -> **활동** -> **감사 로그**를 클릭합니다. 
3. **설정 내보내기**를 클릭하여 진단 설정 블레이드를 엽니다. 기존 설정을 변경하려는 경우 **설정 편집**을 클릭하고, 새로 추가하려면 **진단 설정 추가**를 클릭합니다. 최대 세 개의 설정을 지정할 수 있습니다. 
    ![설정 내보내기](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "설정 내보내기")

4. **이벤트 허브로 스트림** 확인란을 선택하고 **이벤트 허브/구성**을 클릭합니다.
5. 로그를 라우팅하려는 Azure 구독 및 Event Hubs 네임스페이스를 선택합니다. 구독 및 Event Hubs 네임스페이스는 로그가 스트리밍되는 Active Directory 테넌트와 연결되어야 합니다. 또한 로그를 전송해야 하는 Event Hubs 네임스페이스 내에 이벤트 허브를 지정할 수 있습니다. 이벤트 허브가 지정되지 않으면 기본 이름 **insights-logs-audit**를 사용하여 네임스페이스 내에 이벤트 허브가 생성됩니다.
6. **확인**을 클릭하여 이벤트 허브 구성을 종료합니다.
7. **AuditLogs** 확인란을 선택하여 저장소 계정에 감사 로그를 보냅니다. 
8. **SignInLogs** 확인란을 선택하여 저장소 계정에 로그인 로그를 보냅니다.
9. **저장**을 클릭하여 설정을 저장합니다.
    ![진단 설정](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "진단 설정")

10. 약 15분 후 이벤트에 이벤트 허브에 이벤트가 표시되는지 확인합니다. 이렇게 하려면 포털에서 이벤트 허브로 이동하고 **들어오는 메시지** 수가 0보다 큰지 확인합니다. 
    ![감사 로그](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "감사 로그")


## <a name="access-data-from-event-hubs"></a>Event Hubs에서 데이터 액세스

데이터가 이벤트 허브에 나타나면 두 가지 방법으로 액세스할 수 있습니다.

* **데이터를 읽도록 지원되는 SIEM 도구 구성**: 대부분의 도구에서는 이벤트 허브 연결 문자열과 Azure 구독에 대한 특정 권한을 사용하여 이벤트 허브에서 데이터를 읽을 수 있습니다. 다음은 Azure Monitor와 통합되는 완전하지 않은 도구 목록입니다.
    1. **Splunk**: Splunk와 Azure AD 로그를 통합하는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 Splunk와 Azure Active Directory 로그를 통합하는 방법](reporting-azure-monitor-diagnostics-splunk-integration.md)을 참조하세요.
    
    2. **IBM QRadar**: Microsoft Azure DSM 및 Microsoft Azure 이벤트 허브 프로토콜은 [IBM 지원 웹 사이트](http://www.ibm.com/support)에서 다운로드할 수 있습니다. [Azure와의 통합에 대해 여기서 자세히 알아볼 수 있습니다](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic**: 이벤트 허브에서 데이터를 사용하도록 SumoLogic을 설정하려면 [이 지침](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)을 따르세요. 

* **데이터를 읽도록 사용자 지정 도구 설정**: 현재 SIEM이 Azure 모니터 진단에서 아직 지원되지 않는 경우 이벤트 허브 API를 사용하여 사용자 지정 도구를 설정할 수 있습니다. 자세히 알아보려면 [이벤트 허브 API](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph)를 참조하세요.


## <a name="next-steps"></a>다음 단계

* [Azure Monitor 진단을 사용하여 Splunk와 Azure Active Directory 로그 통합](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Azure Monitor 진단에서 감사 로그 스키마 해석](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Azure Monitor 진단에서 로그인 로그 스키마 해석](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)