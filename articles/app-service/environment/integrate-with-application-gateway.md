---
title: "ILB ASE와 Azure Application Gateway 통합"
description: "ILB ASE의 앱을 Azure Application Gateway와 통합하는 방법에 대한 연습"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>ILB ASE와 Application Gateway 통합 #

[Azure ASE(App Service Environment)](./intro.md)는 고객의 Azure Virtual Network 서브넷에 Azure App Service를 배포한 환경입니다. 앱 액세스를 위해 공용 또는 개인 끝점으로 배포할 수 있습니다. 개인 끝점을 사용하여 ASE를 배포하는 것을 ILB ASE라고 합니다.  
Azure Application Gateway는 7계층 부하 분산, SSL 오프로딩 및 WAF 보호를 제공하는 가상 어플라이언스입니다. 공용 IP 주소에서 수신 대기하고, 트래픽을 응용 프로그램 끝점으로 라우팅할 수 있습니다. 여기서는 WAF가 구성된 Application Gateway를 ILB ASE의 앱과 통합하는 방법에 대해 설명합니다.  

Application Gateway와 ILB ASE의 통합은 앱 수준입니다.  ILB ASE에서 Application Gateway를 구성하는 경우 ILB ASE의 특정 앱에 대해 이를 구성합니다. 이렇게 하면 단일 ILB ASE에서 안전한 다중 테넌트 응용 프로그램을 호스팅할 수 있습니다.  

![ILB ASE의 앱을 가리키는 Application Gateway][1]

이 연습에서는 다음을 수행합니다.

* Application Gateway 만들기
* ILB ASE의 앱을 가리키도록 Application Gateway 구성
* 사용자 지정 도메인 이름을 적용하도록 앱 구성
* Application Gateway를 가리키는 공용 DNS 호스트 이름 편집

Application Gateway를 ILB ASE와 통합하려면 다음이 필요합니다.

* ILB ASE
* ILB ASE에서 실행되는 앱
* ILB ASE의 앱에서 사용할 인터넷 라우팅 가능 도메인 이름
* ILB ASE에서 사용하는 ILB 주소(이것은 **설정 -> IP 주소** 아래의 ASE 포털에 있음)

    ![ILB ASE에서 사용하는 IP 주소][9]
    
* 나중에 응용 프로그램 게이트웨이를 가리키는 데 사용되는 공용 DNS 이름 

ILB ASE를 만드는 방법에 대한 자세한 내용은 [ILB ASE 만들기 및 사용][ilbase]을 참조하세요.

이 가이드에서는 ASE가 배포된 동일한 Azure Virtual Network에 있는 Application Gateway를 원한다고 가정합니다. Application Gateway 만들기를 시작하기 전에 Application Gateway를 호스팅하는 데 사용할 서브넷을 선택하거나 만듭니다. GatewaySubnet이라는 이름이 아닌 서브넷 또는 ILB ASE에서 사용하는 서브넷을 사용해야 합니다.
GatewaySubnet에 Application Gateway를 배치하면 나중에 Virtual Network 게이트웨이를 만들 수 없습니다. 또한 ASE는 서브넷에 있을 수 있는 유일한 항목이므로 ILB ASE에서 사용하는 서브넷에 배치할 수도 없습니다.

## <a name="steps-to-configure"></a>구성 단계 ##

1. Azure Portal에서 **새로 만들기 > 네트워크 > Application Gateway**로 차례로 이동합니다. 
    1. 다음을 제공합니다.
        1. Application Gateway의 이름
        1. WAF 선택
        1. ASE VNet에 사용된 것과 동일한 구독 선택
        1. 리소스 그룹 만들기 또는 선택
        1. ASE VNet이 있는 위치 선택

    ![새 응용 프로그램 게이트웨이 만들기의 기본 사항][2]   
    1. [설정] 영역에서 다음을 설정합니다.
        1. ASE VNet
        1. Application Gateway를 배포해야 하는 서브넷 - VPN 게이트웨이를 만들 수 없게 하므로 GatewaySubnet은 사용하지 마세요.
        1. 공용 선택
        1. 공용 IP 주소 선택 - 아직 없으면 지금 새로 만드세요.
        1. HTTP 또는 HTTPS 구성 - HTTPS를 구성하려면 PFX 인증서를 제공해야 합니다.
        1. 웹 응용 프로그램 방화벽 설정 선택 - 여기서는 방화벽을 사용하도록 설정하고, 검색 또는 방지에 대해서도 적합하게 설정할 수 있습니다.

    ![새 응용 프로그램 게이트웨이 만들기 설정][3]
    
    1. 요약 섹션 검토에서 **확인**을 선택합니다. Application Gateway에서 설정을 완료하는 데 30분 정도 걸릴 수 있습니다.  

2. Application Gateway에서 설정을 완료하면 Application Gateway 포털로 이동합니다. **백 엔드 풀**을 선택합니다.  ILB ASE에 대한 ILB 주소를 추가합니다.

    ![백 엔드 풀 구성][4]

3. 백 엔드 풀 구성이 완료되면 **상태 프로브**를 선택합니다. 앱에 사용하려는 도메인 이름에 대한 상태 프로브를 만듭니다. 

    ![상태 프로브 구성][5]
    
4. 상태 프로브 구성이 완료되면 **HTTP 설정**을 선택합니다.  기존 설정을 편집하고, **사용자 지정 프로브 사용**을 선택하고, 구성한 프로브를 선택합니다.

    ![HTTP 설정 구성][6]
    
5. Application Gateway **개요**로 이동하고 Application Gateway에 사용된 공용 IP 주소를 복사합니다.  해당 IP 주소를 앱 도메인 이름에 대한 A 레코드로 설정하거나 CNAME 레코드에서 해당 주소의 DNS 이름을 사용합니다.  공용 IP 주소를 선택하고, Application Gateway 개요 섹션의 링크에서 복사하는 대신 공용 IP 주소 UI에서 복사하는 것이 더 쉽습니다. 

    ![Application Gateway 포털][7]

6. ILB ASE에서 앱에 대한 사용자 지정 도메인 이름을 설정합니다.  포털에서 앱으로 이동하고 [설정] 아래에서 **사용자 지정 도메인**을 선택합니다.

![앱에 사용자 지정 도메인 이름 설정][8]

웹앱에 대한 사용자 지정 도메인 이름을 설정하는 방법은 [웹앱에 대한 사용자 지정 도메인 이름 설정][custom-domain]에 있습니다. ILB ASE 및 해당 문서의 앱에 대한 차이점은 도메인 이름에 대한 유효성 검사가 없다는 것입니다.  앱 끝점을 관리하는 DNS를 소유하고 있으므로 원하는 항목을 여기에 배치할 수 있습니다. 이 경우에 추가하는 사용자 지정 도메인 이름은 DNS에 포함될 필요는 없지만 앱으로 구성해야 합니다. 

설정이 완료되고 DNS 변경 내용을 전파하는 데 약간의 시간이 걸리면 만든 사용자 지정 도메인 이름으로 앱에 액세스할 수 있습니다. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
