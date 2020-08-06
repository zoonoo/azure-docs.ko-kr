---
title: Azure Monitor 배포
description: Azure 구독의 모든 리소스를 모니터링 하는 Azure Monitor의 전체 구현을 수행 하는 데 필요한 여러 단계에 대해 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 34a048c702b62caeecaf21e710a9dcd9156e4aea
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801777"
---
# <a name="deploy-azure-monitor"></a>Azure Monitor 배포
모든 Azure 리소스의 모니터링에 Azure Monitor를 사용 하도록 설정 하는 것은 Azure Monitor 구성 요소를 구성 하 고 Azure 리소스를 구성 하 여 Azure Monitor 수집할 모니터링 데이터를 생성 하는 것입니다. 이 문서에서는 Azure 구독의 모든 리소스를 모니터링 하기 위해 일반적인 구성을 사용 하 Azure Monitor의 전체 구현을 수행 하는 데 필요한 여러 단계를 설명 합니다. 각 단계에 대 한 기본 설명은 자세한 구성 요구 사항에 대 한 다른 설명서에 대 한 링크와 함께 제공 됩니다.

> [!IMPORTANT]
> Azure Monitor 및 해당 구성의 기능은 사용 하도록 설정 된 기능의 비용에 따라 균형을 유지 하는 비즈니스 요구 사항에 따라 달라 집니다. 아래 각 단계는 잠재적 비용이 있는지 확인 하 고 해당 단계를 진행 하기 전에 이러한 비용을 평가 해야 합니다. 전체 가격 정보는 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 을 참조 하세요.

## <a name="configuration-goals"></a>구성 목표
완전 한 Azure Monitor 구현의 목표는 모든 클라우드 리소스 및 응용 프로그램에서 사용 가능한 모든 데이터를 수집 하 고 해당 데이터를 기반으로 가능한 한 많은 Azure Monitor 기능을 사용 하도록 설정 하는 것입니다.

Azure Monitor에 의해 수집 된 데이터는 [Azure Monitor 메트릭](platform/data-platform-metrics.md) 또는 [Azure Monitor 로그](platform/data-platform-logs.md)에 전송 됩니다. 각는 서로 다른 종류의 데이터를 저장 하 고 다른 종류의 분석 및 경고를 사용 하도록 설정 합니다. 서로 다른 경고 유형에 대 한 설명을 보려면 [Azure Monitor 메트릭과 로그 비교](platform/data-platform.md) 를 참조 하 여 [Microsoft Azure에서 경고](platform/alerts-overview.md) 에 대 한 두 가지 및 개요를 비교 하십시오. 

일부 데이터는 다른 기능을 사용 하 여 활용 하기 위해 메트릭과 로그에 모두 보낼 수 있습니다. 이러한 경우 개별적으로 구성 해야 할 수 있습니다. 예를 들어 메트릭 데이터는 메트릭 탐색기 및 메트릭 경고를 지 원하는 Azure 리소스에서 메트릭에 자동으로 전송 됩니다. 로그에 동일한 메트릭 데이터를 전송 하는 각 리소스에 대 한 진단 설정을 만들어야 합니다. 이렇게 하면 Log Analytics를 사용 하 여 다른 로그 데이터를 사용 하 여 성능 추세를 분석할 수 있습니다. 다음 섹션에서는 데이터를 전송 하는 위치를 식별 하 고 가능한 모든 위치에 데이터를 전송 하는 데 필요한 각 단계를 포함 합니다.

Azure 외부에서 리소스를 모니터링 하 고 Azure Monitor 외부로 데이터를 전송 하는 등의 추가 요구 사항이 있을 수 있습니다. 이 문서에 설명 된 기능의 추가 구성으로 이러한 요구 사항을 달성할 수 있습니다. 추가 구성 옵션을 보려면 각 단계의 설명서에 대 한 링크를 따르세요.

## <a name="collect-data-from-azure-resources"></a>Azure 리소스에서 데이터 수집

> [!NOTE]
> Azure Monitor를 사용 하 여 가상 컴퓨터를 모니터링 하는 방법에 대 한 전체 지침은 [Azure Monitor으로 Azure 리소스 모니터링](insights/monitor-azure-resource.md) 을 참조 하세요.

