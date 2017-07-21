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
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d19590c23da43e08ea42cd278347e01d87433a58
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment에 대한 네트워킹 고려 사항 #

## <a name="overview"></a>개요 ##

[ASE(App Service Environment)][Intro]는 Azure App Service를 사용자의 Azure VNet(Virtual Network)에 있는 서브넷에 배포한 것입니다.  ASE에 대한 두 가지 배포 형식이 있습니다.

- _외부 ASE_ - 인터넷 액세스가 가능한 IP 주소에 앱이 호스트된 ASE를 노출합니다. 자세한 내용은 [외부 ASE 만들기][MakeExternalASE]를 참조하세요.
- _ILB ASE_ - 사용자의 Azure VNet(Virtual Network) 내부 IP 주소에 앱이 호스트된 ASE를 노출합니다.  내부 끝점은 ILB(내부 부하 분산 장치)입니다. ILB ASE라고 불리는 이유이기도 합니다. 자세한 내용은 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

현재 ASE의 버전에는 두 가지가 있습니다. ASE의 초기 버전은 ASEv1, 최신 버전은 ASEv2라고 합니다. ASEv1에 대한 자세한 내용은 [App Service Environment v1 소개][ASEv1Intro]를 참조하세요. ASEv1은 클래식 또는 리소스 관리자 VNet에 배포될 수 있습니다. ASEv2는 리소스 관리자 VNet에만 배포될 수 있습니다.

인터넷으로 이동하는 ASE의 모든 호출은 VNet을 출발하여 ASE에 할당된 VIP로 갑니다. 이 VIP의 공용 IP는 인터넷으로 이동하는 ASE의 모든 호출에 대한 원본 IP입니다.  사용자의 ASE에 있는 앱이 VNet 또는 VPN의 리소스에 대한 호출을 만드는 경우 원본 IP는 사용자의 ASE에서 사용되는 서브넷의 IP 중 하나가 됩니다.  ASE가 VNet 내에 있으므로 추가 구성 없이 VNet 내 리소스에 액세스할 수 있습니다. VNet이 사용자의 온-프레미스 네트워크에 연결된 경우 ASE 또는 사용자 앱의 추가 구성 없이 리소스에 액세스할 수 있습니다.

![][1] 

외부 ASE가 있는 경우 해당 공용 VIP는 사용자의 ASE 앱이 HTTP/S, FTP/S, 웹 배포 및 원격 디버깅을 해결하게 되는 끝점입니다.

![][2]

ILB ASE가 있는 경우 ILB의 IP 주소는 HTTP/S, FTP/S, 웹 배포 및 원격 디버깅에 대한 끝점입니다.

기본 앱 액세스 포트는 다음과 같습니다.

| 사용 | 원본 | 받는 사람 |
|----------|---------|-------------|
|  HTTP/HTTPS  | 사용자 구성 가능 |  80, 443 |
|  FTP/FTPS    | 사용자 구성 가능 |  21, 990, 10001-10020 |
|  Visual Studio 원격 디버깅  |  사용자 구성 가능 |  4016, 4018, 4020, 4022 |

