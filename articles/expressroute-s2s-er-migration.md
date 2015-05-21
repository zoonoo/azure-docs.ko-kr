<properties
   pageTitle="사이트 간 VPN을 Express 경로로 마이그레이션"
   description="이 문서는 사이트 간 VPN을 Express 경로로 마이그레이션하는 데 도움이 됩니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# 연결 마이그레이션: Express 경로에 대한 사이트 간 VPN에서

Azure 고객은 사이트 간 VPN 및 Express 경로의 두 가지 방법으로 해당 가상 네트워크에 연결할 수 있습니다. 사이트 간 VPN 연결은 공용 인터넷을 통해 이동합니다. IPsec은 네트워크 트래픽을 암호화하는데 사용됩니다. Express 경로는 Azure에 대한 개인 연결입니다. [Exchange 공급자\(EXP\)](expressroute-exchange-providers.md) 또는 [네트워크 서비스 공급자\(NSP\)](expressroute-network-service-providers.md)를 통해 Azure에 연결할 수 있습니다.

가상 네트워크에 대한 사이트 간 VPN 연결이 이미 있는 경우, 다음 단계에 따라 마이그레이션하여 Express 경로를 통해 연결합니다.

1\) Azure 포털에 로그인

2\) 탐색 창에서 **네트워크**를 클릭하고 VPN에 연결된 가상 네트워크를 클릭합니다.

3\) **대시보드**를 클릭합니다. 페이지 하단에서 **게이트웨이 삭제**를 클릭하고 **예**를 클릭합니다.

  이 단계는 사이트 간 VPN 게이트웨이를 제거합니다.

4\) 포털에서 가상 네트워크에 대해 "게이트웨이가 만들어지지 않았습니다"라고 표시되면, **구성** 페이지를 클릭합니다.

a\) "Express 경로 사용" 확인란을 클릭합니다.

b\) 게이트웨이 서브넷의 CIDR을 /28로 변경합니다.

![Subscription Sharing](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5\) 페이지 하단에서 **저장**을 클릭한 다음 **예**를 클릭합니다.

6\) **대시보드**를 클릭합니다. 페이지 하단에서 **게이트웨이 만들기**를 클릭한 다음 **예**를 클릭합니다.

 이 단계는 Express 경로 게이트웨이를 생성합니다.
 
새 게이트웨이 만들면 가상 네트워크를 Express 경로 회로에 연결할 준비가 됩니다. 가상 네트워크의 모든 가상 컴퓨터는 프로세스를 통해.실행 중입니다. 이동하거나 종료하지 않아도 됩니다.

[Exchange 공급자](expressroute-configuring-exps.md) / [네트워크 서비스 공급자](expressroute-configuring-nsps.md) 자습서를 통해 Express 경로 구성의 마지막 단계로 이동하여 가상 네트워크를 Express 경로 회로에 연결합니다.



<!--HONumber=54-->