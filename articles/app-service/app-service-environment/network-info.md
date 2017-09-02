---
title: "Azure App Service Environment에 대한 네트워킹 고려 사항"
description: "ASE 네트워크 트래픽 및 ASE를 사용하여 NSG 및 UDR을 설정하는 방법을 설명합니다."
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: cd498198e0f206ddca2e3396813b2f2093ec3731
ms.contentlocale: ko-kr
ms.lasthandoff: 08/19/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment에 대한 네트워킹 고려 사항 #

## <a name="overview"></a>개요 ##

 Azure [App Service Environment][Intro]는 Azure App Service를 Azure VNet(Virtual Network) 내 서브넷에 배포한 것입니다. ASE(App Service Environment)에는 두 가지 배포 유형이 있습니다.

- **외부 ASE** - ASE에서 호스트되는 앱을 인터넷 액세스가 가능한 IP 주소에 표시합니다. 자세한 내용은 [외부 ASE 만들기][MakeExternalASE]를 참조하세요.
- **ILB ASE** - ASE에서 호스트되는 앱을 VNet 내부의 IP 주소에 표시합니다. 내부 끝점은 ILB(내부 부하 분산 장치)이므로 ILB ASE라고 합니다. 자세한 내용은 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

현재 App Service Environment에는 두 가지 버전(ASEv1 및 ASEv2)이 있습니다. ASEv1에 대한 자세한 내용은 [App Service Environment v1 소개][ASEv1Intro]를 참조하세요. ASEv1은 클래식 또는 리소스 관리자 VNet에서 배포할 수 있습니다. ASEv2는 리소스 관리자 VNet에만 배포할 수 있습니다.

인터넷으로 이동하는 ASE의 모든 호출은 VNet을 출발하여 ASE에 할당된 VIP로 갑니다. 이 VIP의 공용 IP는 인터넷으로 이동하는 ASE의 모든 호출에 대한 원본 IP입니다. 사용자의 ASE에 있는 앱이 VNet 또는 VPN의 리소스를 호출하는 경우 원본 IP는 ASE에서 사용되는 서브넷의 IP 중 하나가 됩니다. ASE가 VNet 내에 있으므로 추가 구성 없이 VNet 내 리소스에 액세스할 수 있습니다. VNet이 온-프레미스 네트워크에 연결되어 있으면 ASE의 앱도 해당 네트워크의 리소스에 액세스할 수 있습니다. 즉, ASE나 앱을 추가로 구성할 필요가 없습니다.

![외부 ASE][1] 

외부 ASE가 있는 경우에는 공용 VIP가 다음 항목용으로 ASE 앱을 확인하는 끝점으로도 사용됩니다.

* HTTP/S 
* FTP/S 
* 웹 배포
* 원격 디버깅

![ILB ASE][2]

ILB ASE가 있는 경우에는 ILB의 IP 주소가 HTTP/S, FTP/S, 웹 배포 및 원격 디버깅용 끝점입니다.

기본 앱 액세스 포트는 다음과 같습니다.

| 사용 | 원본 | 받는 사람 |
|----------|---------|-------------|
|  HTTP/HTTPS  | 사용자 구성 가능 |  80, 443 |
|  FTP/FTPS    | 사용자 구성 가능 |  21, 990, 10001-10020 |
|  Visual Studio 원격 디버깅  |  사용자 구성 가능 |  4016, 4018, 4020, 4022 |

