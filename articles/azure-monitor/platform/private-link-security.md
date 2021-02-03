---
title: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결
description: Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: a7464216649d6b482893693a1f182af5cf6e77ac
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508996"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다. 많은 서비스의 경우 리소스당 엔드포인트를 설정하기만 하면 됩니다. 그러나 Azure Monitor는 동시에 작동하여 워크로드를 모니터링하는 상호 연결된 서로 다른 서비스의 모음입니다. 결과적으로 Azure Monitor 개인 링크 범위 (AMPLS) 라는 리소스를 빌드 했습니다. AMPLS를 사용 하면 모니터링 네트워크의 경계를 정의 하 고 가상 네트워크에 연결할 수 있습니다. 이 문서에서는 Azure Monitor Private Link 범위를 사용하는 경우와 설정하는 방법에 대해 설명합니다.

## <a name="advantages"></a>장점

Private Link를 사용하면 다음을 수행할 수 있습니다.

- 공용 네트워크 액세스를 개방하지 않고 Azure Monitor에 비공개로 연결
- 권한 있는 개인 네트워크를 통해서만 모니터링 데이터에 액세스할 수 있는지 확인
- 프라이빗 엔드포인트를 통해 연결되는 특정 Azure Monitor 리소스를 정의하여 개인 네트워크에서 데이터 반출 방지
- ExpressRoute 및 Private Link를 사용하여 온-프레미스 개인 네트워크를 Azure Monitor에 안전하게 연결
- Microsoft Azure 백본 네트워크 내에서 모든 트래픽 유지

