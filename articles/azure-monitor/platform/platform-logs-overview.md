---
title: Azure 플랫폼 로그 개요 | 마이크로 소프트 문서
description: Azure 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 Azure Monitor의 로그 개요입니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659323"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 플랫폼 로그 개요
플랫폼 로그는 Azure 리소스 및 해당 리소스가 종속된 Azure 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다. 유지될 하나 이상의 대상으로 전달하도록 특정 플랫폼 로그를 구성해야 하지만 자동으로 생성됩니다. 이 문서에서는 플랫폼 로그가 제공하는 정보와 수집 및 분석을 위해 플랫폼 로그를 구성하는 방법을 포함하여 플랫폼 로그에 대한 개요를 제공합니다.

## <a name="types-of-platform-logs"></a>플랫폼 로그 유형
다음 표에는 Azure의 여러 계층에서 사용할 수 있는 특정 플랫폼 로그가 나열되어 있습니다.

| 로그 | 계층 | 설명 |
|:---|:---|:---|
| 리소스 로그 | Azure 리소스 | Azure *리소스(데이터 평면)* 내에서 수행된 작업에 대한 통찰력을 제공합니다(예: Key Vault에서 비밀을 얻거나 데이터베이스에 대한 요청). 리소스 로그의 내용은 Azure 서비스 및 리소스 유형에 따라 다릅니다.<br><br>*리소스 로그를 이전에 진단 로그라고 했습니다.*  |
| 활동 로그 | Azure 구독 | 서비스 상태 이벤트에 대한 업데이트 외에도 외부(관리*평면)에서*구독의 각 Azure 리소스에 대한 작업에 대한 통찰력을 제공합니다. 활동 로그를 사용하여 _구독의_리소스에서 수행된 쓰기 작업(PUT, POST, DELETE)에 대해 _누가_및 _언제_ 수행할지 확인합니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다.  각 Azure 구독에 대한 단일 활동 로그가 있습니다. |
| Azure Active 디렉터리 로그 | Azure 테넌트 |  로그인 활동 기록 및 특정 테넌트에 대한 Azure Active Directory에서 변경한 내용의 감사 추적이 포함됩니다. Azure Active 디렉터리 로그에 대한 전체 설명은 [Azure Active Directory 보고서란 무엇입니까?를](../../active-directory/reports-monitoring/overview-reports.md) 참조하십시오.   |

> [!NOTE]
> Azure 활동 로그는 주로 Azure Resource Manager에서 발생하는 활동을 대상으로 합니다. 클래식/RDFE 모델을 사용하여 리소스를 추적하지 않습니다. 일부 클래식 리소스 유형(예: Microsoft.ClassicCompute)에는 Azure Resource Manager의 프록시 리소스 공급자가 있습니다. 이러한 프록시 리소스 공급자를 사용하는 Azure Resource Manager를 통해 클래식 리소스 유형과 상호 작용하면 작업이 활동 로그에 표시됩니다. Azure Resource Manager 프록시 외부의 클래식 리소스 유형과 상호 작용할 경우 사용자 작업은 작업 로그에만 기록됩니다. 작업 로그는 포털의 별도 섹션에서 탐색할 수 있습니다.

![플랫폼 로그 개요](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>플랫폼 로그 보기
다른 Azure 플랫폼 로그를 보고 분석하기 위한 다양한 옵션이 있습니다.

- Azure 포털에서 활동 로그를 보고 PowerShell 및 CLI에서 이벤트에 액세스합니다. 자세한 내용은 [Azure 활동 로그 이벤트 보기 및 검색을](activity-log-view.md) 참조하십시오. 
- Azure 포털에서 Azure Active Directory 보안 및 활동 보고서를 봅니다. [Azure Active 디렉터리 보고서란 무엇입니까?](../../active-directory/reports-monitoring/overview-reports.md)  참조하세요.
- 리소스 로그는 지원되는 Azure 리소스에 의해 자동으로 생성되지만 [대상으로](#destinations)보내지 않으면 볼 수 없습니다. 

## <a name="destinations"></a>Destinations
모니터링 요구 사항에 따라 다음 표의 하나 이상의 대상에 플랫폼 로그를 보낼 수 있습니다. 진단 설정을 만들어 플랫폼 로그에 대한 [대상을](diagnostic-settings.md)구성합니다.

| 대상 | 시나리오 | 참조 |
|:---|:---|:---|:---|
| Log Analytics 작업 영역 | 다른 모니터링 데이터로 로그를 분석하고 로그 쿼리 및 경고와 같은 Azure Monitor 기능을 활용합니다. | [활동 로그 및 리소스 로그](resource-logs-collect-workspace.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | 감사, 정적 분석 또는 백업을 위해 로그를 보관합니다. |[활동 로그 및 리소스 로그](archive-diagnostic-logs.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 이벤트 허브 | 로그를 타사 로깅 및 원격 분석 시스템으로 스트리밍합니다.  |[활동 로그 및 리소스 로그](resource-logs-stream-event-hubs.md)<br>[Azure 활동 디렉터리 로그](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>다음 단계

* [다른 범주에 대한 활동 로그 스키마 보기](activity-log-schema.md)
* [다른 Azure 서비스에 대한 리소스 로그 스키마 보기](diagnostic-logs-schema.md)