외부 ASE를 사용하든, ILB ASE를 사용하든 위 표에 나와 있는 포트가 사용됩니다. 외부 ASE를 사용하는 경우 공용 VIP에서 해당 포트에 액세스합니다. ILB ASE를 사용하는 경우에는 ILB에서 해당 포트에 액세스합니다. 포트 443을 잠그면 포털에 표시되는 일부 기능이 영향을 받을 수 있습니다. 자세한 내용은 [포털 종속성](#portaldep)을 참조하세요.

## <a name="ase-dependencies"></a>ASE 종속성 ##

ASE 인바운드 액세스 종속성은 다음과 같습니다.

| 사용 | 원본 | 받는 사람 |
|-----|------|----|
| 관리 | App Service 관리 주소 | ASE 서브넷: 454, 455 |
|  ASE 내부 통신 | ASE 서브넷: 모든 포트 | ASE 서브넷: 모든 포트
|  Azure Load Balancer 인바운드 허용 | Azure Load Balancer | ASE 서브넷: 모든 포트
|  IP 주소가 할당된 앱 | 주소가 할당된 앱 | ASE 서브넷: 모든 포트

인바운드 트래픽은 시스템 모니터링 외에도 ASE의 명령 및 제어를 제공합니다. 이 트래픽이 [ASE 관리 주소][ASEManagement] 문서에 나열되는 원본 IP. 즉, 네트워크 보안 구성이 포트 454 및 455에서 모든 IP로부터의 액세스를 허용해야 합니다.

ASE 서브넷 내에는 내부 구성 요소 통신에 사용된 많은 포트가 있으며 변경할 수 있습니다.  이를 위해서는 ASE 서브넷에 있는 모든 포트를 ASE 서브넷에서 액세스할 수 있어야 합니다. 

Azure Load Balancer 및 ASE 서브넷 간의 통신을 위해서는 최소 포트 454, 455 및 16001이 열려 있어야 합니다. 16001 포트는 부하 분산 장치 및 ASE 사이에 활성 트래픽을 유지하는 데 사용됩니다. ILB ASE를 사용하는 경우 454, 455, 16001 포트로만 트래픽을 잠글 수 있습니다.  외부 ASE를 사용하는 경우 정상적인 앱 액세스 포트를 고려해야 합니다.  주소가 할당된 앱을 사용하는 경우 모든 포트에 대해 열려 있어야 합니다.  특정 앱에 주소를 할당하면 부하 분산 장치는 HTTP 및 HTTPS 트래픽을 ASE로 보내기 전에 알려지지 않은 포트를 사용합니다.

IP 주소가 할당된 앱을 사용하는 경우 앱에 할당된 IP에서 ASE 서브넷으로 트래픽을 허용해야 합니다.

아웃 바운드 액세스의 경우 ASE는 여러 외부 시스템에 종속됩니다. 그러한 시스템 종속성은 DNS 이름으로 정의되며 고정된 IP 주소 집합에 매핑되지 않습니다. 따라서 ASE는 여러 포트를 통해 ASE 서브넷에서 모든 외부 IP에 아웃바운드로 액세스할 수 있어야 합니다. ASE는 다음과 같은 아웃바운드 종속성을 가집니다.

| 사용 | 원본 | 받는 사람 |
|-----|------|----|
| Azure Storage | ASE 서브넷 | table.core.windows.net, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445(445는 ASEv1의 경우에만 필요함) |
| Azure SQL Database | ASE 서브넷 | database.windows.net: 1433, 11000-11999, 14000-14999(자세한 내용은 [SQL Database V12 포트 사용](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md) 참조)|
| Azure 관리 | ASE 서브넷 | management.core.windows.net, management.azure.com: 443 
| SSL 인증서 확인 |  ASE 서브넷            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | ASE 서브넷            |  인터넷: 443
| App Service 관리        | ASE 서브넷            |  인터넷: 443
| Azure DNS                     | ASE 서브넷            |  인터넷: 53
| ASE 내부 통신    | ASE 서브넷: 모든 포트 |  ASE 서브넷: 모든 포트

ASE가 이러한 종속성에 액세스하지 못하는 경우 작동이 중지됩니다. 이러한 작동 중지 상태가 오랜 기간 지속되면 ASE가 일시 중단됩니다.

### <a name="customer-dns"></a>고객 DNS ###

VNet이 고객 정의 DNS 서버로 구성된 경우 테넌트 워크로드가 해당 서버를 사용합니다. ASE는 여전히 관리 목적을 위해 Azure DNS와 통신해야 합니다. 

VNet이 VPN 반대쪽의 고객 DNS로 구성된 경우 ASE가 포함된 서브넷에서 DNS 서버에 연결할 수 있어야 합니다.

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>포털 종속성 ##

ASE의 기능적 종속성 외에 포털 환경과 관련된 몇 가지 추가 항목이 있습니다. Azure Portal의 기능 중 일부는 _SCM 사이트_에 대한 직접 액세스에 의존합니다. Azure App Service의 모든 앱에는 URL이 두 개 있습니다. 첫 번째 URL은 앱에 액세스하는 것입니다. 두 번째 URL은 _Kudu 콘솔_이라고도 하는 SCM 사이트에 액세스하는 것입니다. SCM 사이트를 사용하는 기능은 다음과 같습니다.

-   웹 작업
-   함수
-   Logstream
-   Kudu
-   확장
-   Process Explorer
-   콘솔

ILB ASE를 사용할 때는 VNet 외부에서 인터넷을 통해 SCM 사이트에 액세스할 수 없습니다. 앱이 ILB ASE에서 호스트될 때는 SCM 사이트에 연결할 수 없는 기능이 Azure Portal에서 회색으로 표시됩니다.

SCM 사이트에 종속된 이러한 기능 중 많은 부분은 Kudu 콘솔에서 직접 사용할 수 있습니다. Portal을 사용하는 대신 콘솔에 직접 연결할 수 있습니다. 앱이 ILB ASE에서 호스트되는 경우에는 게시 자격 증명을 사용하여 로그인합니다. ILB ASE에 호스팅된 앱의 SCM 사이트에 액세스하는 URL은 다음 형식으로 표시됩니다. 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

ILB ASE 도메인 이름이 *contoso.net*이고 앱 이름이 *testapp*이면 *testapp.contoso.net*에서 앱에 연결할 수 있습니다. 그리고 앱과 함께 제공되는 SCM 사이트는 *testapp.scm.contoso.net*에서 연결할 수 있습니다.

## <a name="ase-ip-addresses"></a>ASE IP 주소 ##

ASE에는 알고 있어야 할 적지 않은 IP 주소가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- **공용 인바운드 IP 주소**: 외부 ASE의 앱 트래픽 및 외부 ASE와 ILB ASE 둘 다의 관리 트래픽에 사용됩니다.
- **아웃바운드 공용 IP**: VNet에서 시작되는 ASE로부터의 아웃바운드 연결(VPN으로 라우팅되지 않음)의 "시작" IP로 사용됩니다.
- **ILB IP 주소**: ILB ASE를 사용하는 경우 사용됩니다.
- **앱에 할당된 IP 기반 SSL 주소**: 외부 ASE를 사용하며 IP 기반 SSL이 구성되어 있을 때만 사용 가능합니다.

이러한 모든 IP 주소는 ASE UI에서 Azure Portal의 ASEv2에 쉽게 확인할 수 있습니다. ILB ASE를 사용하는 경우 ILB용 IP가 나열됩니다.

![IP 주소][3]

### <a name="app-assigned-ip-addresses"></a>앱에 할당된 IP 주소 ###

외부 ASE를 사용하면 IP 주소를 개별 앱에 할당할 수 있습니다. ILB ASE에서는 IP 주소를 할당할 수 없습니다. 앱용으로 고유한 IP 주소를 구성하는 방법에 대한 자세한 내용은 [Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩](../../app-service-web/app-service-web-tutorial-custom-ssl.md)을 참조하세요.

앱에 고유한 IP 기반 SSL 주소가 있는 경우 ASE는 해당 IP 주소에 매핑하도록 두 개의 포트를 예약합니다. 한 포트는 HTTP 트래픽용이고, 다른 포트는 HTTPS용입니다. 이러한 포트는 IP 주소 섹션의 ASE UI에 나열됩니다. 트래픽은 VIP에서 해당 포트에 연결할 수 있어야 합니다. 그렇지 않으면 앱에 액세스할 수 없습니다. NSG(네트워크 보안 그룹)를 구성할 때는 이 요구 사항을 고려해야 합니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹 ##

[네트워크 보안 그룹][NSGs]은 VNet 내에서 네트워크 액세스를 제어하는 기능을 제공합니다. 포털을 사용할 때는 모든 항목을 거부하는 최저 우선 순위의 암시적 거부 규칙이 있습니다. 허용 규칙은 사용자가 직접 작성합니다.

ASE에서 사용자는 ASE 자체를 호스팅하는 데 사용된 VM에 대한 액세스 권한이 없습니다. Microsoft에서 관리하는 구독을 통해 이러한 VM에 액세스할 수 있습니다. ASE에서 앱에 대한 액세스를 제한하려는 경우 ASE 서브넷에 대해 NSG를 설정합니다. 이 과정에서 ASE 종속성에 유의하세요. 종속성을 차단하면 ASE 작동이 중지됩니다.

NSG는 Azure Portal 또는 PowerShell을 통해 구성할 수 있습니다. 이 문서에서는 Azure Portal의 정보를 제공합니다. **네트워킹** 아래에서 최상위 리소스로 포털에서 NSG를 만들고 관리합니다.

인바운드 및 아웃바운드 요구 사항을 고려할 때, NSG는 이 예제에 나와 있는 NSG와 비슷하게 표시되어야 합니다. VNet 주소 범위는 _192.168.250.0/16_이고 ASE가 있는 서브넷은 _192.168.251.128/25_입니다.

ASE가 작동하기 위한 가장 우선적인 인바운드 요구 사항 두 가지가 이 예제의 목록 맨 위에 나와 있습니다. 이는 ASE 관리를 가능케 하고 ASE가 자체적으로 통신할 수 있도록 합니다. 다른 항목은 모두 테넌트에서 구성할 수 있으며, ASE에서 호스트되는 응용 프로그램에 대한 네트워크 액세스를 관리하는 데 사용할 수 있습니다. 

![인바운드 보안 규칙][4]

기본 규칙을 사용하면 VNet의 IP가 ASE 서브넷과 통신할 수 있습니다. 공용 VIP라고도 하는 부하 분산 장치가 ASE와 통신할 수 있도록 하는 또 다른 기본 규칙도 있습니다. 기본 규칙을 확인하려면 **추가** 아이콘 옆에 있는 **기본 규칙**을 선택합니다. 표시된 NSG 규칙 다음에 기타 모든 항목을 거부하는 규칙을 배치하면 VIP와 ASE 간의 트래픽이 차단됩니다. VNet 내에서 들어오는 트래픽을 차단하려면 인바운드를 허용하는 규칙을 직접 추가합니다. 이때 대상이 **Any**이고 포트 범위가 **\***인 AzureLoadBalancer와 같은 원본을 사용합니다. NSG 규칙은 ASE 서브넷에 적용되므로 대상을 구체적으로 지정할 필요는 없습니다.

앱에 IP 주소를 할당한 경우 포트를 열어 두어야 합니다. 포트를 확인하려면 **App Service Environment** > **IP 주소**를 선택합니다.  

다음 아웃바운드 규칙에 표시된 모든 항목이 필요합니다(마지막 항목은 제외). 이러한 항목을 통해 네트워크에서 이 문서 앞부분에서 언급했던 ASE 종속성에 액세스할 수 있습니다. 이러한 항목 중 하나라도 차단하면 ASE의 작동이 중지됩니다. 목록의 마지막 항목은 사용자의 ASE가 VNet의 다른 리소스와 통신할 수 있도록 설정합니다.

![아웃바운드 보안 규칙][5]

NSG를 정의한 후 ASE가 있는 서브넷에 할당합니다. ASE VNet 또는 서브넷을 기억하지 못하는 경우 ASE 관리 포털에서 확인할 수 있습니다. 서브넷에 NSG를 할당하려면 서브넷 UI로 이동한 다음 NSG를 선택합니다.

## <a name="routes"></a>경로 ##

경로 문제는 Azure ExpressRoute를 사용하여 VNet을 구성할 때 가장 흔히 발생합니다. VNet에는 세 가지 유형의 경로가 있습니다.

-   시스템 경로
-   BGP 경로
-   UDR(사용자 정의 경로)

BGP 경로는 시스템 경로를 재정의합니다. UDR은 BGP 경로를 재정의합니다. Azure Virtual Networks의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요][UDRs]를 참조하세요.

