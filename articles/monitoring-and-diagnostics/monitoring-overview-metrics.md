<properties
    pageTitle="Microsoft Azure의 메트릭 개요 | Microsoft Azure"
    description="Microsoft Azure의 메트릭 개요 및 사용"
    authors="kamathashwin"
    manager=""
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>


# <a name="overview-of-metrics-in-microsoft-azure"></a>Microsoft Azure의 메트릭 개요 

이 문서에서는 Microsoft Azure의 메트릭에 대해 설명하고 그 이점과 사용 방법을 소개합니다.  

## <a name="what-are-metrics?"></a>메트릭이 무엇인가요?

Azure 모니터에서는 원격 분석을 사용하여 Azure에서 워크로드의 상태와 성능에 대한 정보를 구할 수 있습니다. Azure 원격 분석 데이터의 가장 중요한 유형은 대부분의 Azure 리소스에서 내보내는 메트릭(성능 카운터라고도 함)입니다. Azure Monitor는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 및 사용하는 몇 가지 방법을 제공합니다.


## <a name="what-can-you-do-with-metrics?"></a>메트릭으로 무엇을 할 수 있나요?

메트릭은 원격 분석의 중요한 출처로, 다음을 수행할 수 있습니다.

- **성능을 추적** 합니다.
- **문제에 대한 알림을 받습니다** .
- 메트릭이 특정 임계값을 초과할 때 리소스 자동 크기 조정 또는 runbook 실행 등과 같은 **자동화된 작업을 구성합니다**.
- **고급 분석** 이나 보고를 수행합니다.
- **규정 준수/감사** 목적으로 리소스의 성능 또는 상태 기록을 **보관**합니다.

##  <a name="metric-characteristics"></a>메트릭 특성
메트릭에는 다음과 같은 특성이 있습니다.

- 모든 메트릭은 **1분 빈도**입니다. 리소스로부터 1분마다 메트릭 값을 받으므로 거의 실시간으로 리소스의 상태를 확인할 수 있습니다.
- 메트릭은 **옵트인이나 추가 진단 설정 없이 바로 사용할 수 있습니다** .
- 각 메트릭에 대해 **30일 동안의 기록** 에 액세스할 수 있습니다. 리소스의 성능이나 상태에서 최근 및 월별 추세를 신속하게 살펴볼 수 있습니다.

다음을 수행할 수 있습니다.

