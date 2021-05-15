---
title: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결
description: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 76c6d7caf3c63779e12443304688192f7311720a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594566"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다. 많은 서비스의 경우 리소스당 엔드포인트를 설정하기만 하면 됩니다. 그러나 Azure Monitor는 동시에 작동하여 워크로드를 모니터링하는 상호 연결된 서로 다른 서비스의 모음입니다. 그 결과 Azure Monitor 프라이빗 링크 범위(AMPLS)라는 리소스를 구축했습니다. AMPLS를 사용하면 모니터링 네트워크의 경계를 정의하고 가상 네트워크에 연결할 수 있습니다. 이 문서에서는 Azure Monitor Private Link 범위를 사용하는 경우와 설정하는 방법에 대해 설명합니다.

## <a name="advantages"></a>장점

Private Link를 사용하면 다음을 수행할 수 있습니다.

- 공용 네트워크 액세스를 개방하지 않고 Azure Monitor에 비공개로 연결
- 권한 있는 개인 네트워크를 통해서만 모니터링 데이터에 액세스할 수 있는지 확인
- 프라이빗 엔드포인트를 통해 연결되는 특정 Azure Monitor 리소스를 정의하여 개인 네트워크에서 데이터 반출 방지
- ExpressRoute 및 Private Link를 사용하여 온-프레미스 개인 네트워크를 Azure Monitor에 안전하게 연결
- Microsoft Azure 백본 네트워크 내에서 모든 트래픽 유지

