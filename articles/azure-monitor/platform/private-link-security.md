---
title: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결
description: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 42419247de537f9a166c3cdca2fd5a832ade6a5f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461433"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다. 많은 서비스의 경우 리소스당 엔드포인트를 설정하기만 하면 됩니다. 그러나 Azure Monitor는 동시에 작동하여 워크로드를 모니터링하는 상호 연결된 서로 다른 서비스의 모음입니다. 결과적으로 모니터링 네트워크의 경계를 정의하고 가상 네트워크에 연결할 수 있는 AMPLS(Azure Monitor Private Link Scope)라는 리소스를 구축했습니다. 이 문서에서는 Azure Monitor Private Link 범위를 사용하는 경우와 설정하는 방법에 대해 설명합니다.

## <a name="advantages"></a>장점

Private Link를 사용하면 다음을 수행할 수 있습니다.

- 공용 네트워크 액세스를 개방하지 않고 Azure Monitor에 비공개로 연결
- 권한 있는 개인 네트워크를 통해서만 모니터링 데이터에 액세스할 수 있는지 확인
- 프라이빗 엔드포인트를 통해 연결되는 특정 Azure Monitor 리소스를 정의하여 개인 네트워크에서 데이터 반출 방지
- ExpressRoute 및 Private Link를 사용하여 온-프레미스 개인 네트워크를 Azure Monitor에 안전하게 연결
- Microsoft Azure 백본 네트워크 내에서 모든 트래픽 유지

