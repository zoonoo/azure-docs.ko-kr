<properties 
	pageTitle="Azure 가상 컴퓨터에 대한 보안 서비스" 
	description="Azure VM의 주요 보안 기능에 대해 간략하게 설명하고 자세한 정보를 확인할 수 있는 링크를 제공합니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="kathydav"/>

#Azure 가상 컴퓨터에 대한 보안 서비스

<p>Azure 가상 컴퓨터의 보안 작업 및 구성은 대부분이 다른 가상 컴퓨터나 물리적 컴퓨터의 보안 작업 및 구성과 같습니다. 여기에는 잘 알려진 사용자 이름이 아닌 강력한 암호를 사용하고, 운영 체제와 응용 프로그램을 업데이트하기 위한 정책 및 일정을 설정하는 것과 같은 기본적인 사항이 포함됩니다. 또한 다음 방법을 권장하고 구현하는 데 사용할 수 있는 기능을 제공합니다.

- 바이러스 백신/맬웨어 방지 실행

- 끝점에서 네트워크 ACL(액세스 제어 목록) 사용
 
##바이러스 백신/맬웨어 방지 실행

Azure는 바이러스 백신/맬웨어 방지 솔루션에 대한 몇 가지 옵션을 제공하지만 관리하는 업무는 사용자의 책임입니다. 예를 들어 검색을 실행하고 업데이트를 설치해야 하는 시기를 결정해야 합니다. 가상 컴퓨터를 만들 때 또는 더 나중에 바이러스 백신 지원을 추가할 수 있습니다. 현재 세 가지 솔루션이 보안 확장으로 제공되며 이러한 솔루션은 새 가상 컴퓨터와 기존 가상 컴퓨터에 설치될 수 있습니다.

- [Azure VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Azure VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Azure 가상 컴퓨터에 맬웨어 방지 솔루션 배포](http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)
 

##가상 컴퓨터 끝점에서 네트워크 ACL(액세스 제어 목록) 사용

네트워크 ACL(액세스 제어 목록)을 사용하여 가상 컴퓨터 끝점으로의 인바운드 트래픽을 선택적으로 허용하거나 거부할 수 있습니다. 이 패킷 필터링 기능을 통해 보안을 강화할 수 있습니다. 이러한 기능의 작동 방식 및 지침에 대한 링크는 [네트워크 ACL(액세스 제어 목록) 정보](http://go.microsoft.com/fwlink/?LinkId=506655)(영문)를 참조하세요.

##추가 리소스
Microsoft Azure 보안 센터의 [리소스](http://azure.microsoft.com/support/trust-center/resources/)

<!---HONumber=58-->