외부 ASE 또는 ILB ASE에 있는 경우 true입니다. 외부 ASE에 있는 경우 공용 VIP에 적중하는 포트입니다. ILB ASE에 있는 경우 ILB에 있는 해당 포트를 적중할 수 있습니다. 포트 443을 잠근 경우 포털에 노출되는 일부 기능에 영향을 미칠 수 있다는 점에 유의해야 합니다. 자세한 내용은 [포털 종속성](#portaldep)을 참조하세요.

## <a name="ase-dependencies"></a>ASE 종속성 ##

ASE 인바운드 액세스 종속성은 다음과 같습니다.

| 사용 | 원본 | 받는 사람 |
|-----|------|----|
| 관리 | 인터넷 | ASE 서브넷: 454, 455 |
|  ASE 내부 통신 | ASE 서브넷: 모든 포트 | ASE 서브넷: 모든 포트
|  Azure Load Balancer 인바운드 허용 | Azure Load Balancer | 모두

인바운드 트래픽은 시스템 모니터링 외에도 ASE의 명령 및 제어를 제공합니다. 이 트래픽에 대한 원본 IP는 일정하지 않습니다. 즉, 네트워크 보안 구성에서 포트 454 및 455의 모든 IP에서의 액세스를 허용해야 함을 의미합니다.

아웃 바운드 액세스의 경우 ASE는 여러 외부 시스템에 종속됩니다. 그러한 시스템 종속성은 DNS 이름으로 정의되며 고정된 IP 주소 집합에 매핑되지 않습니다. 즉, ASE는 ASE 서브넷부터 다양한 포트의 모든 외부 IP에서의 아웃바운드 액세스가 필요함을 의미합니다. ASE는 다음과 같은 아웃바운드 종속성을 가집니다.

| 사용 | 원본 | 받는 사람 |
|-----|------|----|
| Azure 저장소 | ASE 서브넷 | table.core.windows.net, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445(445는 ASEv1에만 필요함) |
| SQL 데이터베이스 | ASE 서브넷 | database.windows.net: 1433, 11000-11999, 14000-14999(자세한 내용은 [Sql Database V12 포트 사용](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md) 참조)|
| Azure 관리 | ASE 서브넷 | management.core.windows.net, management.azure.com: 443 
| SSL 인증서 확인 |  ASE 서브넷            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | ASE 서브넷            |  인터넷: 443
| App Service 관리        | ASE 서브넷            |  인터넷: 443
| Azure DNS                     | ASE 서브넷            | 인터넷: 53
| ASE 내부 통신    | ASE 서브넷: 모든 포트 | ASE 서브넷: 모든 포트

ASE가 이러한 종속성에 액세스하지 못하는 경우 작동이 중지됩니다. 오랜 기간 지속되면 ASE가 일시 중단됩니다.

**고객 DNS**

VNet이 고객 정의 DNS 서버로 구성된 경우 테넌트 워크로드가 이를 사용합니다. ASE는 여전히 관리 목적을 위해 Azure DNS와 통신해야 합니다. 

VNet이 VPN의 다른 쪽에서 고객 DNS로 구성된 경우 DNS 서버는 ASE를 포함하는 서브넷에서 도달할 수 있어야 합니다.

<a name="portaldep"></a>
## <a name="portal-dependencies"></a>포털 종속성 ##

ASE가 갖는 기능적 종속성 외에 포털 환경과 관련된 몇 가지 추가 항목이 있습니다. Azure Portal의 기능 중 일부는 _SCM 사이트_에 대한 직접 액세스에 의존합니다. Azure App Service의 모든 앱의 경우 두 개의 URL이 있습니다. 첫 번째 URL은 앱에 액세스하는 것입니다. 두 번째 URL은 _Kudu 콘솔_이라고도 하는 SCM 사이트에 액세스하는 것입니다. SCM 사이트를 사용하는 기능 중 일부는 다음을 포함합니다.

-   웹 작업
-   함수
-   Logstream
-   Kudu
-   확장
-   Process Explorer
-   콘솔

이것은 ILB ASE를 사용할 때 가장 어려운 문제입니다. SCM 사이트는 VNet 외부에서는 인터넷을 통해서 액세스할 수 없기 때문입니다. 이러한 이유로 SCM 사이트에 종속된 해당 기능은 사용자의 앱이 ILB ASE에 호스팅된 경우 Azure Portal에서 회색으로 표시됩니다.

SCM 사이트에 종속된 이러한 기능 중 많은 부분은 Kudu 콘솔에서 직접 사용할 수 있습니다. 포털에서 사용하는 대신 직접 연결할 수 있습니다. 앱이 ILB ASE에 호스팅된 경우 게시 자격 증명을 사용하여 로그인해야 합니다. ILB ASE에 호스팅된 앱의 SCM 사이트에 액세스하는 URL은 다음 형식으로 표시됩니다. 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

따라서 ILB ASE가 도메인 이름이 *contoso.net*이고, 앱 이름이 *testapp*이면, 내 앱은 *testapp.contoso.net*에서 연결할 수 있고 함께 이동하는 SCM 사이트는 *testapp.scm.contoso.net*에서 연결할 수 있게 됩니다.

## <a name="ase-ip-addresses"></a>ASE IP 주소 ##

ASE에는 알고 있어야 할 적지 않은 IP 주소가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- 공용 인바운드 IP 주소 - 외부 ASE의 앱 트래픽 및 외부 ASE 및 ILB ASE 모두의 관리 트래픽에 사용됩니다.
- 아웃 바운드 공용 IP - VNet에서 출발한 ASE로부터의 아웃바운드 연결에서 “보낸 사람” IP로 사용되며, VPN으로 라우팅되지 않습니다.
- ILB IP 주소 - ILB ASE를 사용하는 경우에 해당합니다.
- IP 기반 SSL 주소가 할당된 앱 - 외부 ASE 및 IP 기반 SSL이 구성된 경우에만 가능합니다.

이러한 모든 IP 주소는 ASE UI에서 Azure Portal의 ASEv2에 쉽게 확인할 수 있습니다. ILB ASE를 사용하는 경우 ILB에 대한 IP가 나열됩니다.

![][3]

**IP 주소가 할당된 앱**

외부 ASE를 사용하면 IP 주소를 개별 앱에 할당할 수 있습니다. ILB ASE로는 수행할 수 없습니다. 앱에 고유한 IP 주소를 구성하는 방법에 대한 자세한 내용은 [Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩](../../app-service-web/app-service-web-tutorial-custom-ssl.md)을 참조하세요.

앱에 고유한 IP 기반 SSL 주소가 있는 경우 ASE는 해당 IP 주소에 매핑하도록 두 개의 포트를 예약합니다. 한 포트는 HTTP 트래픽용이고, 다른 하나는 HTTPS용입니다. 이러한 포트는 IP 주소 섹션의 ASE UI에 나열됩니다. 트래픽은 VIP에서 해당 포트에 연결할 수 있어야 합니다. 그렇지 않으면 앱에 액세스할 수 없습니다. 이것은 네트워크 보안 그룹을 구성할 때 명심해야 할 중요한 점입니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹 ##

[NSG(네트워크 보안 그룹)][NSGs]는 VNet 내에서 네트워크 액세스를 제어하는 기능을 제공합니다. 포털을 사용할 때 모든 항목을 거부하도록 암시적 거부 규칙이 가장 낮은 우선 순위에 있으므로 사용자가 빌드하는 것이 사용자의 허용 규칙입니다.

ASE에서 사용자는 ASE 자체를 호스팅하는 데 사용된 VM에 대한 액세스 권한이 없습니다. 관리되는 Microsoft 구독에 있습니다. ASE에서 앱에 대한 액세스를 제한하려는 경우 ASE 서브넷에 NSG를 설정해야 합니다. 이 과정에서 ASE 종속성에 유의해야 합니다. 모든 종속성을 차단하면 ASE 작동이 중지됩니다.

NSG는 Azure Portal 또는 PowerShell을 통해 구성할 수 있습니다. 간단하게 하기 위해 여기 나온 정보는 Azure Portal을 보여 줍니다. **네트워킹** 아래에서 최상위 리소스로 포털에서 NSG를 만들고 관리합니다.

인바운드 및 아웃바운드 요구 사항을 고려할 때, NSG는 아래에 표시되는 것과 비슷해야 합니다. 이 예제에서 VNet 주소 범위는 _192.168.250.0/16_이고 ASE가 있는 서브넷은 _192.168.251.128/25_입니다.

ASE가 작동하기 위한 가장 우선적인 인바운드 요구 사항 두 가지가 이 예제에서 가장 위에 나와 있습니다. 이는 ASE 관리를 가능케 하고 ASE가 자체적으로 통신할 수 있도록 합니다. 다른 항목은 구성 가능한 모든 테넌트이며, ASE 호스트 응용 프로그램에 대한 네트워크 액세스를 제어할 수 있습니다.   

![][4]

VNet의 IP를 사용하도록 설정하여 ASE 서브넷에 연결하는 기본 규칙과 더불어, ASE와 통신할 수 있는 이른바 공용 VIP인 부하 분산 장치를 사용하도록 설정하는 기본 규칙도 있습니다.  기본 규칙은 *추가* 아이콘 옆에 있는 *기본 규칙*을 클릭하여 확인할 수 있습니다. NSG 규칙이 표시된 후 나머지 모든 규칙을 거부했다면 VIP와 ASE 간의 트래픽을 방지하게 됩니다. VNet 내부에서 들어오는 트래픽을 방지하고 싶은 경우 인바운드가 \*의 모든 대상 및 포트 범위의 AzureLoadBalancer와 같은 원본을 사용하도록 허용하는 자체 규칙을 추가해야 합니다.  NSG 규칙은 ASE 서브넷에만 적용되므로 대상을 구체화할 필요는 없습니다.

사용자의 앱에 IP 주소를 할당한 경우 시작할 때 사용된 포트를 유지해야 합니다. 사용된 포트는 **App Service Environment** > **IP 주소** UI에서 확인할 수 있습니다.  

아래에 설명된 아웃바운드 규칙에 표시된 마지막 항목 외에는 모두 필요합니다. 이 문서의 앞부분에서 언급한 ASE 종속성에 대한 네트워크 액세스를 사용하도록 설정합니다. 그 중 하나를 차단하는 경우 사용자의 ASE 작동이 중지됩니다. 목록의 마지막 항목은 사용자의 ASE가 VNet의 다른 리소스와 통신할 수 있도록 설정합니다.

![][5]

사용자의 NSG를 정의한 후 ASE가 켜져 있는 서브넷에 할당해야 합니다. ASE VNet 또는 서브넷을 기억하지 못하는 경우 ASE 관리 포털에서 확인할 수 있습니다. 서브넷에 NSG를 할당하려면 서브넷 UI로 이동한 다음 NSG를 선택합니다.

## <a name="routes"></a>경로 ##

ExpressRoute를 사용하여 VNet을 구성하는 경우 경로가 가장 일반적으로 문제가 될 수 있습니다. Azure VNet에 세 가지 형식의 루트가 있습니다. 다음과 같습니다.

-   시스템 경로
-   BGP 경로
-   UDR(사용자 정의 경로)

BGP 경로는 시스템 경로를 재정의합니다. UDR은 BGP 경로를 재정의합니다. Azure Virtual Networks의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요][UDRs]를 참조하세요.

