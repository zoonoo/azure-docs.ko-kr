---
title: "Azure App Service 하이브리드 연결 | Microsoft Docs"
description: "하이브리드 연결을 만들고 사용하여 서로 다른 네트워크의 리소스에 액세스하는 방법"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service 하이브리드 연결 #

## <a name="overview"></a>개요 ##

하이브리드 연결은 Azure의 서비스인 동시에 Azure App Service의 기능입니다.  서비스로서 하이브리드 연결은 Azure App Service에서 제공되는 것보다 많은 용도와 기능을 포함합니다.  하이브리드 연결 및 Azure App Service 외부에서의 관련 사용법을 자세히 알아보려면 [Azure Relay 하이브리드 연결][HCService]에서 시작합니다.

Azure App Service 내에서 하이브리드 연결은 다른 네트워크의 응용 프로그램 리소스에 액세스하는 데 사용될 수 있습니다. 하이브리드 연결을 통해 앱에서 응용 프로그램 끝점에 액세스할 수 있습니다.  응용 프로그램에 액세스하는 대체 기능으로는 사용되지 않습니다.  App Service에서 사용되는 것처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합에 상호 연결됩니다.  즉, TCP 수신 포트를 대상으로 하는 한, 하이브리드 연결 끝점은 모든 운영 체제 및 모든 응용 프로그램에 있을 수 있습니다. 하이브리드 연결은 응용 프로그램 프로토콜이 무엇인지 또는 사용자가 무엇에 액세스하고 있는지 인식하거나 상관하지 않습니다.  단지 네트워크 액세스를 제공합니다.  


## <a name="how-it-works"></a>작동 방법 ##
하이브리드 연결 기능은 Service Bus Relay에 대한 두 개의 아웃바운드 호출로 구성됩니다.  App Service에서 앱이 실행되고 있는 호스트의 라이브러리에서의 연결과, HCM(하이브리드 연결 관리자)에서 Service Bus Relay로의 연결이 있습니다.  HCM은 네트워크 호스팅 내에서 배포하는 릴레이 서비스입니다. 

앱에는 이 두 개의 결합된 연결을 통해 HCM의 다른 쪽에 고정 호스트:포트 조합에 대한 TCP 터널이 포함됩니다.  연결 시 보안을 위해 TLS 1.2를 사용하고 인증/권한 부여를 위해 SAS 키를 사용합니다.    

![][1]

앱이 구성 하이브리드 연결 끝점과 일치하는 DNS 요청을 실행하면 아웃바운드 TCP 트래픽이 하이브리드 연결 아래로 리디렉션됩니다.  

> [!NOTE]
> 이는 항상 하이브리드 연결에 DNS 이름을 사용해야 함을 의미합니다.  일부 클라이언트 소프트웨어는 끝점에서 IP 주소를 사용하는 경우 DNS 조회를 수행하지 않습니다.
>
>

하이브리드 연결의 유형은 두 가지로, 하나는 Azure Relay 서비스로 제공되는 새로운 하이브리드 연결과 이전 BizTalk 하이브리드 연결이 있습니다.  이전 BizTalk 하이브리드 연결은 포털에서 클래스 하이브리드 연결이라고 합니다.  이 문서의 뒷부분에서 이에 대해 자세히 설명합니다.

### <a name="app-service-hybrid-connection-benefits"></a>App Service 하이브리드 연결의 장점 ###

하이브리드 연결 기능에는 다음과 같은 많은 장점이 있습니다.

- 앱이 온-프레미스 시스템 및 서비스에 안전하게 액세스할 수 있습니다.
- 기능에 인터넷 액세스 가능한 끝점이 필요하지 않습니다.
- 빠르고 쉽게 설정할 수 있습니다.  
- 각 하이브리드 연결이 단일 호스트:포트 조합과 일치하므로 보안 측면에서 우수합니다.
- 연결이 모두 표준 웹 포트를 통해 아웃바운드되므로 일반적으로 방화벽 구멍이 필요하지 않습니다.
- 기능이 네트워크 수준이므로 이는 앱에서 사용되는 언어 및 끝점에서 사용되는 기술과 관계없음을 의미합니다.
- 단일 앱에서 여러 네트워크에 액세스하는 데 사용될 수 있습니다. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>하이브리드 연결로 할 수 없는 작업 ###

하이브리드 연결로 할 수 없는 몇 가지 작업은 다음과 같습니다.

- 드라이브 탑재
- UDP 사용
- FTP 수동 모드 또는 확장 수동 모드 같은 동적 포트를 사용하는 TCP 기반 서비스에 액세스
- LDAP 지원(경우에 따라 UDP가 필요하므로)
- Active Directory 지원

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>앱에서 하이브리드 연결 추가 및 만들기 ##