ASE가 시스템을 관리하는 데 사용하는 Azure SQL Database에는 방화벽이 있습니다. 이 방화벽은 통신이 ASE 공용 VIP에서 시작되어야 하도록 요구합니다. ASE에서 SQL Database로의 연결은 ExpressRoute 연결을 통해 다른 IP 주소로 전송되는 경우 거부됩니다.

들어오는 관리 요청에 대한 회신이 ExpressRoute로 전송되는 경우에는 회신 주소가 원래 대상과 달라집니다. 이러한 주소 불일치로 인해 TCP 통신이 중단됩니다.

VNet이 ExpressRoute로 구성된 상태로 ASE가 작동하도록 하기 위해 수행할 수 있는 가장 쉬운 방법은 다음과 같습니다.

-   _0.0.0.0/0_을 보급하도록 ExpressRoute를 구성합니다. 기본적으로 ExpressRoute는 온-프레미스의 모든 아웃바운드 트래픽을 강제로 터널링합니다.
-   UDR를 만듭니다. 그리고 주소 접두사가 _0.0.0.0/0_이며 다음 홉 형식이 _인터넷_인 ASE가 포함된 서브넷에 UDR을 적용합니다.

이러한 두 가지 사항을 변경하면 ASE 서브넷에서 발생하는 인터넷용 트래픽이 ExpressRoute로 강제 전송되지 않으며 ASE가 정상적으로 작동합니다. 