자세한 내용은 [Private Link의 주요 이점](../../private-link/private-link-overview.md#key-benefits)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

Azure Monitor 개인 링크 범위 (AMPLS)는 전용 끝점 (및 포함 된 Vnet)을 하나 이상의 Azure Monitor 리소스 Log Analytics 작업 영역 및 Application Insights 구성 요소에 연결 합니다.

![기본 리소스 토폴로지 다이어그램](./media/private-link-security/private-link-basic-topology.png)

> [!NOTE]
> 단일 Azure Monitor 리소스는 여러 AMPLS에 속할 수 있지만, 단일 VNet을 둘 이상의 AMPLS에 연결할 수는 없습니다. 

### <a name="the-issue-of-dns-overrides"></a>DNS 재정의 문제
Log Analytics 및 Application Insights는 모든 서비스에 대해 전역 끝점을 사용 합니다. 즉, 모든 작업 영역/구성 요소를 대상으로 하는 요청을 처리 합니다. 예를 들어 Application Insights는 로그 수집을 위해 전역 끝점을 사용 하 고 Application Insights 및 Log Analytics는 모두 쿼리 요청에 대해 전역 끝점을 사용 합니다.

개인 링크 연결을 설정 하면 Azure Monitor 끝점을 VNet의 IP 범위에서 개인 IP 주소에 매핑하기 위해 DNS가 업데이트 됩니다. 이 변경 내용은 이러한 끝점의 이전 매핑을 재정의 합니다. 여기에는 의미 있는 의미가 있을 수 있습니다. 

## <a name="planning-based-on-your-network-topology"></a>네트워크 토폴로지를 기반으로 계획

Azure Monitor 개인 링크 설치를 설정 하기 전에 네트워크 토폴로지 및 특히 DNS 라우팅 토폴로지를 고려 합니다. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor 개인 링크는 모든 Azure Monitor 리소스에 적용 됩니다. All 또는 Nothing입니다.
일부 Azure Monitor 끝점은 전역적 이므로 특정 구성 요소나 작업 영역에 대 한 개인 링크 연결을 만들 수 없습니다. 대신 단일 Application Insights 구성 요소에 대 한 개인 링크를 설정 하면 **모든** Application Insights 구성 요소에 대 한 DNS 레코드가 업데이트 됩니다. 구성 요소에 대 한 수집 또는 쿼리 시도는 개인 링크를 통과 하려고 시도 하 고 실패할 수 있습니다. 마찬가지로, 단일 작업 영역에 대 한 개인 링크를 설정 하면 모든 Log Analytics 쿼리가 전용 링크 쿼리 끝점 (작업 영역 특정 끝점이 있는 수집 요청은 제외)을 통해 이동 합니다.

![단일 VNet의 DNS 재정의 다이어그램](./media/private-link-security/dns-overrides-single-vnet.png)

이는 특정 VNet 뿐만 아니라 동일한 DNS 서버를 공유 하는 모든 Vnet에 적용 됩니다 ( [dns 재정의 문제](#the-issue-of-dns-overrides)참조). 따라서 예를 들어 Application Insights 구성 요소에 대 한 로그 수집 요청은 항상 개인 링크 경로를 통해 전송 됩니다. AMPLS에 연결 되지 않은 구성 요소는 개인 링크 유효성 검사에 실패 하 여 이동 하지 않습니다.

**즉, 네트워크의 모든 Azure Monitor 리소스를 개인 링크 (AMPLS에 추가)에 연결 하거나, 네트워크에 없는 모든 리소스를 개인 링크에 연결 해야 합니다.**

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor 개인 링크가 전체 네트워크에 적용 됩니다.
일부 네트워크는 여러 Vnet 구성 됩니다. 이러한 Vnet는 동일한 DNS 서버를 사용 하는 경우 서로 다른 DNS 매핑을 재정의 하 고 Azure Monitor와의 서로 다른 통신을 중단 합니다 ( [DNS 재정의 문제](#the-issue-of-dns-overrides)참조). 궁극적으로, DNS는 Azure Monitor 끝점을이 Vnet 범위 (다른 Vnet에서 연결할 수 없음)의 개인 Ip에 매핑하기 때문에 Azure Monitor와 통신할 수 있습니다.

![여러 Vnet의 DNS 재정의 다이어그램](./media/private-link-security/dns-overrides-multiple-vnets.png)

위의 다이어그램에서 VNet 10.0.1는 먼저 AMPLS1에 연결 하 고 Azure Monitor 전역 끝점을 해당 범위에서 Ip에 매핑합니다. 나중에 VNet 10.0.2는 AMPLS2에 연결 하 고 범위에서 Ip를 사용 하 여 *동일한 글로벌 끝점* 의 DNS 매핑을 재정의 합니다. 이러한 Vnet는 피어 링 되지 않으므로, 첫 번째 VNet은 이제 이러한 끝점에 도달 하지 못합니다.

**동일한 DNS를 사용 하는 Vnet는 직접 또는 허브 VNet을 통해 피어 링 해야 합니다. 피어 링 되지 않는 Vnet는 다른 DNS 서버, DNS 전달자 또는 다른 메커니즘을 사용 하 여 DNS 충돌 방지를 방지 해야 합니다.**

### <a name="hub-spoke-networks"></a>허브-스포크 네트워크
허브-스포크 토폴로지는 각 VNet에 대해 개별적으로 개인 링크를 설정 하는 대신 허브 (주) VNet에서 개인 링크를 설정 하 여 DNS 재정의 문제를 방지할 수 있습니다. 특히 스포크 Vnet에서 사용 하는 Azure Monitor 리소스를 공유 하는 경우이 설정을 사용 하는 것이 좋습니다. 

![허브 및 스포크-단일 PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> 예를 들어 각 VNet이 제한 된 모니터링 리소스 집합에 액세스할 수 있도록 하는 등의 스포크 Vnet 별도의 개인 링크를 만들 수 있습니다. 이러한 경우에는 각 VNet에 대 한 전용 개인 끝점 및 AMPLS를 만들 수 있지만 DNS 재정의를 방지 하기 위해 동일한 DNS 서버를 공유 하지 않는지 확인 해야 합니다.


### <a name="consider-limits"></a>제한 고려

[제한 및 제한 사항](#restrictions-and-limitations)에 나와 있는 것 처럼 AMPLS 개체는 아래 토폴로지에 따라 몇 가지 제한이 있습니다.
* 각 VNet은 **1 개의** AMPLS 개체에만 연결 합니다.
* AMPLS B는 두 개의 가능한 개인 끝점 연결 중 2 개를 사용 하 여 두 Vnet (VNet2 및 VNet3)의 개인 끝점에 연결 됩니다.
* AMPLS A는 세 개의 50 가능한 Azure Monitor 리소스 연결을 사용 하 여 두 개의 작업 영역 및 하나의 응용 프로그램 정보 구성 요소에 연결 합니다.
* Workspace2는 5 개의 가능한 AMPLS 연결 중 2 가지를 사용 하 여 AMPLS A 및 AMPLS B에 연결 합니다.

![AMPLS 제한 다이어그램](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>연결 예

먼저 Azure Monitor Private Link 범위 리소스를 만듭니다.

1. Azure Portal에서 **리소스 만들기** 로 이동하여 **Azure Monitor Private Link 범위** 를 검색합니다.

   ![Azure Monitor 개인 링크 범위 찾기](./media/private-link-security/ampls-find-1c.png)

2. **만들기** 를 선택합니다.
3. 구독 및 리소스 그룹을 선택합니다.
4. AMPLS 이름을 지정합니다. "AppServerProdTelem"와 같이 의미 있고 명확 하지 않은 이름을 사용 하는 것이 가장 좋습니다.
5. **검토 + 만들기** 를 선택합니다. 

   ![Azure Monitor 개인 링크 범위 만들기](./media/private-link-security/ampls-create-1d.png)

6. 유효성 검사를 통과 하도록 한 다음, **만들기** 를 선택 합니다.

### <a name="connect-azure-monitor-resources"></a>Azure Monitor 리소스 연결

Azure Monitor 리소스 (Log Analytics 작업 영역 및 Application Insights 구성 요소)를 AMPLS에 연결 합니다.

1. Azure Monitor 개인 링크 범위에서 왼쪽 메뉴의 **Azure Monitor 리소스** 를 선택 합니다. **추가** 단추를 선택합니다.
2. 작업 영역 또는 구성 요소를 추가합니다. **추가** 단추를 선택 하면 Azure Monitor 리소스를 선택할 수 있는 대화 상자가 나타납니다. 구독 및 리소스 그룹을 찾아보거나 이름을 입력하여 이를 필터링할 수 있습니다. 작업 영역 또는 구성 요소를 선택 하 고 **적용** 을 선택 하 여 범위에 추가 합니다.

    ![범위 선택 UX의 스크린샷](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Monitor 리소스를 삭제 하려면 먼저 연결 된 모든 AMPLS 개체에서 리소스의 연결을 끊어야 합니다. AMPLS에 연결 된 리소스는 삭제할 수 없습니다.

### <a name="connect-to-a-private-endpoint"></a>프라이빗 엔드포인트에 연결

이제 AMPLS에 연결된 리소스가 있으므로 네트워크를 연결 하는 프라이빗 엔드포인트를 만듭니다. 다음 예에서와 같이 이 작업은 [Azure Portal Private Link 센터](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) 또는 Azure Monitor Private Link 범위 내에서 수행할 수 있습니다.

1. 범위 리소스의 왼쪽 리소스 메뉴에서 **개인 끝점 연결** 을 선택 합니다. 끝점 만들기 프로세스를 시작 하려면 **개인 끝점** 을 선택 합니다. 개인 링크 센터에서 시작 된 연결을 선택 하 고 **승인** 을 선택 하 여 승인할 수도 있습니다.

    ![프라이빗 엔드포인트 연결 UX의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 구독, 리소스 그룹, 엔드포인트 이름 및 거주하는 지역을 선택합니다. 지역은 연결할 가상 네트워크와 동일한 지역이어야 합니다.

3. 완료되면 **다음: 리소스** 를 선택합니다. 

4. [리소스] 화면에서

   a. Azure Monitor 프라이빗 범위 리소스가 포함된 **구독** 을 선택합니다. 

   b. **리소스 종류** 에 대해 **Microsoft.insights/privateLinkScopes** 를 선택합니다. 

   다. **리소스** 드롭다운에서 이전에 만든 Private Link 범위를 선택합니다. 

   d. **다음: 구성 >** 를 선택 합니다.
      ![프라이빗 엔드포인트 만들기 선택의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 구성 창에서

   a.    Azure Monitor 리소스에 연결하려는 **가상 네트워크** 및 **서브넷** 을 선택합니다. 
 
   b.    **프라이빗 DNS 영역과 통합** 에 대해 **예** 를 선택하고, 새 프라이빗 DNS 영역을 자동으로 만들도록 합니다. 실제 DNS 영역은 아래 스크린샷에 표시 된 것과 다를 수 있습니다. 
   > [!NOTE]
   > **아니요** 를 선택 하 고 DNS 레코드를 수동으로 관리 하려면 먼저 개인 링크 설정 (이 개인 끝점 및 AMPLS 구성 포함)을 완료 합니다. 그런 다음 [Azure 프라이빗 엔드포인트 DNS 구성](../../private-link/private-endpoint-dns.md)의 지침에 따라 DNS를 구성합니다. Private Link 설정을 위한 준비로 빈 레코드를 만들지 않도록 합니다. 만드는 DNS 레코드는 기존 설정을 재정의하고 Azure Monitor와의 연결에 영향을 줄 수 있습니다.
 
   c.    **검토 + 만들기** 를 선택합니다.
 
   d.    유효성 검사를 통과하도록 합니다. 
 
   e.    **만들기** 를 선택합니다. 

    ![프라이빗 엔드포인트 2 만들기 선택의 스크린샷](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

이제이 AMPLS에 연결 된 새 개인 끝점을 만들었습니다.

## <a name="configure-log-analytics"></a>Log Analytics 구성

Azure Portal로 이동합니다. Log Analytics 작업 영역 리소스 메뉴에는 왼쪽에 **네트워크 격리** 라는 항목이 있습니다. 이 메뉴에서 별도의 두 가지 상태를 제어할 수 있습니다.

![LA 네트워크 격리](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>연결 된 Azure Monitor 개인 링크 범위
이 작업 영역에 연결 된 모든 범위가이 화면에 표시 됩니다. 범위 (AMPLSs)에 연결 하면 각 AMPLS에 연결 된 가상 네트워크의 네트워크 트래픽이이 작업 영역에 연결 될 수 있습니다. 여기를 통해 연결을 만들면 [Azure Monitor 리소스를 연결할](#connect-azure-monitor-resources)때와 같이 범위에서 설정 하는 것과 동일한 효과가 있습니다. 새 연결을 추가 하려면 **추가** 를 선택 하 고 Azure Monitor 개인 링크 범위를 선택 합니다. **적용** 을 선택 하 여 연결 합니다. [제한 및 제한](#restrictions-and-limitations)사항에 설명 된 대로 작업 영역은 5 개의 AMPLS 개체에 연결할 수 있습니다. 

### <a name="access-from-outside-of-private-links-scopes"></a>개인 링크 범위 외부에서 액세스
이 페이지의 맨 아래에 있는 설정은 공용 네트워크에서의 액세스를 제어 합니다. 즉, 위에 나열 된 범위를 통해 연결 되지 않은 네트워크를 의미 합니다. ' 수집 **에 대 한 공용 네트워크 액세스 허용** **'을 설정** 하면 연결 된 범위를 벗어난 컴퓨터에서 로그를 수집 하는 것을 차단 합니다. **쿼리에서 공용 네트워크 액세스 허용을 사용** **하지 않도록** 설정 하면 범위 외부의 컴퓨터에서 오는 쿼리가 차단 됩니다. 여기에는 통합 문서, 대시보드, API 기반 클라이언트 환경, Azure Portal의 통찰력 등을 통해 실행 되는 쿼리가 포함 됩니다. Azure Portal 외부에서 실행 되는 환경 및 Log Analytics 데이터 쿼리도 개인 연결 VNET 내에서 실행 되어야 합니다.

### <a name="exceptions"></a>예외
위에서 설명한 대로 액세스를 제한 하는 것은 Azure Resource Manager에는 적용 되지 않으므로 다음과 같은 제한 사항이 있습니다.
* 데이터에 대 한 액세스-공용 네트워크에서 쿼리를 차단/허용 하는 것이 대부분의 Log Analytics 환경에 적용 되는 반면, 일부는 Azure Resource Manager를 통해 쿼리 데이터를 경험 하므로 리소스 관리자에도 개인 링크 설정이 적용 되지 않는 한 데이터를 쿼리할 수 없습니다. (기능이 곧 제공 될 예정입니다.) 여기에는 Azure Monitor 솔루션, 통합 문서, 통찰력 및 LogicApp 커넥터가 포함 됩니다.
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

Azure Portal로 이동합니다. Azure Monitor Application Insights 구성 요소 리소스에서는 왼쪽에 있는 메뉴 항목 **네트워크 격리** 입니다. 이 메뉴에서 별도의 두 가지 상태를 제어할 수 있습니다.

![AI 네트워크 격리](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

먼저, 액세스할 수 있는 Azure Monitor Private Link 범위에 이 Application Insights 리소스를 연결할 수 있습니다. **추가** 를 선택 하 고 **Azure Monitor 개인 링크 범위** 를 선택 합니다. 적용을 선택 하 여 연결 합니다. 이 화면에 연결된 모든 범위가 표시됩니다. 이 연결을 설정 하면 연결 된 가상 네트워크의 네트워크 트래픽이이 구성 요소에 연결 될 수 있으며, [Azure Monitor 리소스를 연결할](#connect-azure-monitor-resources)때와 마찬가지로 범위에서 연결 하는 것과 동일한 효과가 있습니다. 

다음으로, 이전에 나열된 프라이빗 링크 범위 외부에서 이 리소스에 도달하는 방법을 제어할 수 있습니다. **수집을 위해 공용 네트워크 액세스 허용** 을 **아니요** 로 설정하면 연결된 범위 외부의 머신 또는 SDK에서 데이터를 이 구성 요소에 업로드할 수 없습니다. **쿼리를 위해 공용 네트워크 액세스 허용** 을 **아니요** 로 설정하면 범위 외부의 머신에서 이 Application Insights 리소스의 데이터에 액세스할 수 없습니다. 이 데이터에는 APM 로그, 메트릭 및 라이브 메트릭 스트림에 대한 액세스뿐만 아니라 통합 문서, 대시보드, 쿼리 API 기반 클라이언트 환경, Azure Portal의 인사이트 등을 기반으로 하는 환경도 포함됩니다. 

포털이 아닌 사용 환경도 모니터링되는 워크로드를 포함하는 프라이빗 연결 VNET 내에서 실행되어야 합니다. 

모니터링되는 워크로드를 호스팅하는 리소스를 프라이빗 링크에 추가해야 합니다. App Services에서 이 작업을 수행하는 방법을 보여 주는 [설명서](../../app-service/networking/private-endpoint.md)가 있습니다.

액세스를 이 방법으로 제한하는 경우 Application Insights 리소스의 데이터에만 적용됩니다. 이러한 액세스 설정의 켜기 또는 끄기를 포함한 구성 변경은 Azure Resource Manager에서 관리합니다. 대신 적절한 역할, 권한, 네트워크 제어 및 감사를 사용하여 Resource Manager에 대한 액세스를 제한합니다. 자세한 내용은 [Azure Monitor 역할, 권한 및 보안](roles-permissions-security.md)을 참조하세요.

> [!NOTE]
> 작업 영역 기반 Application Insights를 완벽하게 보호하려면 Application Insights 리소스 및 기본 Log Analytics 작업 영역에 대한 액세스를 모두 잠가야 합니다.
>
> 코드 수준 진단 (프로파일러/디버거)에서는 개인 링크를 지원 하기 위해 고유한 저장소 계정을 제공 해야 합니다. 이 작업을 수행 하는 방법에 대 한 [설명서](../app/profiler-bring-your-own-storage.md) 는 다음과 같습니다.

## <a name="use-apis-and-command-line"></a>API 및 명령줄 사용

Azure Resource Manager 템플릿, REST 및 명령줄 인터페이스를 사용 하 여 앞에서 설명한 프로세스를 자동화할 수 있습니다.

개인 링크 범위를 만들고 관리 하려면 [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) 또는 Azure CLI를 사용 합니다 [(az monitor private 링크 범위)](/cli/azure/monitor/private-link-scope).

네트워크 액세스를 관리하려면 [Log Analytics 작업 영역](/cli/azure/monitor/log-analytics/workspace) 또는 [Application Insights 구성 요소](/cli/azure/ext/application-insights/monitor/app-insights/component)에서 `[--ingestion-access {Disabled, Enabled}]` 및 `[--query-access {Disabled, Enabled}]` 플래그를 사용합니다.

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>사용자 지정 로그 및 IIS 로그를 통해 개인 링크 수집

스토리지 계정은 사용자 지정 로그 수집 프로세스에서 사용됩니다. 기본적으로 서비스 관리형 스토리지 계정이 사용됩니다. 그러나 프라이빗 링크에서 사용자 지정 로그를 수집하려면 사용자 고유의 스토리지 계정을 사용하여 Log Analytics 작업 영역에 연결해야 합니다. [명령줄](/cli/azure/monitor/log-analytics/workspace/linked-storage)을 사용하여 이러한 계정을 설정하는 방법에 대한 자세한 내용을 참조하세요.

사용자 고유의 스토리지 계정을 가져오는 방법에 대한 자세한 내용은 [로그 수집에 고객이 소유한 스토리지 계정 사용](private-storage.md)을 참조하세요.

## <a name="restrictions-and-limitations"></a>제한 사항

### <a name="ampls"></a>AMPLS
AMPLS 개체에는 개인 링크 설정을 계획할 때 고려해 야 할 몇 가지 제한 사항이 있습니다.

* VNet은 1 개의 AMPLS 개체에만 연결할 수 있습니다. 즉, AMPLS 개체는 VNet이 액세스할 수 있어야 하는 모든 Azure Monitor 리소스에 대 한 액세스를 제공 해야 합니다.
* Azure Monitor 리소스 (작업 영역 또는 Application Insights 구성 요소)는 AMPLSs 5 개에 연결할 수 있습니다.
* AMPLS 개체는 50 Azure Monitor 리소스에 연결할 수 있습니다.
* AMPLS 개체는 최대 10 개의 개인 끝점에 연결할 수 있습니다.

이러한 한도를 자세히 검토 하 고 그에 따라 개인 링크를 설정 하는 방법에 대 한 자세한 내용은 [제한 사항을](#consider-limits) 참조 하세요.

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

Application Insights 및 Log Analytics와 같은 Azure Monitor 포털 환경을 사용하려면 개인 네트워크에서 Azure Portal 및 Azure Monitor 확장에 액세스할 수 있어야 합니다. **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor** 및 **AzureFrontDoor** [서비스 태그](../../firewall/service-tags.md) 를 네트워크 보안 그룹에 추가 합니다.

### <a name="programmatic-access"></a>프로그래밍 방식 액세스

개인 네트워크의 Azure Monitor에서 REST API, [CLI](/cli/azure/monitor) 또는 PowerShell을 사용하려면 **AzureActiveDirectory** 및 **AzureResourceManager** [서비스 태그](../../virtual-network/service-tags-overview.md)를 방화벽에 추가합니다.

이러한 태그를 추가하면 로그 데이터 쿼리, Log Analytics 작업 영역과 AI 구성 요소의 만들기 및 관리와 같은 작업을 수행할 수 있습니다.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>콘텐츠 배달 네트워크에서 Application Insights SDK 다운로드

브라우저에서 CDN으로부터 코드를 다운로드하려고 시도하지 않도록 스크립트에서 JavaScript 코드를 번들로 묶습니다. 예제는 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)에서 제공됩니다.

### <a name="browser-dns-settings"></a>브라우저 DNS 설정

개인 링크를 통해 Azure Monitor 리소스에 연결 하는 경우 이러한 리소스에 대 한 트래픽은 네트워크에 구성 된 개인 끝점을 통해 이동 해야 합니다. 개인 끝점을 사용 하도록 설정 하려면 [개인 끝점에 연결](#connect-to-a-private-endpoint)에 설명 된 대로 DNS 설정을 업데이트 합니다. 일부 브라우저는 사용자가 설정 하는 대신 자체 DNS 설정을 사용 합니다. 브라우저는 Azure Monitor 공용 끝점에 연결을 시도 하 고 개인 링크를 완전히 우회할 수 있습니다. 브라우저 설정이 이전 DNS 설정을 재정의 하거나 캐시 하지 않는지 확인 합니다. 

## <a name="next-steps"></a>다음 단계

- [개인 저장소](private-storage.md) 에 대 한 자세한 정보