자세한 내용은 [Private Link의 주요 이점](../../private-link/private-link-overview.md#key-benefits)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

Azure Monitor Private Link 범위는 하나 이상의 프라이빗 엔드포인트(이에 따라 포함된 가상 네트워크)를 하나 이상의 Azure Monitor 리소스에 연결하는 그룹화 리소스입니다. 이 리소스에는 Log Analytics 작업 영역과 Application Insights 구성 요소가 포함됩니다.

![리소스 토폴로지 다이어그램](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> 단일 Azure Monitor 리소스는 여러 AMPLS에 속할 수 있지만, 단일 VNet을 둘 이상의 AMPLS에 연결할 수는 없습니다. 

## <a name="planning-based-on-your-network"></a>네트워크 기반 계획 수립

AMPLS 리소스를 설정하기 전에 네트워크 격리 요구 사항을 고려합니다. 퍼블릭 인터넷에 대한 가상 네트워크 액세스 및 각 Azure Monitor 리소스(즉, Application Insights 구성 요소 및 Log Analytics 작업 영역)의 액세스 제한을 평가합니다.

> [!NOTE]
> 허브 및 스포크 네트워크 또는 피어 링 네트워크의 다른 토폴로지는 각 VNet 및 모든 VNet에 개인 링크를 설정 하는 대신 허브 (주) VNet과 관련 Azure Monitor 리소스 간에 개인 링크를 설정할 수 있습니다. 특히 이러한 네트워크에서 사용 하는 Azure Monitor 리소스를 공유 하는 경우에 적합 합니다. 그러나 각 VNet에서 별도의 모니터링 리소스 집합에 액세스 하도록 허용 하려면 각 네트워크에 대 한 전용 AMPLS 개인 링크를 만듭니다.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Private Link에 연결할 가상 네트워크 평가

먼저 인터넷에 대한 액세스가 제한된 VNet(가상 네트워크)을 평가합니다. 무료 인터넷을 사용하는 VNet에는 Azure Monitor 리소스에 액세스하는 데 Private Link가 필요하지 않을 수 있습니다. VNet에서 연결하는 모니터링 리소스는 들어오는 트래픽을 제한하고 로그 수집 또는 쿼리를 위해 Private Link 연결을 요구할 수 있습니다. 이러한 경우 퍼블릭 인터넷에 액세스할 수 있는 VNet조차도 Private Link 및 AMPLS를 통해 이러한 리소스에 연결해야 합니다.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Private Link를 포함할 Azure Monitor 리소스 평가

각 Azure Monitor 리소스를 검토합니다.

- 리소스에서 특정 VNet에만 있는 리소스로부터 로그를 수집하도록 허용해야 하나요?
- 특정 VNET에 있는 클라이언트만 리소스를 쿼리해야 하나요?

이러한 질문에 대한 대답이 예인 경우 [Log Analytics 작업 영역 구성](#configure-log-analytics) 및 [Application Insights 구성 요소 구성](#configure-application-insights)에서 설명한 대로 제한을 설정하고 이러한 리소스를 단일 또는 여러 AMPLS에 연결합니다. 이러한 모니터링 리소스에 액세스해야 하는 가상 네트워크에는 관련 AMPLS에 연결되는 프라이빗 엔드포인트가 있어야 합니다.
주의 - 동일한 작업 영역 또는 애플리케이션을 여러 AMPLS에 연결하여 다른 네트워크에서 액세스할 수 있도록 허용합니다.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>네트워크 접근성별로 모니터링 리소스 그룹화

각 VNet에서는 하나의 AMPLS 리소스에만 연결할 수 있으므로 동일한 네트워크에서 액세스할 수 있어야 하는 모니터링 리소스를 모두 그룹화해야 합니다. 이 그룹화를 관리하는 가장 간단한 방법은 VNet당 하나의 AMPLS를 만들고 해당 네트워크에 연결할 리소스를 선택하는 것입니다. 그러나 리소스를 줄이고 관리 효율성을 향상시키려면 네트워크에서 AMPLS를 다시 사용하는 것이 좋습니다.

예를 들어 VNet1 및 VNet2 내부 가상 네트워크에서 Workspace1 및 Workspace2 작업 영역 및 Application Insights 3 Application Insights 구성 요소에 연결해야 하는 경우 세 리소스를 모두 동일한 AMPLS에 연결합니다. VNet3에서 Workspace1에만 액세스해야 하는 경우 다음 다이어그램에서와 같이 다른 AMPLS 리소스를 만들고, Workspace1을 여기에 연결하고, VNet3을 연결합니다.

![AMPLS A 토폴로지 다이어그램](./media/private-link-security/ampls-topology-a-1.png)

![AMPLS B 토폴로지 다이어그램](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>제한 고려

개인 링크 설정을 계획할 때 고려해 야 할 몇 가지 제한이 있습니다.

* VNet은 1 개의 AMPLS 개체에만 연결할 수 있습니다. 즉, AMPLS 개체는 VNet이 액세스할 수 있어야 하는 모든 Azure Monitor 리소스에 대 한 액세스를 제공 해야 합니다.
* Azure Monitor 리소스 (작업 영역 또는 Application Insights 구성 요소)는 AMPLSs 5 개에 연결할 수 있습니다.
* AMPLS 개체는 50 Azure Monitor 리소스에 연결할 수 있습니다.
* AMPLS 개체는 최대 10 개의 개인 끝점에 연결할 수 있습니다.

아래 토폴로지에서:
* 각 VNet은 1 개의 AMPLS 개체에 연결 하므로 다른 AMPLSs에 연결할 수 없습니다.
* AMPLS B는 2 Vnet: 2/10의 가능한 개인 끝점 연결을 사용 하 여 연결 합니다.
* AMPLS A는 2 개의 작업 영역 및 1 개의 응용 프로그램 정보 구성 요소에 연결 합니다. 3/50의 가능한 Azure Monitor 리소스를 사용 합니다.
* 작업 영역 2는 AMPLS A 및 AMPLS B: 2/5를 사용 하 여 가능한 AMPLS 연결을 연결 합니다.

![AMPLS 제한 다이어그램](./media/private-link-security/ampls-limits.png)

## <a name="example-connection"></a>연결 예

먼저 Azure Monitor Private Link 범위 리소스를 만듭니다.

1. Azure Portal에서 **리소스 만들기**로 이동하여 **Azure Monitor Private Link 범위**를 검색합니다.

   ![Azure Monitor 개인 링크 범위 찾기](./media/private-link-security/ampls-find-1c.png)

2. **만들기**를 클릭합니다.
3. 구독 및 리소스 그룹을 선택합니다.
4. AMPLS 이름을 지정합니다. 누군가가 실수로 네트워크 보안 경계를 위반하지 않도록 범위를 사용하는 용도 및 보안 경계를 명확히 하는 이름을 사용하는 것이 가장 좋습니다. 예를 들어 "AppServerProdTelem"입니다.
5. **검토 + 만들기**를 클릭합니다. 

   ![Azure Monitor 개인 링크 범위 만들기](./media/private-link-security/ampls-create-1d.png)

6. 유효성 검사를 통과하도록 한 다음, **만들기**를 클릭합니다.

## <a name="connect-azure-monitor-resources"></a>Azure Monitor 리소스 연결

먼저 AMPLS를 프라이빗 엔드포인트에 연결한 다음, Azure Monitor 리소스에 연결하거나 그 반대로 연결할 수 있지만, Azure Monitor 리소스를 사용하여 시작하면 연결 프로세스가 더 빠르게 진행됩니다. Azure Monitor Log Analytics 작업 영역 및 Application Insights 구성 요소를 AMPLS에 연결하는 방법은 다음과 같습니다.

1. Azure Monitor Private Link 범위의 왼쪽 메뉴에서 **Azure Monitor 리소스**를 클릭합니다. **추가** 단추를 클릭합니다.
2. 작업 영역 또는 구성 요소를 추가합니다. **추가** 단추를 클릭하면 Azure Monitor 리소스를 선택할 수 있는 대화 상자가 표시됩니다. 구독 및 리소스 그룹을 찾아보거나 이름을 입력하여 이를 필터링할 수 있습니다. 작업 영역 또는 구성 요소를 선택하고, **적용**을 클릭하여 이를 범위에 추가합니다.

    ![범위 선택 UX의 스크린샷](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>프라이빗 엔드포인트에 연결

이제 AMPLS에 연결된 리소스가 있으므로 네트워크를 연결 하는 프라이빗 엔드포인트를 만듭니다. 다음 예에서와 같이 이 작업은 [Azure Portal Private Link 센터](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) 또는 Azure Monitor Private Link 범위 내에서 수행할 수 있습니다.

1. 범위 리소스의 왼쪽 리소스 메뉴에서 **프라이빗 엔드포인트 연결**을 클릭합니다. **프라이빗 엔드포인트**를 클릭하여 엔드포인트 만들기 프로세스를 시작합니다. 또한 Private Link 센터에서 시작된 연결을 선택하고 **승인**을 클릭하여 이를 승인할 수도 있습니다.

    ![프라이빗 엔드포인트 연결 UX의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 구독, 리소스 그룹, 엔드포인트 이름 및 거주하는 지역을 선택합니다. 지역은 연결할 가상 네트워크와 동일한 지역이어야 합니다.

3. **다음: 리소스**를 선택합니다. 

4. [리소스] 화면에서

   a. Azure Monitor 프라이빗 범위 리소스가 포함된 **구독**을 선택합니다. 

   b. **리소스 종류**에 대해 **Microsoft.insights/privateLinkScopes**를 선택합니다. 

   다. **리소스** 드롭다운에서 이전에 만든 Private Link 범위를 선택합니다. 

   d. **다음: 구성 >** 을 클릭합니다.
      ![프라이빗 엔드포인트 만들기 선택의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 구성 창에서

   a.    Azure Monitor 리소스에 연결하려는 **가상 네트워크** 및 **서브넷**을 선택합니다. 
 
   b.    **프라이빗 DNS 영역과 통합**에 대해 **예**를 선택하고, 새 프라이빗 DNS 영역을 자동으로 만들도록 합니다. 실제 DNS 영역은 아래 스크린샷에 표시 된 것과 다를 수 있습니다. 
 
   다.    **검토 + 만들기**를 클릭합니다.
 
   d.    유효성 검사를 통과하도록 합니다. 
 
   e.    **만들기**를 클릭합니다. 

    ![프라이빗 엔드포인트 2 만들기 선택의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

이제 이 Azure Monitor Private Link 범위에 연결된 새 프라이빗 엔드포인트를 만들었습니다.

## <a name="configure-log-analytics"></a>Log Analytics 구성

Azure Portal로 이동합니다. Log Analytics 작업 영역 리소스에는 왼쪽에 메뉴 항목 **네트워크 격리가** 있습니다. 이 메뉴에서 별도의 두 가지 상태를 제어할 수 있습니다. 

![LA 네트워크 격리](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

먼저, 액세스할 수 있는 Azure Monitor Private Link 범위에 이 Log Analytics 리소스를 연결할 수 있습니다. **추가**를 클릭하고, Azure Monitor Private Link 범위를 선택합니다.  **적용**을 클릭하여 연결합니다. 이 화면에 연결된 모든 범위가 표시됩니다. 이 연결을 설정하면 연결된 가상 네트워크의 네트워크 트래픽이 이 작업 영역에 도달할 수 있습니다. 연결은 [Azure Monitor 리소스 연결](#connect-azure-monitor-resources)에서와 같이 범위에서 연결하는 것과 동일한 효과가 있습니다.  

다음으로, 위에 나열된 프라이빗 링크 범위 외부에서 이 리소스에 연결하는 방법을 제어할 수 있습니다. **수집을 위해 공용 네트워크 액세스 허용**을 **아니요**로 설정하면 연결된 범위 외부의 머신에서 데이터를 이 작업 영역에 업로드할 수 없습니다. **쿼리를 위해 공용 네트워크 액세스 허용**을 **아니요**로 설정하면 범위 외부의 머신에서 이 작업 영역의 데이터에 액세스할 수 없습니다. 이 데이터에는 통합 문서, 대시보드, 쿼리 API 기반 클라이언트 환경, Azure Portal의 인사이트 등에 대한 액세스가 포함됩니다. Azure Portal 외부에서 실행 되는 환경 및 Log Analytics 데이터 쿼리도 개인 연결 VNET 내에서 실행 되어야 합니다.

이러한 방식으로 액세스를 제한 하는 것은 Azure Resource Manager에는 적용 되지 않으므로 다음과 같은 제한 사항이 있습니다.
* 데이터에 대 한 액세스-공용 네트워크에서 쿼리를 차단 하는 것이 대부분의 Log Analytics 환경에 적용 되는 반면, 일부는 Azure Resource Manager를 통해 쿼리 데이터를 경험 하므로 리소스 관리자에 개인 링크 설정이 적용 되지 않는 한 (곧 제공 되는 기능) 데이터를 쿼리할 수 없습니다. 여기에는 Azure Monitor 솔루션, 통합 문서, 통찰력 및 LogicApp 커넥터가 포함 됩니다.
* 작업 영역 관리-작업 영역 설정 및 구성 변경 내용 (이러한 액세스 설정을 설정 하거나 해제 하는 등)은 Azure Resource Manager를 통해 관리 됩니다. 적절 한 역할, 권한, 네트워크 제어 및 감사를 사용 하 여 작업 영역 관리에 대 한 액세스를 제한 합니다. 자세한 내용은 [Azure Monitor 역할, 권한 및 보안](roles-permissions-security.md)을 참조하세요.

> [!NOTE]
> [진단 설정](diagnostic-settings.md)을 통해 작업 영역에 업로드된 로그 및 메트릭은 안전한 프라이빗 Microsoft 채널을 통해 이동하며 이러한 설정으로 제어되지 않습니다.

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 솔루션 팩 다운로드

Log Analytics 에이전트에서 솔루션 팩을 다운로드할 수 있도록 하려면 해당하는 정규화된 도메인 이름을 방화벽 허용 목록에 추가합니다. 


| 클라우드 환경 | 에이전트 리소스 | 포트 | Direction |
|:--|:--|:--|:--|
|Azure 공용     | scadvisorcontent.blob.core.windows.net         | 443 | 아웃바운드
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  아웃바운드
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 아웃바운드

## <a name="configure-application-insights"></a>Application Insights 구성

Azure Portal로 이동합니다. Azure Monitor Application Insights 구성 요소 리소스의 왼쪽에는 **네트워크 격리** 메뉴 항목이 있습니다. 이 메뉴에서 별도의 두 가지 상태를 제어할 수 있습니다.

![AI 네트워크 격리](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

먼저, 액세스할 수 있는 Azure Monitor Private Link 범위에 이 Application Insights 리소스를 연결할 수 있습니다. **추가**를 클릭하고, **Azure Monitor Private Link 범위**를 선택합니다. [적용]을 클릭하여 연결합니다. 이 화면에 연결된 모든 범위가 표시됩니다. 이 연결을 설정하면 연결된 가상 네트워크의 네트워크 트래픽이 이 구성 요소에 도달할 수 있습니다. 연결은 [Azure Monitor 리소스 연결](#connect-azure-monitor-resources)에서와 같이 범위에서 연결하는 것과 동일한 효과가 있습니다. 

다음으로, 이전에 나열된 프라이빗 링크 범위 외부에서 이 리소스에 도달하는 방법을 제어할 수 있습니다. **수집을 위해 공용 네트워크 액세스 허용**을 **아니요**로 설정하면 연결된 범위 외부의 머신 또는 SDK에서 데이터를 이 구성 요소에 업로드할 수 없습니다. **쿼리를 위해 공용 네트워크 액세스 허용**을 **아니요**로 설정하면 범위 외부의 머신에서 이 Application Insights 리소스의 데이터에 액세스할 수 없습니다. 이 데이터에는 APM 로그, 메트릭 및 라이브 메트릭 스트림에 대한 액세스뿐만 아니라 통합 문서, 대시보드, 쿼리 API 기반 클라이언트 환경, Azure Portal의 인사이트 등을 기반으로 하는 환경도 포함됩니다. 

포털이 아닌 사용 환경도 모니터링되는 워크로드를 포함하는 프라이빗 연결 VNET 내에서 실행되어야 합니다. 

모니터링되는 워크로드를 호스팅하는 리소스를 프라이빗 링크에 추가해야 합니다. App Services에서 이 작업을 수행하는 방법을 보여 주는 [설명서](../../app-service/networking/private-endpoint.md)가 있습니다.

액세스를 이 방법으로 제한하는 경우 Application Insights 리소스의 데이터에만 적용됩니다. 이러한 액세스 설정의 켜기 또는 끄기를 포함한 구성 변경은 Azure Resource Manager에서 관리합니다. 대신 적절한 역할, 권한, 네트워크 제어 및 감사를 사용하여 Resource Manager에 대한 액세스를 제한합니다. 자세한 내용은 [Azure Monitor 역할, 권한 및 보안](roles-permissions-security.md)을 참조하세요.

> [!NOTE]
> 작업 영역 기반 Application Insights를 완벽하게 보호하려면 Application Insights 리소스 및 기본 Log Analytics 작업 영역에 대한 액세스를 모두 잠가야 합니다.
>
> 코드 수준 진단 (프로파일러/디버거)에서는 개인 링크를 지원 하기 위해 고유한 저장소 계정을 제공 해야 합니다. 이 작업을 수행 하는 방법에 대 한 [설명서](../app/profiler-bring-your-own-storage.md) 는 다음과 같습니다.

## <a name="use-apis-and-command-line"></a>API 및 명령줄 사용

Azure Resource Manager 템플릿과 명령줄 인터페이스를 사용하여 앞에서 설명한 프로세스를 자동화할 수 있습니다.

프라이빗 링크 범위를 만들고 관리하려면 [az monitor private-link-scope](/cli/azure/monitor/private-link-scope?view=azure-cli-latest)를 사용합니다. 이 명령을 사용하면 범위를 만들고, Log Analytics 작업 영역 및 Application Insights 구성 요소를 연결하며, 프라이빗 엔드포인트를 추가/제거/승인할 수 있습니다.

네트워크 액세스를 관리하려면 [Log Analytics 작업 영역](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) 또는 [Application Insights 구성 요소](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest)에서 `[--ingestion-access {Disabled, Enabled}]` 및 `[--query-access {Disabled, Enabled}]` 플래그를 사용합니다.

## <a name="collect-custom-logs-over-private-link"></a>개인 링크를 통한 사용자 지정 로그 수집

스토리지 계정은 사용자 지정 로그 수집 프로세스에서 사용됩니다. 기본적으로 서비스 관리형 스토리지 계정이 사용됩니다. 그러나 프라이빗 링크에서 사용자 지정 로그를 수집하려면 사용자 고유의 스토리지 계정을 사용하여 Log Analytics 작업 영역에 연결해야 합니다. [명령줄](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest)을 사용하여 이러한 계정을 설정하는 방법에 대한 자세한 내용을 참조하세요.

사용자 고유의 스토리지 계정을 가져오는 방법에 대한 자세한 내용은 [로그 수집에 고객이 소유한 스토리지 계정 사용](private-storage.md)을 참조하세요.

## <a name="restrictions-and-limitations"></a>제한 사항

### <a name="agents"></a>에이전트

Windows 및 Linux 에이전트의 최신 버전은 Log Analytics 작업 영역에 안전 하 게 수집할 수 있도록 개인 네트워크에서 사용 해야 합니다. 이전 버전에서는 개인 네트워크에서 모니터링 데이터를 업로드할 수 없습니다.

**Log Analytics Windows 에이전트**

Log Analytics 에이전트 버전 10.20.18038.0 이상을 사용 합니다.

**Log Analytics Linux 에이전트**

에이전트 버전 1.12.25 이상을 사용합니다. 사용할 수 없는 경우 VM에서 다음 명령을 실행합니다.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure portal

Application Insights 및 Log Analytics와 같은 Azure Monitor 포털 환경을 사용하려면 개인 네트워크에서 Azure Portal 및 Azure Monitor 확장에 액세스할 수 있어야 합니다. **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor**및 **AzureFrontDoor** [서비스 태그](../../firewall/service-tags.md) 를 네트워크 보안 그룹에 추가 합니다.

### <a name="programmatic-access"></a>프로그래밍 방식 액세스

개인 네트워크의 Azure Monitor에서 REST API, [CLI](/cli/azure/monitor?view=azure-cli-latest) 또는 PowerShell을 사용하려면 **AzureActiveDirectory** 및 **AzureResourceManager** [서비스 태그](../../virtual-network/service-tags-overview.md)를 방화벽에 추가합니다.

이러한 태그를 추가하면 로그 데이터 쿼리, Log Analytics 작업 영역과 AI 구성 요소의 만들기 및 관리와 같은 작업을 수행할 수 있습니다.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>콘텐츠 배달 네트워크에서 Application Insights SDK 다운로드

브라우저에서 CDN으로부터 코드를 다운로드하려고 시도하지 않도록 스크립트에서 JavaScript 코드를 번들로 묶습니다. 예제는 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)에서 제공됩니다.

### <a name="browser-dns-settings"></a>브라우저 DNS 설정

개인 링크를 통해 Azure Monitor 리소스에 연결 하는 경우 이러한 리소스에 대 한 트래픽은 네트워크에 구성 된 개인 끝점을 통과 해야 합니다. 개인 끝점을 사용 하도록 설정 하려면 [개인 끝점에 연결](#connect-to-a-private-endpoint)에 설명 된 대로 DNS 설정을 업데이트 합니다. 일부 브라우저는 사용자가 설정 하는 대신 자체 DNS 설정을 사용 합니다. 브라우저는 Azure Monitor 공용 끝점에 연결을 시도 하 고 개인 링크를 완전히 우회할 수 있습니다. 브라우저 설정이 이전 DNS 설정을 재정의 하거나 캐시 하지 않는지 확인 합니다. 

## <a name="next-steps"></a>다음 단계

- [개인 저장소](private-storage.md) 에 대 한 자세한 정보
