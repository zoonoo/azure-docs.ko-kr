---
title: OMS 포털에서 Azure Portal로 전환하는 Log Analytics 사용자를 위한 일반적인 질문 | Microsoft Docs
description: OMS 포털에서 Azure Portal로 이전하는 Log Analytics 사용자를 위한 일반적인 질문에 대답합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.openlocfilehash: b353e3c0ed14c7ae42e6b1f2c8a92904bf0befd7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916288"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>OMS 포털에서 Azure Portal로 전환하는 Log Analytics 사용자를 위한 일반적인 질문
Log Analytics는 처음에 OMS 포털이라는 자체 포털을 사용하여 구성을 관리하고 수집된 데이터를 분석했습니다.  이 포털의 모든 기능은 Azure Portal로 이동되었으며 계속 개발될 예정입니다.

이 문서에서는 이 전환을 수행하는 사용자를 위한 일반적인 질문에 대답합니다.  OMS 포털에서 Log Analytics를 사용한 경우 여기서 Azure Portal에서 동일한 작업을 수행할 수 있는 방법을 찾을 수 있습니다.

## <a name="do-i-need-to-migrate-anything"></a>마이그레이션해야 하는 사항이 있나요?
아니요. Log Analytics 자체는 변경되지 않으므로 마이그레이션해야 하는 사항은 없습니다. Log Analytics에 액세스하는 데 사용하는 인터페이스만 변경됩니다. 실제로, 현재 OMS 포털에서 사용하는 것과 동일한 작업 영역, 솔루션, 뷰 및 로그 검색을 Azure Portal에서 액세스할 수 있습니다.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Azure에서 Log Analytics를 어디서 찾을 수 있습니까?
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.  **모든 서비스**를 클릭하고 리소스 목록에서 **Log Analytics**를 입력합니다. **Log Analytics**를 선택한 다음, 사용자의 작업 영역을 선택합니다. 작업 영역에 대한 요약 페이지가 표시됩니다.