> [!IMPORTANT]
> UDR에 정의된 경로는 ExpressRoute 구성을 통해 보급된 경로보다 우선하도록 충분히 구체적이어야 합니다. 이전 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용합니다. 따라서 더 구체적인 주소 범위를 사용하는 경로 보급 알림으로 인해 주소 범위가 잘못 재정의될 가능성이 있습니다.
>

공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 수행하는 ExpressRoute 구성에서는 ASE가 지원되지 않습니다. 공용 피어링이 구성된 ExpressRoute 구성은 Microsoft에서 경로 보급 알림을 받습니다. 보급 알림에는 대규모 Microsoft Azure IP 주소 범위 집합이 포함되어 있습니다. 개인 피어링 경로에서 주소 범위를 교차 보급하는 경우 ASE 서브넷의 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라로 강제 터널링됩니다. 이 네트워크 흐름은 현재 ASE에서 지원되지 않습니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로의 교차 보급 경로를 중지합니다.

UDR를 만들려면 다음 단계를 수행합니다.

1. Azure Portal로 이동합니다. **네트워킹** > **경로 테이블**을 선택합니다.

2. VNet과 동일한 지역에 새 경로 테이블을 만듭니다.

3. 경로 테이블 UI 내에서 **경로** > **추가**를 선택합니다.