하이브리드 연결은 앱 포털 또는 하이브리드 연결 서비스 포털을 통해 만들 수 있습니다.  앱에서 사용할 하이브리드 연결을 만드는 데는 앱 포털을 사용하는 것이 좋습니다.  하이브리드 연결을 만들려면 [Azure Portal][portal]로 이동하고 해당 앱의 UI로 이동합니다.  **네트워킹 > 하이브리드 연결 끝점 구성**을 선택합니다.  여기서 앱에서 구성된 하이브리드 연결을 확인할 수 있습니다.  

![][2]

새 하이브리드 연결을 추가하려면 [하이브리드 연결 추가]를 클릭합니다.  열리는 UI에는 이미 만들어진 하이브리드 연결이 나열됩니다.  하이브리드 연결을 하나 이상 앱에 추가하려면 원하는 항목을 클릭하고 **선택한 하이브리드 연결 추가**를 누릅니다.  

![][3]

새 하이브리드 연결을 만들려면 **새 하이브리드 연결 만들기**를 클릭합니다.  여기에서 다음을 지정합니다. 

- 끝점 이름
- 끝점 호스트 이름
- 끝점 포트
- 사용할 Service Bus 네임스페이스

![][4]

모든 하이브리드 연결은 Service Bus 네임스페이스에 연결되고 각 Service Bus 네임스페이스는 Azure 지역에 있습니다.  네트워크에서 발생하는 대기 시간을 피하려면 앱과 같은 지역에서 Service Bus 네임스페이스를 시도하고 사용해야 합니다.

앱에서 하이브리드 연결을 제거하려면 해당 항목을 마우스 오른쪽 단추로 클릭하고 **연결 끊기**를 선택합니다.  

하이브리드 연결이 웹앱에 추가되면 웹앱을 클릭하여 세부 정보를 확인할 수 있습니다.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>하이브리드 연결 및 App Service 계획 ##

현재 만들 수 있는 하이브리드 연결은 새로운 하이브리드 연결뿐입니다.  하이브리드 연결은 기본, 표준, 프리미엄 및 격리 요금제 SKU에서만 사용할 수 있습니다.  요금제에는 연결된 제한이 있습니다.  

| 요금제 | 요금제에서 사용 가능한 하이브리드 연결 수 |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| 격리 | 200 |

App Service 계획 제한 사항이 있으므로 App Service 계획에는 사용 중인 하이브리드 연결 수 및 연결이 사용되는 앱을 보여 주는 UI도 있습니다.  

![][6]

앱 보기와 같이 하이브리드 연결을 클릭하여 세부 정보를 볼 수 있습니다.  여기 표시된 속성에서 앱 보기에 있는 모든 정보를 확인할 수 있지만 같은 App Service 계획에서 해당 하이브리드 연결을 사용 중인 다른 앱 수도 확인할 수 있습니다.

![][7]

App Service 계획에서 사용할 수 있는 하이브리드 연결 끝점 수에는 제한이 있지만 사용되는 각 하이브리드 연결은 해당 App Service 계획의 앱 전체에서 제한 없이 사용할 수 있습니다.  즉, 내 App Service 계획에서 1개의 하이브리드 연결을 5개의 서로 다른 응용 프로그램에 사용했어도 하이브리드 연결 개수는 1입니다.

기본, 표준, 프리미엄 또는 격리 SKU에서만 사용 가능한 개수를 초과한 하이브리드 연결에는 추가 비용이 부과됩니다.  하이브리드 연결 가격 책정에 대한 자세한 내용은 [Service Bus 가격 책정][sbpricing]을 참조하세요.

## <a name="hybrid-connection-manager"></a>하이브리드 연결 관리자 ##

하이브리드 연결이 작동하려면 하이브리드 연결 끝점을 호스트하는 릴레이 에이전트가 네트워크에 있어야 합니다.  이 릴레이 에이전트를 HCM(하이브리드 연결 관리자)이라고 합니다.  이 도구는 [Azure Portal][portal]의 앱에서 사용 가능한 **네트워킹 > 하이브리드 연결 끝점 구성** UI에서 다운로드할 수 있습니다.  

이 도구는 Windows의 Windows Server 2008 R2 이상 버전에서 실행됩니다.  설치된 후 HCM은 서비스로 실행됩니다.  이 서비스는 구성된 끝점을 기준으로 Azure Service Bus 릴레이에 연결됩니다.  HCM의 연결은 포트 80 및 443으로 아웃바운드됩니다.    

HCM에는 구성을 위한 UI가 있습니다.  HCM이 설치된 후 하이브리드 연결 관리자 설치 디렉터리에 있는 HybridConnectionManagerUi.exe를 실행하여 UI를 표시할 수 있습니다.  Windows 10에서는 검색 상자에 *Hybrid Connection Manager UI*를 입력하여 UI에 쉽게 액세스할 수 있습니다.  

HCM UI가 시작되면 가장 먼저 이 HCM 인스턴스로 구성된 하이브리드 연결을 모두 나열한 테이블이 표시됩니다.  변경하려면 Azure를 통해 인증해야 합니다. 

HCM에 하나 이상의 하이브리드 연결을 추가하려면 다음을 수행합니다.

