---
title: Azure platform logs 개요 | Microsoft Docs
description: Azure 리소스 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 하는 Azure의 진단 로그에 대 한 개요입니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1602b9beb5e667caf1688901c7ae4e67e5f5839
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262571"
---
# <a name="overview-of-azure-platform-logs"></a>Azure platform 로그 개요
플랫폼 로그는 Azure 리소스 및 해당 리소스가 종속 된 Azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다. 보존 하기 위해 특정 플랫폼 로그를 하나 이상의 대상으로 전달 하도록 구성 해야 하는 경우에도 자동으로 생성 됩니다. 이 문서에서는 제공 하는 정보와 수집 및 분석을 위해 구성 하는 방법을 비롯 하 여 플랫폼 로그의 개요를 제공 합니다.

## <a name="types-of-platform-logs"></a>플랫폼 로그 유형
다음 표에는 다양 한 Azure 계층에서 사용할 수 있는 특정 플랫폼 로그가 나열 되어 있습니다.

| 계층 | 로그 | 설명 |
|:---|:---|:---|
| Azure 리소스 | [리소스 로그](resource-logs-overview.md) | Azure 리소스 ( *데이터 평면*) 내에서 수행 된 작업에 대 한 통찰력을 제공 합니다. 예를 들어 Key Vault에서 비밀을 가져오거나 데이터베이스를 요청 하는 등의 작업을 수행할 수 있습니다. 리소스 로그의 콘텐츠는 Azure 서비스 및 리소스 유형에 따라 달라 집니다.<br>*리소스 로그는 이전에 진단 로그 라고 합니다.*  |
| Azure 구독 | [활동 로그](activity-logs-overview.md) | Service Health 이벤트에 대 한 업데이트 외에도 외부 (*관리 평면*)에서 구독의 각 Azure 리소스에 대 한 작업에 대 한 통찰력을 제공 합니다. 각 Azure 구독에 대 한 단일 활동 로그가 있습니다.   |
| Azure 테넌트 | [Azure Active Directory 로그](../../active-directory/reports-monitoring/overview-reports.md)  | 특정 테 넌 트에 대해 Azure Active Directory의 로그인 활동 기록과 변경 내용의 감사 내역을 포함 합니다.   |


![플랫폼 로그 개요](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>플랫폼 로그 보기
Azure Portal에서 [활동 로그](activity-log-view.md) 와 [Azure Active Directory 로그](../../active-directory/reports-monitoring/overview-reports.md) 를 볼 수 있습니다. 리소스 로그를 보려면 [대상](#destinations) 으로 리소스 로그를 보내야 합니다.


## <a name="destinations"></a>대상
모니터링 요구 사항에 따라 다음 표에 있는 하나 이상의 대상에 플랫폼 로그를 보낼 수 있습니다. 

| Destination | 시나리오 | 참조 |
|:---|:---|:---|:---|
| Log Analytics 작업 영역 | 다른 모니터링 데이터를 사용 하 여 로그를 분석 하 고 로그 쿼리 및 경고와 같은 Azure Monitor 기능을 활용 합니다. | [리소스 로그](resource-logs-collect-storage.md)<br>[활동 로그](activity-log-collect.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | 감사, 정적 분석 또는 백업용 로그를 보관 합니다. |[리소스 로그](archive-diagnostic-logs.md)<br>[활동 로그](activity-log-export.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hubs | 로그를 타사 로깅 및 원격 분석 시스템으로 스트리밍합니다.  |[리소스 로그](resource-logs-stream-event-hubs.md)<br>[활동 로그](activity-log-export.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>진단 설정 및 로그 프로필
[진단 설정을 만들어](diagnostic-settings.md)리소스 로그 및 Azure Active Directory 로그의 대상을 구성 합니다. [로그 프로필을 만들거나](activity-log-export.md) [Log Analytics 작업 영역에 연결](activity-log-collect.md)하 여 활동 로그의 대상을 구성 합니다.

진단 설정 및 로그 프로필은 다음을 정의 합니다.

- 선택한 로그 및 메트릭을 보낼 하나 이상의 대상입니다.
- 대상으로 전송 되는 리소스의 로그 범주 및 메트릭
- 저장소 계정을 대상으로 선택한 경우 각 로그 범주를 유지 해야 하는 기간입니다.



## <a name="next-steps"></a>다음 단계

* [활동 로그에 대 한 자세한 정보](activity-logs-overview.md)
* [리소스 로그에 대해 자세히 알아보기](resource-logs-overview.md)
* [다른 Azure 서비스에 대 한 리소스 로그 스키마 보기](diagnostic-logs-schema.md)