ASE가 시스템을 관리하는 데 사용하는 SQL Database에는 허용 목록이 있으며 ASE 공용 VIP에서 발생하는 통신이 필요합니다. 들어오는 관리 요청에 대한 회신이 ExpressRoute로 전송된 경우 회신 주소는 대상과 달라 TCP 통신이 단절됩니다.

따라서 사용자의 VNet이 ExpressRoute로 구성된 상태로 ASE가 작동하기 위해서는 다음을 수행하는 것이 가장 쉬운 방법입니다.

-   ExpressRoute를 _0.0.0.0/0_을 보급하도록 구성하여 기본적으로 모든 아웃바운드 트래픽 온-프레미스를 강제로 터널링합니다.
-   UDR을 만들고 주소 접두사 _0.0.0.0/0_ 및 다음 홉의 형식 _인터넷_을 사용하는 App Service Environment를 포함하는 서브넷에 적용합니다.

이러한 두 가지 사항을 변경하면 ASE 서브넷에서 발생하는 인터넷용 트래픽을 ExpressRoute로 강제로 내려 보내지 않게 되고 ASE는 작동할 수 있게 됩니다. 

> [!IMPORTANT]
> UDR에 정의된 경로는 ExpressRoute 구성을 통해 보급된 경로보다 우선하도록 충분히 구체적이어야 합니다. 이전 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용하고 따라서 잠재적으로 더 구체적인 주소 범위를 사용하는 경로 알림에서 실수로 재정의될 수 있습니다.
>