Azure 리소스의 일부 모니터링은 구성 없이 자동으로 사용할 수 있으며, 추가 모니터링 데이터를 수집 하는 구성 단계를 수행 해야 합니다. 다음 표에서는 Azure 리소스에서 사용 가능한 모든 데이터를 수집 하는 데 필요한 구성 단계를 보여 줍니다. 여기에는 Azure Monitor 메트릭 및 Azure Monitor 로그로 전송 되는 단계 데이터가 포함 됩니다. 아래 섹션에서는 각 단계에 대해 자세히 설명 합니다.

[![Azure 리소스 모니터링 ](media/deploy/deploy-azure-resources.png) 배포](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>구성 없음
Azure 구독을 만들 때 구성 없이 Azure Monitor의 다음 기능이 사용 하도록 설정 됩니다. 이 모니터링과 관련 된 비용은 없습니다.

[Azure Active Directory logs](../active-directory/reports-monitoring/overview-reports.md) -로그인 활동에 대 한 테 넌 트 수준 기록과 Azure Active Directory의 변경 내용에 대 한 감사 내역을 제공 합니다. Azure Active Directory 로그 및 Azure Portal에서 확인 하는 방법에 대 한 자세한 내용은 Azure Active Directory 포털의 Azure Active Directory 포털 및 [로그인 활동 보고서](../active-directory/reports-monitoring/concept-sign-ins.md) [에서 감사 작업 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 를 참조 하세요.

[활동 로그](platform/platform-logs-overview.md) -관리 그룹 및 Azure에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 합니다. 새 Azure 리소스를 만들거나 리소스를 수정 하거나 중요 한 작업을 수행할 때 이벤트가 활동 로그에 자동으로 기록 됩니다. Azure Portal에서 이벤트를 확인 하 고 특정 이벤트가 생성 될 때 활동 로그 경고를 만들 수 있습니다. 활동 로그 및 Azure Portal에서이를 보는 방법에 대 한 자세한 내용은 [Azure 활동 로그](platform/activity-log.md) 를 참조 하세요.

[플랫폼 메트릭](platform/metrics-supported.md) -Azure 서비스에서 [Azure Monitor 메트릭으로](platform/data-platform-metrics.md)자동으로 수집 됩니다. 이 데이터는 다양 한 서비스에 대 한 Azure Portal의 **개요** 페이지에 표시 되는 경우가 많습니다. Azure Portal에서 플랫폼 메트릭을 분석 하는 방법에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](platform/metrics-getting-started.md) 을 참조 하세요. 


### <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
Azure 리소스의 로그와 같은 데이터를 수집 하 고, Azure virtual machines의 게스트 운영 체제에서 데이터를 수집 하 고, 대부분의 Azure Monitor 정보를 수집 하는 데 필요한 [Azure Monitor 로그](platform/data-platform-logs.md)를 사용 하려면 하나 이상의 Log Analytics 작업 영역이 필요 합니다. Azure 센티널 및 Azure Security Center와 같은 다른 서비스는 Log Analytics 작업 영역을 사용 하 고 Azure Monitor 하는 데 사용 하는 것과 동일한 서비스를 공유할 수 있습니다. 단일 작업 영역에서 시작 하 여이 모니터링을 지원할 수 있지만 여러 작업 영역을 사용 하는 경우에 대 한 지침은 [Azure Monitor 로그 배포 디자인](platform/design-logs-deployment.md) 을 참조 하세요.

Log Analytics 작업 영역을 만드는 데 드는 비용은 없지만 데이터를 수집 하도록 구성한 후에는 잠재적으로 요금이 부과 될 수 있습니다. 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](platform/manage-cost-storage.md)를 참조하세요.  

[Azure Portal에서 Log Analytics 작업 영역 만들기](learn/quick-create-workspace.md) 를 참조 하 여 초기 Log Analytics 작업 영역을 만듭니다. 액세스를 구성 하려면 [Azure Monitor의 로그 데이터 및 작업 영역에 대 한 액세스 관리](platform/manage-access.md) 를 참조 하세요. 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>테 넌 트 및 구독 로그를 수집 하는 진단 설정 만들기
테 넌 트에 대 한 [Azure Active Directory 로그](../active-directory/reports-monitoring/overview-reports.md) 와 구독에 대 한 [활동 로그](platform/platform-logs-overview.md) 를 자동으로 수집 하는 동안에는 Log Analytics 작업 영역으로 보내 Log Analytics의 로그 쿼리를 사용 하 여 다른 로그 데이터를 사용 하 여 이러한 이벤트를 분석할 수 있습니다. 이를 통해 Azure Active Directory 로그에 대해 경고 하 고 활동 로그 경고 보다 더 복잡 한 논리를 제공 하는 유일한 방법인 로그 쿼리 경고를 만들 수 있습니다.