- Azure 포털을 통해 리소스를 선택하고 차트에 표시하여 **모든 메트릭** 간편하게 찾고, 액세스하고, 확인할 수 있습니다. 
- 메트릭이 사용자가 설정한 임계값을 초과하면 알림을 보내거나 자동 조치를 취하는 메트릭 **경고 규칙** 을 구성할 수 있습니다. 자동 크기 조정은 웹 사이트나 계산 리소스에서 들어오는 요청이나 부하에 부합하게 리소스 크기를 조정할 수 있는 특수 자동 작업입니다. 임계값을 초과하는 메트릭을 기준으로 자동 크기 조정 설정 규칙을 확대/축소하도록 구성할 수 있습니다.
- **보관** 합니다. 리소스에 대한 진단 설정을 구성할 때 메트릭을 blob 저장소로 라우팅할 수 있습니다.
- **스트리밍한 다음** metrics to an Event Hub, enabling you to then route them to Azure 스트리밍한 다음 Analytics or custom apps for near-real time analysis. 진단 설정을 사용할 수 있습니다.
- **Route** OMS Log Analytics로 라우팅하여 리소스로부터 받은 메트릭 데이터에 대한 인스턴스 분석, 검색 및 사용자 지정 경고를 잠금 해제합니다.
- **사용합니다** .
- **쿼리** 합니다.

 ![Azure Monitor의 메트릭 라우팅](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>포털을 통해 메트릭 액세스
다음은 Azure 포털을 통해 메트릭 차트를 만드는 간단한 연습입니다.

### <a name="view-metrics-after-creating-a-resource"></a>리소스를 만든 후 메트릭 보기
1. Azure 포털 열기
2. 새 앱 서비스 - 웹 사이트를 만듭니다.
3. 웹 사이트를 만든 후 웹 사이트의 개요 블레이드로 이동합니다.
4. '모니터링' 타일로 새 메트릭을 볼 수 있습니다. 타일을 편집하고 다른 메트릭을 선택할 수 있습니다.

 ![Azure Monitor의 리소스에 대한 메트릭](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>모든 메트릭 한 곳에서 액세스
1. Azure 포털 열기 
2. 새 모니터 탭으로 이동하고 그 아래에서 메트릭을 선택합니다. 
3. 구독, 리소스 그룹 및 리소스의 이름을 드롭다운 목록에서 선택할 수 있습니다. 
4. 이제 사용 가능한 메트릭 목록을 볼 수 있습니다. 
5. 관심 있는 메트릭을 선택하고 표시합니다. 
6. 오른쪽 위 모서리에 있는 핀을 클릭하여 대시보드에 고정할 수 있습니다.

 ![Azure Monitor에서 모든 메트릭을 한 곳에서 액세스](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] 추가적인 진단 설정 없이 VM(Azure Resource Manager 기반) 및 VM 규모 설정에서 호스트 설정 메트릭에 액세스할 수 있습니다. 이러한 호스트 수준 메트릭은 Windows 및 Linux 인스턴스에서 사용할 수 있습니다. 이러한 메트릭을 VM 또는 VMSS에서 Azure Diagnostics를 사용할 때 액세스할 수 있는 게스트 OS 수준 메트릭과 혼동해서는 안 됩니다. Azure Diagnostics 구성에 대한 자세한 내용은 [Microsoft Azure Diagnostics이란?](../azure-diagnostics.md)을 참조하세요.

## <a name="access-metrics-via-rest-api"></a>REST API를 통해 메트릭 액세스
Azure Monitor API를 통해 Azure Metrics에 액세스할 수 있습니다. 메트릭 검색 및 액세스에 사용할 수 있는 2가지 API가 있습니다. 다음을 사용합니다. 

- [Azure Monitor 메트릭 정의 REST API](https://msdn.microsoft.com/library/mt743621.aspx) 를 사용하여 서비스에 사용 가능한 메트릭 목록에 액세스합니다.
- [Azure Monitor 메트릭 REST API](https://msdn.microsoft.com/library/mt743622.aspx) 를 사용하여 실제 메트릭 데이터에 액세스합니다.

>[AZURE.NOTE] 이 문서에서는 Azure 리소스에 대해 [메트릭의 새 API](https://msdn.microsoft.com/library/dn931930.aspx) 를 통한 메트릭을 다룹니다. 새 메트릭 정의 API의 API 버전은 2016-03-01이며 메트릭 API에 대한 버전은 2016-09-01입니다. 레거시 메트릭 정의 및 메트릭은 API 버전 2014-04-01로 액세스할 수 있습니다.

Azure Monitor REST API 사용에 대한 자세한 연습은 [Azure Monitor REST API 연습](monitoring-rest-api-walkthrough.md)을 참조하세요.

## <a name="export-options-for-metrics"></a>메트릭에 대한 내보내기 옵션
모니터 탭의 진단 로그 블레이드로 이동하여 메트릭의 내보내기 옵션을 확인할 수 있습니다. 이 문서의 앞에서 설명한 사용 사례에 대해 Blob 저장소, 이벤트 허브 또는 OMS Log Analytics로 라우팅할 메트릭(및 진단 로드)을 선택할 수 있습니다. 

 ![Azure Monitor에서 메트릭에 대한 내보내기 옵션](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Resource Manager 템플릿, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) 또는 [REST APIs](https://msdn.microsoft.com/library/dn931943.aspx)를 통해 이 항목을 구성할 수 있습니다. 

## <a name="take-action-on-metrics"></a>메트릭에 대한 작업
메트릭 데이터에 대해 알림을 받거나 자동 작업을 수행할 수 있습니다. 경고 규칙 또는 자동 크기 조정 설정을 통해 이를 구성할 수 있습니다.

### <a name="alert-rules"></a>경고 규칙
메트릭에 대해 경고 규칙을 구성할 수 있습니다. 이 경고 규칙은 메트릭이 특정 임계값을 초과했는지 확인하고, 이메일을 통해 사용자에게 알리거나 사용자 지정 스크립트의 실행에 사용할 수 있는 webhook를 실행합니다. Webhook를 사용하여 타사 제품 통합을 구성할 수도 있습니다.

 ![Azure Monitor의 메트릭 및 경고 규칙](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoscale
일부 Azure 리소스에서는 워크로드 처리를 위해 규모를 확대/축소하는 여러 인스턴스를 지원합니다. 자동 크기 조정은 앱 서비스(웹 앱), 가상 컴퓨터 규모 집합(VMSS) 및 기존 Cloud Services에 적용됩니다. 특정 메트릭이 지정한 임계값을 초과할 때 규모를 확대하거나 축소하도록 자동 크기 조정 규칙을 구성할 수 있습니다. 자세한 내용은 [자동 크기 조정 개요](monitoring-overview-autoscale.md)를 참조하세요.

 ![Azure Monitor의 메트릭 및 자동 크기 조정](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>지원되는 서비스 및 메트릭
Azure Monitor는 새로운 메트릭 인프라입니다. Azure 포털의 다음 Azure 서비스와 새 Azure Monitor API 버전을 지원합니다.

- VM(Azure Resource Manager 기반)
- VM 크기 집합
- 배치
- 이벤트 허브 네임스페이스 
- 서비스 버스 네임스페이스(프리미엄 SKU에만 해당)
- SQL(버전 12)
- 탄력적인 SQL 풀
- 웹 사이트
- 웹 서버 팜
- 논리 앱
- IoT Hub
- Redis 캐시
- 네트워킹: 응용 프로그램 게이트웨이
- 검색

모든 지원되는 서비스 및 메트릭의 상세 목록은 [Azure Monitor 메트릭 - 리소스 유형별 지원 메트릭](monitoring-supported-metrics.md)에서 확인할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

이 문서에 있는 링크를 참조하세요. 또한 다음을 학습할 수 있습니다.  

-  [자동 크기 조정에 대한 공통 메트릭](insights-autoscale-common-metrics.md)
-  [경고 규칙을 만드는](insights-alerts-portal.md)







<!--HONumber=Oct16_HO2-->


