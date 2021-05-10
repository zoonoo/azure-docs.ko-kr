---
title: Azure 플랫폼 로그 개요| Microsoft Docs
description: Azure 리소스 작업에 관한 풍부한 데이터를 자주 제공하는 Azure Monitor 로그의 개요입니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: f2b22746b7a78349580220f4aa7579876fd21c33
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074593"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 플랫폼 로그 개요
플랫폼 로그에서 Azure 리소스 및 이에 따른 Azure 플랫폼에 관한 자세한 진단 및 감사 정보를 제공합니다. 특정 플랫폼 로그를 보존하기 위해 하나 이상의 대상으로 전달하도록 구성해야 하는 경우에도 자동으로 생성됩니다. 이 문서에서는 제공하는 정보와 수집 및 분석을 위해 구성하는 방법을 비롯하여 플랫폼 로그의 개요를 제공합니다.

## <a name="types-of-platform-logs"></a>플랫폼 로그 형식
다음 표에는 다양한 Azure 계층에서 사용할 수 있는 특정 플랫폼 로그가 나열되어 있습니다.

| 로그 | 계층 | 설명 |
|:---|:---|:---|
| [리소스 로그](./resource-logs.md) | Azure 리소스 | Azure 리소스(*데이터 평면*) 내에서 수행된 작업과 관련된 인사이트를 제공합니다. 예를 들어 Key Vault에서 비밀을 가져오거나 데이터베이스에 요청할 수 있습니다. 이러한 로그의 내용은 Azure 서비스와 리소스 종류에 따라 달라집니다.<br><br>*리소스 로그는 이전에 진단 로그라고 하였습니다.*  |
| [활동 로그](../essentials/activity-log.md) | Azure 구독 | Service Health 이벤트 업데이트 외에도 외부(*관리 평면*)에서 구독의 각 Azure 리소스 작업을 위한 인사이트를 제공합니다. 활동 로그를 사용하면 구독 리소스 관련 쓰기 작업(PUT, POST, DELETE)이 _무엇_ 이며 _누가_ _언제_ 수행했는지 판단할 수 있습니다. Azure 구독마다 하나의 활동 로그가 있습니다. |
| [Azure Active Directory 로그](../../active-directory/reports-monitoring/overview-reports.md) | Azure 테넌트 |  특정 테넌트에 관한 Azure Active Directory의 로그인 작업 기록과 변경 내용의 감사 내역을 포함합니다.   |

> [!NOTE]
> Azure 활동 로그는 주로 Azure Resource Manager에서 발생하는 활동을 대상으로 합니다. 클래식/RDFE 모델을 사용하여 리소스를 추적하지 않습니다. 일부 클래식 리소스 유형(예: Microsoft.ClassicCompute)에는 Azure Resource Manager의 프록시 리소스 공급자가 있습니다. 이러한 프록시 리소스 공급자를 사용하는 Azure Resource Manager를 통해 클래식 리소스 유형과 상호 작용하면 작업이 활동 로그에 표시됩니다. Azure Resource Manager 프록시 외부의 클래식 리소스 유형과 상호 작용할 경우 사용자 작업은 작업 로그에만 기록됩니다. 작업 로그는 포털의 별도 섹션에서 탐색할 수 있습니다.

![플랫폼 로그 개요](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>플랫폼 로그 보기
다양한 Azure 플랫폼 로그를 보고 분석하는 여러 가지 옵션이 있습니다.

- Azure Portal에서 활동 로그를 보고 PowerShell 및 CLI에서 이벤트에 액세스합니다. 자세한 내용은 [활동 로그 보기](../essentials/activity-log.md#view-the-activity-log)를 참조하세요. 
- Azure Portal에서 Azure Active Directory 보안 및 작업 보고서를 봅니다. [Azure Active Directory 보고서란?](../../active-directory/reports-monitoring/overview-reports.md)을 참고하세요.  참조하세요.
- 리소스 로그는 지원되는 Azure 리소스에 의해 자동으로 생성되지만 [대상](#destinations)에 전송하지 않는 한 볼 수 없습니다. 

## <a name="destinations"></a>대상
모니터링 요구 사항에 따라 다음 표에 있는 하나 이상의 대상에 플랫폼 로그를 보낼 수 있습니다. [진단 설정을 만들어](../essentials/diagnostic-settings.md) 플랫폼 로그의 대상을 구성합니다.

| 대상 | Description |
|:---|:---|
| Log Analytics 작업 영역 | 모든 Azure 리소스의 로그를 함께 분석하고 [로그 쿼리](../logs/log-query-overview.md) 및 [로그 경고](../alerts/alerts-log.md)를 포함하여 [Azure Monitor 로그](../logs/data-platform-logs.md)에 사용할 수 있는 모든 기능을 활용합니다. 로그 쿼리의 결과를 Azure 대시보드에 고정하거나 통합 문서에 대화형 보고서의 일부로 포함합니다. | 
| 이벤트 허브 | Azure 외부(예: 타사 SIEM 또는 사용자 지정 원격 분석 플랫폼)에 플랫폼 로그 데이터를 보냅니다. |
| Azure Storage | 감사 또는 백업용 로그를 보관합니다. |

- 활동 로그 또는 리소스 로그를 위한 진단 설정을 만드는 방법에 관한 자세한 내용은 [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../essentials/diagnostic-settings.md)를 참조하세요. 
- Azure Active Directory 로그를 위한 진단 설정을 만드는 방법에 관한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure Monitor 로그와 Azure AD 로그 통합](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).
  - [자습서: Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [자습서: Azure Storage 계정에 Azure AD 로그 보관](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>다음 단계

* [활동 로그에 관한 자세한 내용 읽기](../essentials/activity-log.md)
* [리소스 로그에 관한 자세한 내용 읽기](./resource-logs.md)