활동 로그를 작업 영역에 전송 하는 데 드는 비용이 없지만 Azure Active Directory 로그에 대 한 데이터 수집 및 보존 요금은 있습니다. 

[AZURE AD 로그를 Azure Monitor 로그와 통합](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 하 고 [다른 대상에 플랫폼 로그 및 메트릭을 전송 하는 진단 설정 만들기](platform/diagnostic-settings.md) 를 참조 하 여 테 넌 트 및 구독에 대 한 진단 설정을 만들어 Log Analytics 작업 영역에 로그 항목을 보냅니다. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>리소스 로그 및 플랫폼 메트릭을 수집 하는 진단 설정 만들기
Azure의 리소스는 리소스 내에서 수행 된 작업에 대 한 세부 정보를 제공 하는 [리소스 로그](platform/platform-logs-overview.md) 를 자동으로 생성 합니다. 그러나 플랫폼 메트릭과 달리 리소스 로그를 수집 하도록 구성 해야 합니다. Log Analytics 작업 영역에 전송 하 여 Azure Monitor 로그에 사용 되는 다른 데이터와 결합 하는 진단 설정을 만듭니다. 동일한 진단 설정을 사용 하 여 대부분의 리소스에 대 한 플랫폼 메트릭을 동일한 작업 영역에 보낼 수도 있습니다. 그러면 수집 된 다른 데이터와 함께 로그 쿼리를 사용 하 여 메트릭 데이터를 분석할 수 있습니다.

이 컬렉션에 대 한 비용이 발생 하므로 상당한 수의 리소스에서 구현 하기 전에 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 을 참조 하세요. 또한 로그 수집 비용을 최적화 하는 방법에 대 한 자세한 내용은 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](platform/manage-cost-storage.md) 를 참조 하세요.