![Log Analytics 작업 영역](media/oms-portal-faq/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>사용 권한을 관리하려면 어떻게 해야 하나요?
Azure Portal에서 Log Analytics 작업 영역에 액세스할 없는 경우 [Azure 역할 기반 액세스](../../role-based-access-control/role-assignments-portal.md)를 사용하여 사용자의 사용 권한을 구성해야 합니다. 작업 영역 사용 권한 관리에 대한 자세한 내용은 [작업 영역 관리](manage-access.md#manage-accounts-and-users)를 참조하세요. 경고에 대한 사용 권한 관리에 대한 자세한 내용은 [Azure Monitor에서의 역할, 권한 및 보안 시작](../../azure-monitor/platform/roles-permissions-security.md)을 참조하세요.

## <a name="how-do-i-create-a-new-workspace"></a>새 작업 영역을 만들려면 어떻게 해야 하나요? 
Azure Portal의 작업 영역 목록에서 **추가**를 클릭합니다.  전체 세부 사항은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../learn/quick-create-workspace.md)를 참조하세요.

![개요 페이지](media/oms-portal-faq/new-workspace.png)

## <a name="where-is-my-overview-page"></a>개요 페이지는 어디에 있나요?
OMS 포털의 메인 화면에는 사용자가 만든 모든 사용자 지정 보기 및 작업 영역에 설치된 모든 관리 솔루션에 대한 타일이 표시됩니다. 이 동일한 보기는 Azure Portal에서 사용할 수 있습니다. 작업 영역에서 **작업 영역 요약**을 선택합니다.

![개요 페이지](media/oms-portal-faq/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>로그 검색 및 뷰 디자이너를 열려면 어떻게 해야 하나요?
**로그 검색** 및 **뷰 디자이너**는 모두 메인 페이지에서 사용할 수 있습니다. Azure Portal의 사용자 작업 영역의 왼쪽 메뉴에서 **개요** 바로 옆에 있습니다.

## <a name="where-do-i-find-settings"></a>설정은 어디에 있나요?
OMS 포털의 **설정** 섹션에 있는 대부분의 설정은 작업 영역을 위한 Azure Portal의 **고급 설정** 메뉴에서 사용할 수 있습니다.

![고급 설정](media/oms-portal-faq/advanced-settings.png)

다음 섹션에서는 이전에 OMS 포털의 **설정** 섹션에서 제공되었던 설정에 액세스할 수 있는 방법의 전체 목록을 제공합니다.

### <a name="accounts"></a>계정 
계정 설정은 다음 표에 설명된 대로 Azure Portal에서 서로 다른 위치에서 관리됩니다.

| OMS 포털의 설정 | Azure Portal의 동일한 설정 |
|:---|:---|
| Automation 계정 | 작업 영역에 대한 **자동화 계정** 메뉴입니다. |
| Azure 구독 및 데이터 요금제 | 작업 영역에 대한 **가격 책정 계층** 메뉴입니다. |
| 사용자 관리 | Azure 역할 기반 액세스를 사용하여 [사용자 작업 영역에 대한 사용 권한을 관리](#how-do-i-manage-permissions)합니다. |
| 작업 영역 정보 | 작업 영역에 대한 **OMS 작업 영역** 메뉴에서 사용할 수 있는 정보입니다. |

### <a name="alerts"></a>경고
Log Analytics 쿼리를 기반으로 하는 경고 규칙은 이제 [통합된 경고 환경](#how-do-i-create-and-manage-alerts)에서 관리됩니다. 

### <a name="computer-groups"></a>컴퓨터 그룹
작업 영역에 대한 **고급 설정** 메뉴에서 컴퓨터 그룹을 관리합니다. 

### <a name="connected-sources"></a>연결된 소스
대부분의 연결된 소스 설정은 작업 영역에 대한 **고급 설정** 메뉴에서 관리합니다. 다음 표에 이 메뉴의 각 섹션에 대한 세부 정보가 나와 있습니다.

| OMS 포털의 설정 | Azure Portal의 동일한 설정 |
|:---|:---|
| Windows 서버   | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| Linux 서버   | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| Azure Storage     | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| System Center     | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| Office 365        | 구성 세부 정보는 [Office 365 관리 솔루션에 대한 설명서](../insights/solution-office-365.md)를 참조하세요. |
| Windows 원격 분석 | 솔루션에 대한 설정 메뉴입니다. 구성 세부 정보는 [Azure Portal의 Windows Analytics](/windows/deployment/update/windows-analytics-azure-portal)를 참조하세요. |
| ITSM 커넥터    | Log Analytics를 사용하여 ITSM 서비스에 연결하기 위한 지침은 [ITSM 제품/서비스를 IT 서비스 관리 커넥터에 연결](itsmc-connections.md)을 참조하세요. |

### <a name="data"></a>Data
대부분의 데이터 설정은 작업 영역에 대한 **고급 설정** 메뉴에서 관리합니다. 다음 표에 이 메뉴의 각 섹션에 대한 세부 정보가 나와 있습니다.

| OMS 포털의 설정 | Azure Portal의 동일한 설정 |
|:---|:---|
| Windows 이벤트 로그           | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| Windows 성능 카운터 | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| Linux 성능 카운터   | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| IIS 로그                     | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| 사용자 지정 필드                | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| 사용자 지정 로그                  | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| syslog                       | 작업 영역에 대한 **고급 설정** 메뉴입니다. |
| Application Insights         | Log Analytics와 Application Insights가 같은 데이터 엔진을 공유하므로 현재는 이 솔루션을 사용하지 않습니다.  |
| Windows 파일 추적        | Azure Automation의 **변경 내용 추적** 메뉴입니다. 자세한 내용은 [변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적](../../automation/change-tracking.md)을 참조하세요. |
| Windows 레지스트리 추적        | Azure Automation의 **변경 내용 추적** 메뉴입니다. 자세한 내용은 [변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적](../../automation/change-tracking.md)을 참조하세요. |
| Linux 파일 추적          | Azure Automation의 **변경 내용 추적** 메뉴입니다. 자세한 내용은 [변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적](../../automation/change-tracking.md)을 참조하세요. |

### <a name="solutions"></a>솔루션
작업 영역에 대한 **솔루션** 메뉴에서 솔루션을 관리합니다. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>관리 솔루션을 설치 및 제거하려면 어떻게 해야 하나요?
OMS 포털에서는 솔루션 갤러리에서 관리 솔루션을 설치하고 **설정**에서 제거합니다. Azure Portal에서는 Azure Marketplace에서 [관리 솔루션을 설치](../insights/solutions.md#install-a-monitoring-solution)합니다. 설치된 솔루션의 목록에서 [솔루션을 제거](../insights/solutions.md#remove-a-monitoring-solution)합니다.

## <a name="how-do-i-create-and-manage-alerts"></a>경고를 만들고 관리하려면 어떻게 해야 합니까?
Log Analytics 쿼리를 기반으로 하는 경고 규칙은 이제 [통합된 경고 환경](../../azure-monitor/platform/alerts-metric.md)에서 관리됩니다. Azure Portal에서 경고 구성 및 사용에 대한 자세한 내용은 [Log Analytics에서 Azure Alerts로 경고를 확장하는 방법](../../azure-monitor/platform/alerts-extend-tool.md)을 참조하세요.

## <a name="how-do-i-access-my-dashboards"></a>내 대시보드에 액세스하려면 어떻게 해야 하나요?
Log Analytics의 **내 대시보드** 기능이 더 이상 사용되지 않습니다. 이 기능은 뷰 디자이너 부분의 프라이빗 컬렉션을 사용할 수 있으며 기본 제공 Azure 대시보드 기능으로 대체 됩니다. 공유 보기에 대해 [뷰 디자이너](view-designer.md)를 사용하면 Log Analytics에서 계속 데이터 시각화를 할 수 있습니다. 또한 이러한 보기에서 시각화를 고정하거나 [개별 쿼리](../learn/tutorial-logs-dashboards.md)의 경우 Azure 대시보드에 고정할 수 있습니다.

## <a name="how-do-i-check-my-usage"></a>내 사용량은 어떻게 확인할 수 있나요?
이제 작업 영역의 **사용량 및 예상 비용**을 선택하면 Log Analytics의 내 사용량 및 비용을 쉽게 보고 관리할 수 있습니다.

![사용량 및 예상 비용](media/oms-portal-faq/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>클래식 포털을 계속 사용할 수 있나요?
제한된 시간 동안에는 사용자의 작업 영역 이름을 사용한 이 URL을 통해 포털에 계속 액세스할 수 있습니다. https://\<사용자의 작업 영역 이름\>.portal.mms.microsoft.com. 하지만 Azure Portal을 사용하는 것이 좋습니다. 차단 문제에 관해 LAUpgradeFeedback@microsoft.com으로 의견을 보내 주세요.

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 [관리 솔루션을 찾아 설치](../insights/solutions.md)합니다.
- [Azure Portal의 로그 검색](../log-query/portals.md)에 대해 알아봅니다.