공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 수행하는 ExpressRoute 구성에서는 App Service Environment가 지원되지 않습니다. 구성된 공용 피어링이 있는 ExpressRoute 구성은 다양한 Microsoft Azure IP 주소 범위 집합에 대해 Microsoft에서 경로 알림을 받습니다. 이러한 개인 피어링 경로에 주소 범위의 교차 보급을 수행한 경우 App Service Environment의 서브넷에서 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라에 강제로 터널링되는 결과를 가져옵니다. 이 네트워크 흐름은 현재 App Service Environment에서 지원되지 않습니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로 이어진 교차 보급 경로를 중지합니다.

설명된 대로 UDR을 만들려면 다음을 수행합니다.

1.  Azure Portal > **네트워킹** > **경로 테이블**로 이동합니다.
2.  VNet과 동일한 지역에 새 경로 테이블을 만듭니다.
3.  사용자의 경로 테이블 UI 내에서 **경로** > **추가**를 선택합니다.
4.  **다음 홉 형식**을 **인터넷**으로, **주소 접두사**를 _0.0.0.0/0_으로 설정한 다음 **저장**을 클릭합니다.

그러면 다음과 같이 표시됩니다.

![][6]

새 경로 테이블을 만든 후 사용자 ASE를 포함하는 서브넷으로 이동합니다. 포털에서 가져온 목록에서 경로 테이블을 선택합니다. 변경 내용을 저장하면 사용자의 서브넷을 알리는 NSG 및 루트가 표시되어야 합니다.

![][7]

### <a name="deploying-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>ExpressRoute와 통합된 기존 Azure Virtual Networks에 배포 ###

사용자의 ASE를 ExpressRoute와 이미 통합되어 있는 VNet에 배포하려는 경우 ASE를 배포할 서브넷을 미리 구성해야 하며, 그런 다음 Resource Manager 템플릿을 사용하여 배포합니다. ASE를 이미 구성된 ExpressRoute가 있는 VNet에 만들려면 다음을 수행합니다.

- ASE를 호스팅하는 서브넷을 만듭니다.

    > [!NOTE]
    > ASE 외에는 서브넷에 있을 수 없으며, 나중에 변경할 수 없기 때문에 향후 성장을 위해 충분히 큰 주소 공간을 선택해야 합니다. 128개 주소를 사용하는 `/25`가 권장되는 크기입니다.
- 앞에서 설명한 대로 UDR(즉, 경로 테이블)을 만들고 서브넷에 설정합니다.
- [ARM 템플릿을 사용하여 ASE 만들기][MakeASEfromTemplate]에서 설명한 대로 Resource Manager 템플릿을 사용하여 ASE를 만듭니다.

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