Azure 리소스에 대 한 진단 설정을 만들려면 [azure에서 리소스 로그 및 메트릭을 수집 하는 진단 설정 만들기](platform/diagnostic-settings.md#create-in-azure-portal) 를 참조 하세요. 각 Azure 리소스에 대해 진단 설정을 만들어야 하므로 azure [정책을](../governance/policy/overview.md) 사용 하 여 azure 리소스를 만들 때마다 자동으로 생성 되는 설정을 사용 하는 방법에 대 한 자세한 내용은 [규모에 Azure Monitor 배포](deploy-scale.md) 를 참조 하세요.

### <a name="enable-insights-and-solutions"></a>통찰력 및 솔루션 사용
정보 및 솔루션은 특정 서비스 또는 솔루션에 대 한 특수 한 모니터링을 제공 합니다. Insights는 통합 문서와 같은 Azure Monitor의 최신 기능을 사용 하므로 서비스에 사용할 수 있는 경우 통찰력을 사용 해야 합니다. 모든 Azure 구독에서 자동으로 사용할 수 있지만, 전체 기능을 사용 하려면 몇 가지 구성이 필요할 수 있습니다. 일반적으로 이전에 구성 하 고 추가 데이터를 수집할 수 있는 플랫폼 메트릭 및 리소스 로그를 사용 합니다.

각 구독에 솔루션을 추가 하 고 Azure Monitor 로그의 데이터로만 작업 하 고 추가 로그 데이터를 수집할 수 있어야 합니다.

솔루션 또는 통찰력에 대 한 비용은 없지만 수집 하는 모든 데이터에 대해 요금이 부과 될 수 있습니다.

Azure Monitor에서 사용할 수 있는 정보 및 솔루션 목록은 [Azure Monitor에서 모니터링 되는 내용](monitor-reference.md) 을 참조 하세요. 고유한 구성 또는 가격 책정 정보는 각각에 대 한 설명서를 참조 하세요. 

## <a name="collect-data-from-virtual-machines"></a>가상 머신에서 데이터 수집

> [!NOTE]
> Azure Monitor를 사용 하 여 가상 컴퓨터를 모니터링 하는 방법에 대 한 전체 가이드는 [Azure Monitor으로 Azure virtual Machines 모니터링](insights/monitor-vm-azure.md) 을 참조 하세요. 

가상 머신은 다른 Azure 리소스와 유사한 데이터를 생성 하지만, 게스트 운영 체제에서 데이터를 수집 하려면 에이전트가 필요 합니다. Azure Monitor에서 사용 하는 에이전트의 비교는 [Azure Monitor 에이전트 개요](platform/agents-overview.md) 를 참조 하세요. 

[VM용 Azure Monitor](insights/vminsights-overview.md) 는 Log Analytics 에이전트 및 종속성 에이전트를 사용 하 여 가상 컴퓨터의 게스트 운영 체제에서 데이터를 수집 하므로 이러한 정보를 구현 하는 과정의 일부로 이러한 에이전트를 배포할 수 있습니다. 이렇게 하면 Azure Security Center와 같이 사용 하는 다른 서비스에 대 한 Log Analytics 에이전트를 사용할 수 있습니다.


[![AZURE VM ](media/deploy/deploy-azure-vm.png) 배포](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>VM용 Azure Monitor에 대 한 작업 영역 구성
VM용 Azure Monitor에는 일반적으로 다른 Azure 리소스에서 데이터를 수집 하기 위해 만든 것과 동일한 Log Analytics 작업 영역이 필요 합니다. 가상 컴퓨터를 사용 하도록 설정 하기 전에 작업 영역에 VM용 Azure Monitor 하는 데 필요한 솔루션을 추가 해야 합니다.

VM용 Azure Monitor의 Log Analytics 작업 영역을 구성 하는 방법에 대 한 자세한 내용은 [VM용 Azure Monitor Log Analytics 작업 영역 구성](insights/vminsights-configure-workspace.md) 을 참조 하세요.

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>각 가상 컴퓨터에서 VM용 Azure Monitor 사용
작업 영역을 구성한 후에는 Log Analytics 에이전트와 종속성 에이전트를 설치 하 여 각 가상 컴퓨터를 사용 하도록 설정할 수 있습니다. 각 가상 컴퓨터를 만들 때 자동으로 구성할 수 있도록 하는 Azure Policy 포함 하 여 이러한 에이전트를 설치 하는 방법에는 여러 가지가 있습니다. VM용 Azure Monitor에서 수집 된 성능 데이터 및 프로세스 세부 정보는 Azure Monitor 로그에 저장 됩니다.

가상 컴퓨터에 에이전트를 배포 하 고 모니터링에 사용 하도록 설정 하는 옵션은 [사용 VM용 Azure Monitor 개요 사용](insights/vminsights-enable-overview.md) 을 참조 하세요.

### <a name="configure-workspace-to-collect-events"></a>이벤트를 수집 하도록 작업 영역 구성
VM용 Azure Monitor는 각 가상 컴퓨터의 게스트 운영 체제에서 성능 데이터와 프로세스의 세부 정보 및 종속성을 수집 합니다. Log Analytics 에이전트는 Windows 및 Linux의 syslog에서 이벤트 로그를 포함 하 여 게스트에서 로그를 수집할 수도 있습니다. 연결 된 Log Analytics 작업 영역에서 이러한 로그에 대 한 구성을 검색 합니다. 작업 영역을 한 번만 구성 하면 에이전트가 연결 될 때마다 구성 변경 내용이 다운로드 됩니다. 

에이전트 가상 컴퓨터에서 추가 데이터를 수집 하도록 Log Analytics 작업 영역을 구성 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 에이전트 데이터 원본](platform/agent-data-sources.md) 을 참조 하세요.

> [!NOTE]
> 성능 카운터를 수집 하도록 작업 영역을 구성할 수도 있지만이는 VM용 Azure Monitor에 의해 수집 된 성능 데이터와 중복 될 가능성이 높습니다. 작업 영역에서 수집 된 성능 데이터는 *성능 테이블에 저장 되 고 VM용 Azure Monitor* 에서 수집 되는 성능 데이터는 *InsightsMetrics* 테이블에 저장 됩니다. VM용 Azure Monitor에서 아직 수집 하지 않은 카운터가 필요한 경우에만 작업 영역에서 성능 수집을 구성 합니다.

### <a name="diagnostic-extension-and-telegraf-agent"></a>진단 확장 및 Telegraf 에이전트
VM용 Azure Monitor는 Log Analytics 작업 영역으로 성능 데이터를 전송 하는 Log Analytics 에이전트를 사용 하지만 Azure Monitor 메트릭을 사용 하지 않습니다. 이 데이터를 메트릭에 전송 하면 메트릭 탐색기를 사용 하 여 분석 하 고 메트릭 경고와 함께 사용할 수 있습니다. 이렇게 하려면 Windows의 진단 확장과 Linux의 Telegraf 에이전트가 필요 합니다.

이러한 에이전트 설치 및 구성에 대 한 자세한 내용은 [Windows Azure 진단 확장 설치 및 구성 (WAD)](platform/diagnostics-extension-windows-install.md) 및 [InfluxData Telegraf 에이전트를 사용 하 여 Linux VM에 대 한 사용자 지정 메트릭 수집](platform/collect-custom-metrics-linux-telegraf.md) 을 참조 하세요.


## <a name="monitor-applications"></a>애플리케이션 모니터링
Azure Monitor는 모니터링 하려는 각 응용 프로그램에 대해 구성 해야 하는 [Application Insights](app/app-insights-overview.md)를 사용 하 여 사용자 지정 응용 프로그램을 모니터링 합니다. 구성 프로세스는 모니터링 되는 응용 프로그램의 유형과 수행 하려는 모니터링 유형에 따라 달라 집니다. Application Insights에 의해 수집 된 데이터는 기능에 따라 Azure Monitor 메트릭, Azure Monitor 로그 및 Azure blob 저장소에 저장 됩니다. 성능 데이터는 추가 구성이 필요 없는 Azure Monitor 메트릭과 Azure Monitor 로그에 저장 됩니다.

### <a name="create-an-application-resource"></a>응용 프로그램 리소스 만들기
모니터링할 각 응용 프로그램에 대해 Application Insights에서 리소스를 만들어야 합니다. Application Insights에 의해 수집 된 로그 데이터는 Azure Monitor 로그에 저장 되지만 [Azure Monitor 로그의 데이터를 구조화 하는 방법](platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)에 설명 된 대로 Log Analytics 작업 영역과는 다릅니다. 현재 미리 보기 상태는 응용 프로그램 데이터를 다른 데이터와 함께 Log Analytics 작업 영역에 직접 저장 하는 기능입니다. 이렇게 하면 구성이 간소화 되 고 응용 프로그램에서 Log Analytics 작업 영역의 모든 기능을 활용할 수 있습니다.

 응용 프로그램을 만들 때 클래식 또는 작업 영역 기반 (미리 보기)을 사용할지 여부를 선택 해야 합니다. 클래식 응용 프로그램을 만들려면 [Application Insights 리소스 만들기](app/create-new-resource.md) 를 참조 하세요. 작업 영역 기반 응용 프로그램을 만들려면 [작업 영역 기반 Application Insights 리소스 (미리 보기)](app/create-workspace-resource.md) 를 참조 하세요.

### <a name="configure-codeless-or-code-based-monitoring"></a>코드 없는 또는 코드 기반 모니터링 구성
응용 프로그램에 대 한 모니터링을 사용 하도록 설정 하려면 코드 없는 또는 코드 기반 모니터링을 사용할지 여부를 결정 해야 합니다. 구성 프로세스는이 결정 및 모니터링할 응용 프로그램 유형에 따라 달라 집니다.

**코드 없는 모니터링** 은 구현 하기 쉽고 코드 개발 후에 구성할 수 있습니다. 코드를 업데이트할 필요가 없습니다. 응용 프로그램에 따라 모니터링을 사용 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [Azure Web Apps에서 호스트 되는 응용 프로그램](app/azure-web-apps.md)
- [Java 애플리케이션](app/java-in-process-agent.md)
- [Azure VM 또는 Azure Virtual Machine Scale Set의 IIS에 호스트된 ASP.NET 애플리케이션](app/azure-vm-vmss-apps.md)
- [IIS 온-프레미스 VM에 호스트된 ASP.NET 애플리케이션](app/monitor-performance-live-website-now.md)


**코드 기반 모니터링** 은 더 사용자 지정이 가능 하며 추가 원격 분석을 수집 하지만 Application Insights SDK NuGet 패키지에서 코드에 종속성을 추가 해야 합니다. 응용 프로그램에 따라 모니터링을 사용 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [ASP.NET 애플리케이션](app/asp-net.md)
- [ASP.NET Core 애플리케이션](app/asp-net-core.md)
- [.NET 콘솔 애플리케이션](app/console.md)
- [Java](app/java-get-started.md)
- [Node.JS](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [기타 플랫폼](app/platforms.md)

### <a name="configure-availability-testing"></a>가용성 테스트 구성
Application Insights의 가용성 테스트는 전 세계의 지점에서 일정 한 간격으로 응용 프로그램의 가용성과 응답성을 모니터링 하는 되풀이 테스트입니다. 간단한 ping 테스트를 무료로 만들거나 웹 요청 시퀀스를 만들어 관련 비용이 있는 사용자 트랜잭션을 시뮬레이션할 수 있습니다. 

다양 한 종류의 테스트를 요약 하 고 만드는 방법에 대 한 자세한 내용은 [웹 사이트의 가용성 모니터링](app/monitor-web-app-availability.md) 을 참조 하세요.

### <a name="configure-profiler"></a>프로파일러 구성
Application Insights의 프로파일러는 .NET 응용 프로그램에 대 한 성능 추적을 제공 합니다. 특정 웹 요청을 처리할 때 가장 긴 시간을 사용 하는 "핫" 코드 경로를 식별 하는 데 도움이 됩니다. 프로파일러를 구성 하는 프로세스는 응용 프로그램의 유형에 따라 달라 집니다. 

프로파일러 구성에 대 한 자세한 내용은 [Application Insights를 사용 하 여 Azure에서 프로덕션 응용 프로그램 프로 파일링](app/profiler-overview.md) 을 참조 하세요.

### <a name="configure-snapshot-debugger"></a>스냅숏 디버거 구성
Application Insights 스냅숏 디버거는 .NET 응용 프로그램에서 예외 원격 분석을 모니터링 하 고, 프로덕션에서 문제를 진단 하는 데 필요한 정보를 포함 하도록 최상위 예외에 대 한 스냅숏을 수집 합니다. 스냅숏 디버거를 구성 하는 프로세스는 응용 프로그램의 유형에 따라 달라 집니다. 

스냅숏 디버거 구성에 대 한 자세한 내용은 [.net 앱의 예외에 대 한 스냅숏 디버그](app/snapshot-debugger.md) 를 참조 하세요.


## <a name="visualize-data"></a>데이터 시각화
정보 및 솔루션에는 자신의 데이터를 분석 하기 위한 자체 통합 문서와 보기가 포함 되어 있습니다. 이 외에도 Azure Monitor 데이터와 대시보드의 통합 문서를 비롯 하 여 Azure의 다른 서비스 데이터와 Azure Monitor 데이터를 결합 하는 데 사용할 수 있는 고유한 [시각화](visualizations.md) 를 만들 수 있습니다.


### <a name="create-workbooks"></a>통합 문서 만들기
Azure Monitor [통합 문서](platform/workbooks-overview.md) 를 사용 하면 Azure Portal에서 풍부한 시각적 보고서를 만들 수 있습니다. Azure Monitor 메트릭과 Azure Monitor 로그에서 다양 한 데이터 집합을 결합 하 여 통합 된 대화형 환경을 만들 수 있습니다. Azure Monitor 메뉴의 **통합 문서** 탭에서 통합 문서의 갤러리에 액세스할 수 있습니다. 

사용자 지정 통합 문서를 만드는 방법에 대 한 자세한 내용은 [Azure Monitor 통합 문서](platform/workbooks-overview.md) 를 참조 하세요.

### <a name="create-dashboards"></a>대시보드 만들기
Azure [대시보드](../azure-portal/azure-portal-dashboards.md) 는 azure에 대 한 기본 전문가 용 기술 이며 다른 서비스의 데이터와 Azure Monitor 데이터를 결합 하 여 Azure 인프라에 대 한 단일 창을 제공 합니다. Azure Monitor 로그의 데이터를 포함 하는 대시보드를 만드는 방법에 대 한 자세한 내용은 [Log Analytics 데이터의 대시보드 만들기 및 공유](learn/tutorial-logs-dashboards.md) 를 참조 하세요. 

Application Insights 데이터를 포함 하는 대시보드를 만드는 방법에 대 한 자세한 내용은 [Azure 애플리케이션 Insights를 사용 하 여 사용자 지정 KPI 대시보드 만들기](learn/tutorial-app-dashboards.md) 를 참조 하세요. 

## <a name="alerts"></a>경고
Azure Monitor 경고는 모니터링 데이터에서 식별 된 중요 한 데이터 나 패턴을 사전에 알려 줍니다. 일부 정보는 구성 없이 경고를 생성 합니다. 다른 시나리오의 경우 분석할 데이터를 포함 하는 [경고 규칙](platform/alerts-overview.md) 및 경고를 생성 하는 시기에 대 한 조건 및 경고가 생성 될 때 수행할 작업을 정의 하는 작업 그룹을 만들어야 합니다. 


### <a name="create-action-groups"></a>작업 그룹 만들기
[작업 그룹](platform/action-groups.md) 은 경고가 트리거될 때 수행할 동작을 결정 하기 위해 경고 규칙에서 사용 하는 알림 기본 설정의 컬렉션입니다. 작업의 예로는 메일 또는 텍스트 전송, webhook 호출 또는 ITSM 도구에 데이터 보내기 등이 있습니다. 각 경고 규칙에는 하나 이상의 작업 그룹이 필요 하며 여러 경고 규칙에서 단일 작업 그룹을 사용할 수 있습니다.

작업 그룹 만들기에 대 한 자세한 내용 및 포함할 수 있는 다양 한 작업에 대 한 설명은 [Azure Portal에서 작업 그룹 만들기 및 관리](platform/action-groups.md) 를 참조 하세요.


### <a name="create-alert-rules"></a>경고 규칙 만들기
사용 되는 데이터 형식으로 정의 된 여러 유형의 경고 규칙이 있습니다. 각각에는 서로 다른 기능과 비용이 있습니다. 따라야 하는 기본 전략은 필요한 논리를 제공 하는 가장 저렴 한 비용의 경고 규칙 유형을 사용 하는 것입니다.

- [활동 로그 규칙](platform/activity-log-alerts.md). 지정 된 조건과 일치 하는 새 활동 로그 이벤트에 대 한 응답으로 경고를 만듭니다. 이러한 경고에 대 한 비용은 없으므로 먼저 선택 해야 합니다. 활동 로그 경고 만들기에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 활동 로그 경고 만들기, 보기 및 관리를](platform/alerts-activity-log.md) 참조 하세요.
- [메트릭 경고 규칙](platform/alerts-metric-overview.md)입니다. 임계값을 초과 하는 하나 이상의 메트릭 값에 대 한 응답으로 경고를 만듭니다. 메트릭 경고는 상태 저장을 의미 합니다. 즉, 값이 임계값 아래로 떨어지면 경고가 자동으로 닫히고 상태가 변경 될 때 알림만 전송 됩니다. 메트릭 경고에 대 한 비용이 발생 하지만 로그 경고 보다 훨씬 적습니다. 메트릭 경고 만들기에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리를](platform/alerts-metric.md) 참조 하세요.
- [로그 경고 규칙](platform/alerts-unified-log.md)입니다. 일정 쿼리 결과가 지정 된 조건과 일치 하는 경우 경고를 만듭니다. 이러한 규칙은 가장 부담이 큰 경고 규칙 이지만 가장 복잡 한 조건을 허용 합니다. 로그 쿼리 경고 만들기에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리를](platform/alerts-log.md) 참조 하세요.
- [응용 프로그램 경고](app/monitor-web-app-availability.md) 를 사용 하면 웹 응용 프로그램의 자동 관리 성능 및 가용성 테스트를 수행할 수 있습니다. 간단한 ping 테스트를 무료로 수행할 수 있지만 더 복잡 한 테스트를 위한 비용이 듭니다. 다른 테스트에 대 한 설명과이를 만드는 방법에 대 한 자세한 내용은 [웹 사이트의 가용성 모니터링](app/monitor-web-app-availability.md) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Policy를 사용 하 여 규모에 Azure Monitor 배포를](deploy-scale.md)참조 하세요.