4. **다음 홉 형식**을 **인터넷**으로, **주소 접두사**를 **0.0.0.0/0**으로 설정합니다. **저장**을 선택합니다.

    다음과 유사한 출력이 표시됩니다.

    ![기능 경로][6]

5. 새 경로 테이블을 만든 후 ASE가 포함된 서브넷으로 이동합니다. Portal의 목록에서 경로 테이블을 선택합니다. 변경 내용을 저장하면 서브넷이 표시된 경로와 NSG를 확인할 수 있습니다.

    ![NSG 및 경로][7]

### <a name="deploy-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>ExpressRoute와 통합된 기존 Azure Virtual Networks에 배포 ###

ExpressRoute와 통합된 VNet에 ASE를 배포하려면 ASE를 배포할 서브넷을 미리 구성합니다. 그런 다음 리소스 관리자 템플릿을 사용하여 ASE를 배포합니다. ASE를 이미 구성된 ExpressRoute가 있는 VNet에 만들려면 다음을 수행합니다.

- ASE를 호스팅하는 서브넷을 만듭니다.

    > [!NOTE]
    > ASE 이외의 항목을 서브넷에 포함해서는 안 됩니다. 향후 확장이 가능한 주소 공간을 선택해야 합니다. 이 설정은 나중에 변경할 수 없습니다. 주소 128개를 포함할 수 있는 `/25` 크기를 사용하는 것이 좋습니다.

- 앞에서 설명한 대로 UDR(예: 경로 테이블)을 만들고 서브넷에서 UDR을 설정합니다.
- [리소스 관리자 템플릿을 사용하여 ASE 만들기][MakeASEfromTemplate]에서 설명한 대로 리소스 관리자 템플릿을 사용하여 ASE를 만듭니다.

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md

