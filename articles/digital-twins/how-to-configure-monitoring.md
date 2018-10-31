---
title: Azure Digital Twins에서 모니터링을 구성하는 방법 | Microsoft Docs
description: Azure Digital Twins에서 모니터링을 구성하는 방법
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: adgera
ms.openlocfilehash: 1c8f1931a29ae9769f7d8ad57a184e3240105a1a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945824"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Azure Digital Twins에서 모니터링을 구성하는 방법

Azure Digital Twins는 강력한 로깅, 모니터링 및 분석을 지원합니다. 솔루션 개발자는 Azure Log Analytics, 진단 로그, 활동 로깅 및 기타 서비스를 사용하여 IoT 앱의 복잡한 모니터링 요구 사항을 지원할 수 있습니다. 로깅 옵션을 결합하여 여러 서비스의 레코드를 쿼리 또는 표시하고 여러 서비스에 대한 세밀한 로깅 범위를 제공할 수 있습니다.

이 문서에서는 로깅 및 모니터링 옵션을 요약하고 Azure Digital Twins에 한정된 방식으로 로깅 및 모니터링 옵션을 결합하는 방법을 설명합니다.

## <a name="review-activity-logs"></a>활동 로그 검토

Azure [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 각 Azure 서비스 인스턴스의 구독 수준 이벤트 및 작업 기록에 대한 인사이트를 신속하게 제공합니다.

구독 수준 이벤트는 다음과 같습니다.

* 리소스 만들기
* 리소스 제거
* 앱 비밀 만들기
* 다른 서비스와 통합

Azure Digital Twins에 대한 활동 로깅이 기본적으로 사용되며 다음 방법을 사용하여 Azure Portal을 통해 확인할 수 있습니다.

1. Azure Digital Twins 인스턴스를 선택합니다.
1. **활동 로그**를 선택하여 디스플레이 패널을 불러옵니다.

    ![활동 로그][1]

고급 활동 로깅의 경우:

1. **로그** 옵션을 선택하여 **활동 로그 분석 개요**를 선택합니다.

    ![선택][2]

1. **활동 로그 분석 개요**는 필수 활동 로그 데이터를 요약합니다.

    ![활동 로그 분석 개요][3]

>[!TIP]
>**활동 로그**를 사용하여 구독 수준 이벤트에 대한 인사이트를 신속하게 얻을 수 있습니다.

## <a name="enable-customer-diagnostic-logs"></a>고객 진단 로그 사용

각 Azure 인스턴스에 대한 [진단 설정](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)을 설정하여 작업 로깅을 보완할 수 있습니다. 활동 로그는 구독 수준 이벤트와 관련이 있지만, 진단 로깅은 리소스 자체의 작업 기록에 대한 인사이트를 제공합니다.

다음은 진단 로깅의 예입니다.

* 사용자 정의 함수의 실행 시간
* 성공적인 API 요청의 응답 상태 코드
* 키 자격 증명 모음에서 앱 키 검색

인스턴스에 진단 로그를 사용하려면:

1. Azure Portal에서 리소스를 불러옵니다.
1. **진단 설정**을 클릭합니다.

    ![진단 설정 1][4]

1. **진단 켜기**를 클릭하여 데이터를 수집합니다(이전에 사용하도록 설정하지 않은 경우).
1. 요청된 필드에 정보를 입력하고 데이터를 저장할 방법 위치를 선택합니다.

    ![진단 설정 2][5]

    진단 로그는 종종 [Azure File Storage](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)를 사용하여 저장되고 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)와 공유됩니다. 두 옵션 모두 선택할 수 있습니다.

>[!TIP]
>**진단 로그**를 사용하여 리소스 작업에 대한 인사이트를 살펴봅니다.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor 및 Log Analytics

IoT 응용 프로그램은 서로 다른 리소스, 장치, 위치 및 데이터를 하나로 통합합니다. 세밀한 로깅은 전체 응용 프로그램 아키텍처의 각 부분, 서비스 또는 구성 요소에 대한 자세한 정보를 제공하지만, 유지 관리 및 디버깅을 위해 통합 개요가 필요한 경우가 많습니다.

Azure Monitor는 한 위치에서 로깅 원본을 살펴보고 분석할 수 있는 강력한 Log Analytics 서비스를 제공합니다. 따라서 Azure Monitor는 복잡한 IoT 앱 내에서 로그를 분석하는 데 매우 유용합니다.

다음은 사용 사례입니다.

* 여러 진단 로그 기록 쿼리
* 여러 사용자 정의 함수의 로그 검토
* 특정 시간 프레임 내에서 두 개 이상의 서비스에 대한 로그 표시

전체 로그 쿼리는 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries)를 통해 제공됩니다. 이러한 강력한 기능을 설정하려면:

1. Azure Portal에서 **Log Analytics**를 검색합니다.
1. 사용 가능한 **Log Analytics** 인스턴스가 나타납니다. 그 중 하나를 선택하고 쿼리할 **로그**를 선택합니다.

    ![Log Analytics][6]

1. 아직 **Log Analytics** 인스턴스가 없는 경우 **추가** 단추를 클릭하여 작업 영역을 만들 수 있습니다.

    ![OMS 만들기][7]

**Log Analytics** 인스턴스가 프로비전된 후에는 강력한 쿼리를 사용하여 여러 로그에서 항목을 찾거나 **로그 관리**를 사용하여 특정 조건을 검색할 수 있습니다.

   ![로그 관리][8]

강력한 쿼리 작업에 대한 자세한 내용은 [쿼리 시작하기](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)를 참조하세요.

> [!NOTE]
> **Log Analytics**에 이벤트를 처음으로 보낼 때 5분 지연이 발생할 수 있습니다.

Azure Log Analytics는 **문제 진단 및 해결**을 클릭하면 볼 수 있는 강력한 오류 및 경고 알림 서비스도 제공합니다.

   ![경고 및 오류 알림][9]

>[!TIP]
>**Log Analytics**를 사용하여 여러 앱 기능, 구독 또는 서비스에 대한 로그 기록을 쿼리할 수 있습니다.

## <a name="other-options"></a>기타 옵션

Azure Digital Twins는 응용 프로그램별 로깅 및 보안 감사도 지원합니다. Azure Digital Twins 인스턴스에 제공되는 모든 Azure 로깅 옵션의 개요는 [Azure 로그 감사](https://docs.microsoft.com/azure/security/azure-log-audit) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)에 대해 자세히 알아보세요.

[진단 로그 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)를 읽고 Azure 진단 설정에 대해 자세히 알아보세요.

[Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)에 대해 자세히 알아보세요.

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png