자세한 내용은 [Private Link의 주요 이점](../../private-link/private-link-overview.md#key-benefits)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

Azure Monitor 프라이빗 링크 범위 (AMPLS)는 엔드포인트 (및 포함된 VNet)을 하나 이상의 Azure Monitor 리소스-Log Analytics 작업 영역 및 Application Insights 구성 요소-에 연결합니다.

![기본 리소스 토폴로지 다이어그램](./media/private-link-security/private-link-basic-topology.png)

* VNet의 프라이빗 엔드포인트는 이러한 엔드포인트의 공용 IP를 사용하는 대신 네트워크 풀에서 프라이빗 IP를 통해 Azure Monitor 엔드포인트에 도착할 수 있습니다. 이렇게 하면 불필요한 아웃 바운드 트래픽까지 VNet을 열지 않고도 Azure Monitor 리소스를 계속 사용할 수 있습니다. 
* 프라이빗 엔드포인트에서 Azure Monitor 리소스에 대한 트래픽은 Microsoft Azure 백본을 지나며, 공용 네트워크로 라우팅되지 않습니다. 
* 공용 네트워크에서 수집 및 쿼리를 허용하거나 거부하도록 각 작업 영역 또는 컴포넌트를 구성할 수 있습니다. 리소스 수준의 보호를 제공하므로 특정 리소스에 대한 트래픽을 제어할 수 있습니다.

> [!NOTE]
> 단일 Azure Monitor 리소스는 여러 AMPLS에 속할 수 있지만, 단일 VNet을 둘 이상의 AMPLS에 연결할 수는 없습니다. 

## <a name="planning-your-private-link-setup"></a>프라이빗 링크 설정 계획

Azure Monitor 프라이빗 링크 설치를 설정하기 전에, 네트워크 토폴로지 및 특히 DNS 라우팅 토폴로지를 고려합니다. 

### <a name="the-issue-of-dns-overrides"></a>DNS 재정의 문제
일부 Azure Monitor 서비스는 글로벌 엔드포인트 사용합니다. 즉, 작업 영역/컴포넌트를 대상으로 하는 요청을 처리합니다. 몇 가지 예는 Application Insights 수집 엔드포인트와 Application Insights 및 Log Analytics의 쿼리 엔드포인트입니다.

프라이빗 링크 연결을 설정하면 Azure Monitor 엔드포인트를 VNet의 IP 범위에서 프라이빗 IP 주소에 매핑하기 위해 DNS가 업데이트됩니다. 이 변경 내용은 이러한 엔드포인트의 이전 매핑을 재정의하며, 여기에는 검토된 바와 같이 의미 있는 영향을 가질 수 있습니다. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor 프라이빗 링크는 모든 Azure Monitor 리소스에 적용됩니다. - 모두 또는 없음입니다.
일부 Azure Monitor 엔드포인트는 전역적이므로 특정 구성 요소나 작업 영역에 대한 프라이빗 링크 연결을 만들 수 없습니다. 대신 단일 Application Insights 구성 요소나 Log Analytics 작업 영역에 대한 프라이빗 링크를 설정하면, **모든** Application Insights 구성 요소에 대한 DNS 레코드가 업데이트됩니다. 어떤 구성 요소 수집 또는 쿼리 시도는 프라이빗 링크를 통과하고, 그리고 실패하기도 합니다. Log Analytics와 관련하여 수집 및 구성 엔드포인트는 작업 영역별로 다릅니다. 즉, 프라이빗 링크 설정은 지정된 작업 영역에만 적용됩니다. 다른 작업 영역의 수집 및 구성은 기본 공용 Log Analytics 엔드포인트로 전달됩니다.

![단일 VNet의 DNS 재정의 다이어그램](./media/private-link-security/dns-overrides-single-vnet.png)

이는 특정 VNet뿐만 아니라 동일한 DNS 서버를 공유하는 모든 Vnet에 적용됩니다( [DNS 재정의 문제](#the-issue-of-dns-overrides) 참조). 따라서, 예를 들어 Application Insights 구성 요소에 대한 로그 수집 요청은 항상 프라이빗 링크 경로를 통해 전송됩니다. AMPLS에 연결되지 않은 구성 요소는 프라이빗 링크 유효성 검사에 실패하여 이동하지 않습니다.

> [!NOTE]
> 결론적으로: 일단 단일 리소스에 대한 프라이빗 링크 연결을 설정하면, 네트워크를 통해 Azure Monitor 리소스에 적용됩니다. Application Insights 리소스의 경우 ' 모두' 또는 ' 없음'입니다. 즉, 네트워크의 모든 Application Insight 리소스를 AMPS에 추가하거나 리소스를 추가하지 않아야 합니다.
> 
> 데이터 반출 위험을 다루려면 AMPLS에 모든 Application Insights 및 Log Analytics 리소스를 추가하고, 가능한 한 네트워크 송신 트래픽을 차단하는 것이 좋습니다.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor 프라이빗 링크가 전체 네트워크에 적용됩니다.
일부 네트워크는 여러 가상 네트워크로 구성됩니다. Vnet에서 동일한 DNS 서버를 사용하는 경우, 각각의 DNS 매핑을 재정의하고 Azure Monitor와의 서로 다른 통신을 중단할 수 있습니다 ( [DNS 재정의 문제](#the-issue-of-dns-overrides)참조). 궁극적으로, DNS가 Azure Monitor 엔드포인트를 이 VNet 범위에서 프라이빗 IP에 매핑하므로 마지막 VNet만 Azure Monitor와 통신할 수 있습니다 (다른 Vnet에서 도달할 수 없음).

![복합 Vnet의 DNS 재정의 다이어그램](./media/private-link-security/dns-overrides-multiple-vnets.png)

위의 다이어그램에서 VNet 10.0.1.x는 먼저 AMPLS1에 연결하고, Azure Monitor 전역 엔드포인트를 해당 범위에서 IP에 매핑합니다. 이후에 VNet 10.0.2.x는 AMPLS2에 연결하고, 범위에서 IP를 사용하여 *동일한 글로벌 엔드포인트* 의 DNS 매핑을 재정의합니다. 이러한 Vnet는 피어링되지 않으므로, 첫 번째 VNet은 이제 이러한 엔드포인트에 도달하지 못합니다.

> [!NOTE]
> 결론을 짓자면: AMPLS 설치는 동일한 DNS 영역을 공유하는 모든 네트워크에 영향을 줍니다. 각각의 DNS 엔드포인트 매핑을 재정의하지 않으려면, 피어링 네트워크 (예: 허브 VNet)에서 단일 프라이빗 엔드포인트를 설정하거나 DNS 수준에서 네트워크를 분리하는 것이 가장 좋습니다 (예를 들어, DNS 전달자를 사용하거나 별도의 DNS 서버를 사용).

### <a name="hub-spoke-networks"></a>Hub-spoke 네트워크
Hub-spoke 토폴로지는 각 VNet에 대해 개별적으로 프라이빗 링크를 설정하는 대신 허브(메인) VNet에서 프라이빗 링크를 설정함으로써 DNS 재정의 문제를 방지할 수 있습니다. 특히 spoke Vnet에서 사용하는 Azure Monitor 리소스를 공유하는 경우 이 설정을 사용하는 것이 좋습니다. 

![Hub-및-spoke-단일-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> 예를 들자면 각 VNet이 제한된 모니터링 리소스 집합에 액세스할 수 있도록 하는 등 spoke Vnet 별도의 프라이빗 링크를 만들 수 있습니다. 이러한 경우에는 각 VNet에 대한 전용 프라이빗 엔드포인트 및 AMPLS를 만들 수 있지만, DNS 재정의를 방지하기 위해 동일한 DNS 영역을 공유하지 않는지 확인해야 합니다.


### <a name="consider-limits"></a>제한 사항 고려

[제약 및 제한 사항](#restrictions-and-limitations)에 나열된 것처럼 AMPLS 개체에는 아래 토폴로지에 나타나는 몇 가지 제한점이 있습니다.
* 각 VNet은 **1개의** AMPLS 개체에만 연결합니다.
* AMPLS B는 열 개 중 두 개의 프라이빗 엔드포인트를 사용하여 두 Vnet(VNet2 및 VNet3)의 프라이빗 엔드포인트로 연결됩니다.
* AMPLS A는 세 개에서 50개의 가능한 Azure Monitor 리소스 연결을 사용하여 두 개의 작업 영역 및 하나의 애플리케이션 정보 구성 요소에 연결합니다.
* Workspace2는 5개의 가능한 AMPLS 연결 중 2가지를 사용하여 AMPLS A 및 AMPLS B에 연결합니다.

![AMPLS 제한 다이어그램](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>연결 예

먼저 Azure Monitor Private Link 범위 리소스를 만듭니다.

1. Azure Portal에서 **리소스 만들기** 로 이동하여 **Azure Monitor Private Link 범위** 를 검색합니다.

   ![Azure Monitor 프라이빗 링크 범위](./media/private-link-security/ampls-find-1c.png)

2. **만들기** 를 선택합니다.
3. 구독 및 리소스 그룹을 선택합니다.
4. AMPLS 이름을 지정합니다. "AppServerProdTelem"와 같이 의미 있고 명확한 이름을 사용하는 것이 가장 좋습니다.
5. **검토 + 생성** 를 선택합니다. 

   ![Azure Monitor 프라이빗 링크 범위 생성](./media/private-link-security/ampls-create-1d.png)

6. 유효성 검사를 통과하도록 한 다음, **생성** 을 선택합니다.

### <a name="connect-azure-monitor-resources"></a>Azure Monitor 리소스 연결

Azure Monitor 리소스(Log Analytics 작업 공간 및 Application Insights 구성 요소)를 AMPLS에 연결합니다.

1. Azure Monitor 프라이빗 링크 범위에서 왼쪽 메뉴의 **Azure Monitor 리소스** 를 클릭합니다. **추가** 단추를 선택합니다.
2. 작업 영역 또는 구성 요소를 추가합니다. **추가** 단추를 클릭하면 Azure Monitor 리소스를 선택할 수 있는 대화 상자가 표시됩니다. 구독 및 리소스 그룹을 찾아보거나 이름을 입력하여 이를 필터링할 수 있습니다. 작업 영역 또는 구성 요소를 선택하고, **적용** 을 클릭하여 이를 범위에 추가합니다.

    ![범위 선택 UX의 스크린샷](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Monitor 리소스를 삭제하려면 먼저 연결된 모든 AMPLS 개체에서 리소스의 연결을 끊어야 합니다. AMPLS에 연결된 리소스는 삭제할 수 없습니다.

### <a name="connect-to-a-private-endpoint"></a>프라이빗 엔드포인트에 연결

이제 AMPLS에 연결된 리소스가 있으므로 네트워크를 연결 하는 프라이빗 엔드포인트를 만듭니다. 다음 예에서와 같이 이 작업은 [Azure Portal Private Link 센터](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) 또는 Azure Monitor Private Link 범위 내에서 수행할 수 있습니다.

1. 범위 리소스에서 왼쪽 리소스 메뉴에서 **프라이빗 엔드포인트 연결** 을 클릭합니다. **프라이빗 엔드포인트** 를 클릭하여 엔드포인트 생성 프로세스를 시작합니다. 그것들을 선택하고 **승인** 을 클릭하여 여기 프라이빗 링크 센터에서 시작된 연결을 승인할 수도 있습니다.

    ![프라이빗 엔드포인트 연결 UX의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 구독, 리소스 그룹, 엔드포인트 이름 및 거주하는 지역을 선택합니다. 지역은 연결할 가상 네트워크와 동일한 영역이어야 합니다

3. 완료되면 **다음: 리소스** 를 선택합니다. 

4. [리소스] 화면에서

   a. Azure Monitor 프라이빗 범위 리소스가 포함된 **구독** 을 선택합니다. 

   b. **리소스 종류** 에 대해 **Microsoft.insights/privateLinkScopes** 를 선택합니다. 

   다. **리소스** 드롭다운에서 이전에 만든 Private Link 범위를 선택합니다. 

   d. **다음: 구성 >** 을 선택합니다.
      ![프라이빗 엔드포인트 만들기 선택의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 구성 창에서

   a.    Azure Monitor 리소스에 연결하려는 **가상 네트워크** 및 **서브넷** 을 선택합니다. 
 
   b.    **프라이빗 DNS 영역과 통합** 에 대해 **예** 를 선택하고, 새 프라이빗 DNS 영역을 자동으로 만들도록 합니다. 실제 DNS 영역은 아래 스크린샷에 표시된 것과 다를 수도 있습니다. 
   > [!NOTE]
   > **아니요** 를 선택하고 DNS 레코드를 수동으로 관리하려면, 먼저 이 프라이빗 엔드포인트 및 AMPLS 구성을 포함한 프라이빗 링크 설정을 완료합니다. 그런 다음 [Azure 프라이빗 엔드포인트 DNS 구성](../../private-link/private-endpoint-dns.md)의 지침에 따라 DNS를 구성합니다. Private Link 설정을 위한 준비로 빈 레코드를 만들지 않도록 합니다. 만드는 DNS 레코드는 기존 설정을 재정의하고 Azure Monitor와의 연결에 영향을 줄 수 있습니다.
 
   다.    **검토 + 만들기** 를 선택합니다.
 
   d.    유효성 검사를 통과하도록 합니다. 
 
   e.    **만들기** 를 선택합니다. 

    ![프라이빗 엔드포인트 선택의 세부 사항 스크린샷.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

이제 이 AMPLS에 연결된 새 프라이빗 엔드포인트를 만들었습니다.

## <a name="review-and-validate-your-private-link-setup"></a>프라이빗 링크 설정 검토 및 유효성 검사

### <a name="reviewing-your-endpoints-dns-settings"></a>엔드포인트의 DNS 설정 검토
이제 만든 프라이빗 엔드포인트에 4개의 DNS 영역을 구성해야 합니다.

[ ![프라이빗 엔드포인트 DNS 영역의 스크린샷.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net

> [!NOTE]
> 이러한 각 영역은 특정 Azure Monitor 엔드포인트를 VNet의 IP 풀에서 프라이빗 IP에 매핑합니다. 아래 이미지에 표시된 IP 주소는 예시일 뿐입니다. 대신 구성에 사용자 네트워크의 프라이빗 IP가 표시되어야 합니다.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
이 영역에는 Azure Monitor에서 사용하는 전역 엔드포인트가 포함되어 있으며, 이러한 엔드포인트는 특정 리소스가 아닌 모든 리소스를 고려하는 요청을 처리합니다. 이 영역에는 다음에 대한 엔드포인트가 매핑되어야 합니다.
* `in.ai` -(Application Insights 수집 엔드포인트, 전역 및 지역별 항목이 표시됩니다.
* `api` - Application Insights 및 Log Analytics API 엔드포인트
* `live` - Application Insights 라이브 메트릭 엔드포인트
* `profiler` - Application Insights 프로파일러 엔드포인트
* `snapshot` - Application Insights 스냅샷 엔드포인트 [![프라이빗 DNS 영역 모니터의 스크린샷-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
이 영역에서는 OMS 엔드포인트에 대한 작업 영역별 매핑을 다룹니다. 이 프라이빗 엔드포인트와 연결된 AMPLS에 연결된 각 작업 영역에 대한 항목이 표시됩니다.
[![프라이빗 DNS 영역의 스크린샷 oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
이 영역에서는 Log Analytics의 수집 엔드포인트와 ODS 엔드포인트에 특정한 작업 영역별 매핑을 다룹니다. 이 프라이빗 엔드포인트와 연결된 AMPLS에 연결된 각 작업 영역에 대한 항목이 표시됩니다.
[![프라이빗 DNS 영역의 스크린샷 ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
이 영역에서는 에이전트 서비스 자동화 엔드포인트에 특정한 작업 영역별 매핑을 다룹니다. 이 프라이빗 엔드포인트와 연결된 AMPLS에 연결된 각 작업 영역에 대한 항목이 표시됩니다.
[![프라이빗 DNS 영역 에이전트의 스크린샷 svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

### <a name="validating-you-are-communicating-over-a-private-link"></a>프라이빗 링크를 통해 통신하고 있는지 확인
* 이제 요청이 프라이빗 엔드포인트를 통해 전송되고 프라이빗 IP 매핑 끝점을 통해 전송되는 것을 확인하기 위해 네트워크 추적을 사용하여 도구 또는 브라우저에서도 검토할 수 있습니다. 예를 들어, 작업 영역 또는 애플리케이션을 쿼리하려는 경우 요청이 API 엔드포인트에 매핑되는 프라이빗 IP로 전송되었는지 확인해야 하며, 이 예제에서는 *172.17.0.9* 입니다.

    참고: 일부 브라우저는 다른 DNS 설정을 사용할 수도 있습니다 ( [브라우저 DNS 설정](#browser-dns-settings)참조). DNS 설정이 적용되는지 확인합니다.

* 작업 영역 또는 구성 요소가 공용 네트워크에서 요청을 받지 않도록 하려면 (AMPLS을 통해 연결되지 않음), [프라이빗 링크 범위 외부에서 액세스 관리에서 설명한 대로 리소스의 공개 수집 및 쿼리 플래그를 *아니오* 로 설정합니다 ](#manage-access-from-outside-of-private-links-scopes).

* 보호된 네트워크의 클라이언트에서 `nslookup` DNS 영역에 나열된 엔드포인트 중 하나를 사용합니다. DNS 서버에서 기본적으로 확인되는 공용 IP 대신 매핑된 프라이빗 IP로 확인되어야 합니다.


## <a name="configure-log-analytics"></a>Log Analytics 구성

Azure Portal로 이동합니다. Log Analytics 작업 영역 리소스 메뉴에는 왼쪽에 **네트워크 분리** 라는 항목이 있습니다. 이 메뉴에서 별도의 두 가지 상태를 제어할 수 있습니다.

![LA 네트워크 격리](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>연결된 Azure Monitor 프라이빗 링크 범위
작업 영역에 연결된 모든 범위가 이 화면에 표시됩니다. 범위(AMPLSs)에 연결하면 각 AMPLS에 연결된 가상 네트워크의 네트워크 트래픽이 이 작업 영역에 도달할 수 있습니다. 여기를 통해 연결을 생성하면 [Azure Monitor 리소스를 연결](#connect-azure-monitor-resources)했던 때와 같이 범위에서 설정하는 것과 동일한 효과가 있습니다. 새 연결을 추가하려면 **추가** 를 선택하고 Azure Monitor 프라이빗 링크 범위를 선택합니다. **적용** 을 클릭하여 연결합니다. 참고: [제약 및 제한사항](#restrictions-and-limitations)에 설명된 대로 작업 영역은 5개의 AMPLS 개체에 연결할 수 있습니다. 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>프라이빗 링크 범위 외부에서 액세스 관리
이 페이지의 맨 아래에 있는 설정은 공용 네트워크에서의 액세스를 제어하고, 즉, 위에 나열된 범위를 통해 연결되지 않은 네트워크를 의미합니다. **수집을 위한 공용 네트워크 액세스 허용** 을 **아니오** 로 설정하면 연결된 범위를 벗어나는 시스템에서 로그를 수집하도록 차단합니다. **수집을 위한 공용 네트워크 액세스 허용** 을 **아니오** 로 설정하면 연결된 범위를 벗어나는 시스템에서 오는 쿼리를 차단합니다. 이 데이터에는 통합 문서, 대시보드, API 기반 클라이언트 환경, Azure Portal의 인사이트 등에 대한 액세스가 포함됩니다. Azure Portal 외부에서 실행 중인 경험과 이 쿼리 로그 분석 데이터도 프라이빗 링크된 VNET 내에서 실행되어야 합니다.

### <a name="exceptions"></a>예외
위에서 설명한 대로 액세스를 제한하는 것은 Azure Resource Manager에는 적용되지 않고 다음과 같은 제한 사항이 있습니다.
* 데이터에 대한 액세스-공용 네트워크에서의 쿼리를 차단/허용하는 것이 대부분의 Log Analytics 경험에 적용되는 반면, 일부는 Azure Resource Manager를 통해 쿼리 데이터를 경험하므로 리소스 관리자에도 프라이빗 링크 설정이 적용되지 않는 한 데이터를 쿼리할 수 없습니다. (기능이 곧 제공될 예정입니다.) Azure Monitor 솔루션, 통합 문서 및 인사이트, LogicApp 커넥터 등을 예로 들 수 있습니다.
* (이러한 액세스 설정의 켜기 또는 끄기를 포함한)작업 영역 관리-작업 영역 구성 및 변경은 Azure Resource Manager에서 관리합니다. 적절한 역할, 권한, 네트워크 제어 및 감사를 사용하여 작업 영역 관리에 대한 액세스를 제한합니다. 자세한 내용은 [Azure Monitor 역할, 권한 및 보안](../roles-permissions-security.md)을 참조하세요.

> [!NOTE]
> [진단 설정](../essentials/diagnostic-settings.md)을 통해 작업 영역에 업로드된 로그 및 메트릭은 안전한 프라이빗 Microsoft 채널을 통해 이동하며 이러한 설정으로 제어되지 않습니다.

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 솔루션 팩 다운로드

Log Analytics 에이전트에서 솔루션 팩을 다운로드할 수 있도록 하려면 해당하는 정규화된 도메인 이름을 방화벽 허용 목록에 추가합니다. 


| 클라우드 환경 | 에이전트 리소스 | 포트 | Direction |
|:--|:--|:--|:--|
|Azure 공용     | scadvisorcontent.blob.core.windows.net         | 443 | 아웃바운드
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  아웃바운드
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 아웃바운드

## <a name="configure-application-insights"></a>Application Insights 구성

Azure Portal로 이동합니다. Azure Monitor Application Insights 구성 요소 리소스의 왼쪽에는 **네트워크 격리성** 메뉴 항목이 있습니다. 이 메뉴에서 별도의 두 가지 상태를 제어할 수 있습니다.

![AI 네트워크 격리](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

먼저, 액세스할 수 있는 Azure Monitor Private Link 범위에 이 Application Insights 리소스를 연결할 수 있습니다. **추가** 를 클릭하고, **Azure Monitor 프라이빗 링크 범위** 를 선택합니다. [적용]을 클릭하여 연결합니다. 이 화면에 연결된 모든 범위가 표시됩니다. 이 연결을 설정하면 연결된 가상 네트워크의 네트워크 트래픽이 이 구성 요소에 연결될 수 있으며, [Azure Monitor 리소스를 연결](#connect-azure-monitor-resources)할 때와 마찬가지로 범위에서 연결하는 것과 같은 효과가 있습니다. 

다음으로, 이전에 나열된 프라이빗 링크 범위 외부에서 이 리소스에 도달하는 방법을 제어할 수 있습니다. **수집을 위한 공용 네트워크 액세스 허용** 을 **아니요** 로 설정하면 연결된 범위 외부의 머신 또는 SDK에서 이 구성 요소에 데이터를 업로드할 수 없습니다. **쿼리를 위한 공용 네트워크 액세스 허용** 을 **아니요** 로 설정하면 범위 외부의 머신에서 이 Application Insights 리소스의 데이터에 액세스할 수 없습니다. 이 데이터에는 APM 로그, 메트릭 및 라이브 메트릭 스트림에 대한 액세스뿐만 아니라 통합 문서, 대시보드, 쿼리 API 기반 클라이언트 환경, Azure Portal의 인사이트 등을 기반으로 하는 환경도 포함됩니다. 

> [!NOTE]
> 포털이 아닌 사용 환경은 모니터링되는 워크로드를 포함하는 프라이빗 링크 VNET에서도 실행해야 합니다.

모니터링되는 워크로드를 호스팅하는 리소스를 프라이빗 링크에 추가해야 합니다. 예를 들어, [Azure 웹 앱용 프라이빗 엔드포인트 사용](../../app-service/networking/private-endpoint.md)을 참조하십시오.

액세스를 이 방법으로 제한하는 경우 Application Insights 리소스의 데이터에만 적용됩니다. 이러한 액세스 설정의 켜기 또는 끄기를 포함한 구성 변경은 Azure Resource Manager에서 관리합니다. 그래서 적절한 역할, 권한, 네트워크 제어 및 감사를 통해 Resource Manager에 대한 액세스를 제한해야 합니다. 자세한 내용은 [Azure Monitor 역할, 권한 및 보안](../roles-permissions-security.md)을 참조하세요.

> [!NOTE]
> 작업 영역 기반 Application Insights를 완벽하게 보호하려면 Application Insights 리소스 및 기본 Log Analytics 작업 영역에 대한 액세스를 모두 잠가야 합니다.
>
> 코드 수준 진단 (프로파일러/디버거)는 프라이빗 링크를 지원하기 위한 [고유의 스토리지 계정을 제공](../app/profiler-bring-your-own-storage.md)해야 합니다.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Private 링크의 All 또는 Nothing 특성 처리
[프라이빗 링크 설정 계획](#planning-your-private-link-setup)에 설명된 대로 설정하면 단일 리소스에 대한 프라이빗 링크 설정도 해당 네트워크 및 동일한 DNS를 공유하는 다른 네트워크에 있는 모든 Azure Monitor 리소스에 영향을 줍니다. 이 동작은 온보딩 프로세스를 어렵게 만들 수 있습니다. 다음 옵션을 살펴보세요.

* 모두 - 가장 간단하고 안전한 방법은 AMPLS에 모든 Application Insights 구성 요소를 추가하는 것입니다. 다른 네트워크에서도 계속 액세스하려는 구성 요소의 경우 "수집/쿼리에 대한 공용 인터넷 액세스 허용" 플래그를 [예] (기본값)로 설정된 상태로 둡니다.
* 네트워크 격리 - spoke vnet을 사용한다면 (또는 spoke vnet에 맞출 수 있는 경우) [Azure의 Hub-spoke 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)의 지침을 따릅니다. 그런 다음, 관련 spoke VNets에서 별도의 개인 링크 설정을 구성합니다. DNS 영역을 다른 spoke 네트워크와 공유하면 [DNS 재정의](#the-issue-of-dns-overrides)가 발생하므로 DNS 영역도 구분하십시오.
* 특정 앱에 사용자 지정 DNS 영역 사용 - 이 솔루션을 사용하면 공용 경로를 통해 다른 모든 트래픽을 유지하면서 프라이빗 링크를 통해 선택 Application Insights 구성 요소에 액세스할 수 있습니다.
    - [사용자 지정 프라이빗 DNS 영역](../../private-link/private-endpoint-dns.md)을 설정하고 internal.monitor.azure.com와 같은 고유한 이름을 지정합니다.
    - AMPLS 및 프라이빗 엔드포인트를 생성하고, 프라이빗 DNS와 자동으로 통합하지 **않도록** 선택합니다.
    - 프라이빗 엔드포인트 -> DNS 구성으로 이동하고 제안된 FQDNs의 매핑을 검토합니다.
    - [구성 추가]를 선택하고 방금 만든 internal.monitor.azure.com 영역을 선택합니다.
    - ![구성된 DNS 영역의 위 스크린샷](./media/private-link-security/private-endpoint-global-dns-zone.png)에 대한 레코드 추가
    - Application Insights 구성 요소로 이동하여 [연결 문자열](../app/sdk-connection-string.md)을 복사합니다.
    - 프라이빗 링크를 통해 이 구성 요소를 호출하려는 앱 또는 스크립트는 EndpointSuffix=internal.monitor.azure.com과 함께 연결 문자열을 사용해야 합니다.
* DNS 대신 호스트 파일을 통한 엔드포인트 매핑-네트워크의 특정 컴퓨터/VM 에서만 프라이빗 링크에 액세스할 수 있습니다.
    - AMPLS 및 프라이빗 엔드포인트를 설정하고, 프라이빗 DNS와 자동으로 통합하지 **않도록** 선택합니다. 
    - 호스트 파일에서 앱을 실행하는 컴퓨터에서 위의 A 레코드를 구성합니다.


## <a name="use-apis-and-command-line"></a>API 및 명령줄 사용

Azure Resource Manager 템플릿과 명령 줄 인터페이스를 사용하여 앞에서 설명한 프로세스를 자동화할 수 있습니다.

프라이빗 링크 범위를 생성하고 관리하려면 [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) 혹은 [Azure CLI(az monitor private-link-scope)](/cli/azure/monitor/private-link-scope)를 사용합니다.

네트워크 액세스를 관리하려면 [Log Analytics 작업 영역](/cli/azure/monitor/log-analytics/workspace) 또는 [Application Insights 구성 요소](/cli/azure/ext/application-insights/monitor/app-insights/component)에서 `[--ingestion-access {Disabled, Enabled}]` 및 `[--query-access {Disabled, Enabled}]` 플래그를 사용합니다.

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>프라이빗 링크를 통한 사용자 지정 로그 및 IIS 로그 수집

스토리지 계정은 사용자 지정 로그 수집 프로세스에서 사용됩니다. 기본적으로 서비스 관리형 스토리지 계정이 사용됩니다. 그러나 프라이빗 링크에서 사용자 지정 로그를 수집하려면 사용자 고유의 스토리지 계정을 사용하여 Log Analytics 작업 영역에 연결해야 합니다. [명령줄](/cli/azure/monitor/log-analytics/workspace/linked-storage)을 사용하여 이러한 계정을 설정하는 방법에 대한 자세한 내용을 참조하세요.

사용자 고유의 스토리지 계정을 가져오는 방법에 대한 자세한 내용은 [로그 수집에 고객이 소유한 스토리지 계정 사용](private-storage.md)을 참조하세요.

## <a name="restrictions-and-limitations"></a>제한 사항

### <a name="ampls"></a>AMPLS
AMPLS 개체에는 프라이빗 링크 설정을 계획할 때 고려해야 할 몇 가지 제한 사항이 있습니다.

* VNet은 1개의 AMPLS 개체에만 연결할 수 있습니다. 즉, AMPLS 개체는 VNet이 액세스할 수 있어야 하는 모든 Azure Monitor 리소스에 대한 액세스를 제공해야 합니다.
* Azure Monitor 리소스(작업 영역 또는 Application Insights 구성 요소)는 최대 5개의 AMPLS에 연결할 수 있습니다.
* 한 AMPLS 개체는 최대 50개의 Azure Monitor 리소스에 연결할 수 있습니다.
* 한 AMPLS 개체는 최대 10개의 프라이빗 엔드포인트에 연결할 수 있습니다.

이러한 제한 사항을 자세히 검토하려면 [제한 사항 고려](#consider-limits)를 참조하세요.

### <a name="agents"></a>에이전트

로그 분석 작업 영역에 대한 안전한 원격 분석 수집을 사용하도록 설정하려면 최신 버전의 Windows 및 Linux 에이전트를 사용해야 합니다. 이전 버전에서는 프라이빗 네트워크에서 모니터링 데이터를 업로드할 수 없습니다.

**Log Analytics Windows 에이전트**

Log Analytics 에이전트 버전 10.20.18038.0 이상을 사용합니다.

**Log Analytics Linux 에이전트**

에이전트 버전 1.12.25 이상을 사용합니다. 할 수 없는 경우 VM에서 다음 명령을 실행합니다.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure portal

Application Insights 및 Log Analytics와 같은 Azure Monitor 포털 환경을 사용하려면 개인 네트워크에서 Azure Portal 및 Azure Monitor 확장에 액세스할 수 있어야 합니다. **AzureActiveDirectory**, **AzureResourceManager**, AzureFrontDoor.FirstParty 및 **AzureFrontdoor.Frontend** [서비스 태그를](../../firewall/service-tags.md) 네트워크 보안 그룹에 추가합니다.

### <a name="querying-data"></a>데이터 쿼리
[ `externaldata` 운영자](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor)는 프라이빗 링크에 대해 지원되지 않지만 스토리지 계정에서 데이터를 읽으므로, 스토리지에 대해 개인적으로 액세스되는 걸 보장하지는 않습니다.

### <a name="programmatic-access"></a>프로그래밍 방식 액세스

프라이빗 네트워크의 Azure Monitor에서 REST API, [CLI](/cli/azure/monitor) 또는 PowerShell을 사용하려면 **AzureActiveDirectory** 및 **AzureResourceManager** [서비스 태그](../../virtual-network/service-tags-overview.md)를 방화벽에 추가합니다.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>콘텐츠 배달 네트워크에서 Application Insights SDK 다운로드

브라우저에서 CDN으로부터 코드를 다운로드하려고 시도하지 않도록 스크립트에서 JavaScript 코드를 추가로 줍니다. 예제는 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)에서 제공됩니다.

### <a name="browser-dns-settings"></a>브라우저 DNS 설정

프라이빗 링크를 통해 Azure Monitor 리소스에 연결하는 경우 이러한 리소스에 대한 트래픽은 네트워크에 구성된 프라이빗 엔드포인트를 통해 이동해야 합니다. 프라이빗 엔드포인트를 사용하도록 설정하려면 [프라이빗 엔드포인트에 연결](#connect-to-a-private-endpoint)에 설명된 대로 DNS 설정을 업데이트합니다. 일부 브라우저는 사용자가 설정하는 대신 자체 DNS 설정을 사용합니다. 브라우저는 Azure Monitor 공용 엔드포인트에 연결을 시도하고 프라이빗 링크를 완전히 우회할 수 있습니다. 브라우저 설정이 이전 DNS 설정을 재정의하거나 캐시하지 않는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

- [개인 스토리지](private-storage.md)에 대한 자세한 정보