1. HCM UI를 시작합니다.
1. [Configure another hybrid connection](다른 하이브리드 연결 구성)을 클릭합니다. ![][8]

1. Azure 계정으로 로그인합니다.
1. 구독을 선택합니다.
1. 이 HCM이 릴레이할 하이브리드 연결을 클릭합니다. ![][9]

1. 저장을 클릭합니다.

이때 추가한 하이브리드 연결이 표시됩니다.  구성된 하이브리드 연결을 클릭하고 연결에 대한 세부 정보를 확인할 수도 있습니다.

![][10]

HCM에서 구성된 하이브리드 연결을 지원하려면 다음이 필요합니다.

- 포트 80 및 443을 통한 Azure에 대한 TCP 액세스
- 하이브리드 연결 끝점에 대한 TCP 액세스
- 끝점 호스트 및 Azure Service Bus 네임스페이스에서 DNS 조회를 수행할 수 있음

HCM은 이전 BizTalk 하이브리드 연결과 새로운 하이브리드 연결을 둘 다 지원합니다.

### <a name="redundancy"></a>중복 ###

각 HCM은 여러 하이브리드 연결을 지원할 수 있습니다.  특정 하이브리드 연결이 여러 HCM에서 지원될 수도 있습니다.  기본 동작은 특정 끝점에 대해 구성된 HCM에 걸쳐 트래픽을 라운드 로빈 방식으로 순환하는 것입니다.  네트워크에서 하이브리드 연결에 대한 고가용성이 필요한 경우에는 개별 컴퓨터에서 여러 HCM을 인스턴스화하면 됩니다. 

### <a name="manually-adding-a-hybrid-connection"></a>수동으로 하이브리드 연결 추가 ###

구독 외부에서 사용자가 특정 하이브리드 연결에 대한 HCM 인스턴스를 호스트하게 하려면 해당 하이브리드 연결에 대한 게이트웨이 연결 문자열을 공유하면 됩니다.  [Azure Portal][portal]의 하이브리드 연결 속성에서 이 문자열을 확인할 수 있습니다. 이 문자열을 사용하려면 HCM에서 **수동으로 구성** 단추를 클릭하고 게이트웨이 연결 문자열에 붙여넣습니다.


## <a name="troubleshooting"></a>문제 해결 ##

실행 중인 응용 프로그램과의 하이브리드 연결이 설정되고 하나 이상의 HCM에 이 하이브리드 연결이 구성되어 있으면 포털에서 상태가 **연결됨**으로 표시됩니다.  **연결됨**으로 표시되지 않으면 앱이 다운되거나 HCM이 포트 80 또는 443에서 Azure로 연결될 수 없는 것입니다.  

클라이언트가 끝점에 연결할 수 없는 주된 이유는 끝점이 DNS 이름이 아닌 IP 주소를 사용하여 지정되었기 때문입니다.  앱이 원하는 끝점에 도달할 수 없고 IP 주소가 사용되었으면 HCM이 실행 중인 호스트에서 유효한 DNS 이름을 사용하도록 전환합니다.  HCM이 실행 중인 호스트에서 DNS 이름이 제대로 확인되고 HCM이 실행 중인 호스트에서 하이브리드 연결 끝점으로 연결이 설정되어 있는지도 확인해야 합니다.  

App Service에는 콘솔에서 호출할 수 있는 tcpping이라는 도구가 있습니다.  이 도구를 통해 TCP 끝점에 액세스할 수 있는지 알 수 있지만 하이브리드 연결 끝점에 액세스할 수 있는지는 알 수 없습니다.  콘솔에서 하이브리드 연결 끝점에 사용될 경우 성공한 ping은 호스트:포트 조합을 사용하는 앱에 구성된 하이브리드 연결이 있다는 것을 알려줄 뿐입니다.  

## <a name="biztalk-hybrid-connections"></a>BizTalk 하이브리드 연결 ##

이전 BizTalk 하이브리드 연결 기능의 경우 추가 BizTalk 하이브리드 연결 만들기가 중단되었습니다.  앱에 있는 기존 BizTalk 하이브리드 연결은 계속 사용할 수 있지만 새 서비스로 마이그레이션해야 합니다.  BizTalk 버전에 비해 새 서비스의 몇 가지 장점은 다음과 같습니다.

- 추가 BizTalk 계정이 필요하지 않습니다.
- BizTalk 하이브리드 연결에서 TLS는 1.0이 아닌 1.2입니다.
- 통신이 IP 주소 및 추가적인 기타 포트 범위 대신 DNS 이름을 사용하여 포트 80 및 443을 통해 Azure에 도달합니다.  

BizTalk 하이브리드 연결을 앱에 추가하려면 [Azure Portal][portal]에서 해당 앱으로 이동하고 **네트워킹 > 하이브리드 연결 끝점 구성**을 클릭합니다.  클래식 하이브리드 연결 표에서 **클래식 하이브리드 연결 추가**를 클릭합니다.  여기에 BizTalk 하이브리드 연결 목록이 표시됩니다.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/

