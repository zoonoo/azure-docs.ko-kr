<properties
	pageTitle="SharePoint Server 팜 만들기 | Microsoft Azure"
	description="Azure 포털 마켓플레이스를 사용하여 새로운 기본 또는 고가용성 SharePoint Server 2013 팜을 빠르게 생성하기"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="josephd"/>

# SharePoint 서버 팜 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 모델입니다.

Microsoft Azure 포털 마켓플레이스를 사용하면 미리 구성된 SharePoint Server 2013 팜을 신속하게 만들 수 있습니다. 그러면 개발 및 테스팅 환경을 위해 기본 또는 고가용성 SharePoint 팜이 필요하거나 SharePoint Server 2013 을 조직의 협업 솔루션으로 평가하는 경우 상당한 시간이 줄어들 수 있습니다.

> [AZURE.NOTE] Azure 포털에서 Azure 마켓플레이스의 **SharePoint 서버 팜** 항목이 제거되었습니다. 이 항목은 **SharePoint 2013 비 HA 팜** 및 **SharePoint 2013 HA 팜** 항목으로 대체되었습니다.

기본 SharePoint 팜은 다음 구성의 3가지 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

이 팜 구성을 간소화된 SharePoint 앱 개발 설정이나 SharePoint 2013의 최초 평가에 사용할 수 있습니다.

기본(3-서버) SharePoint 팜을 만들려면:

1. [여기](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)를 클릭합니다.
2. **배포**를 클릭합니다.
3. **SharePoint 2013 비 HA 팜** 창에서 **만들기**를 클릭합니다.
4. **SharePoint 2013 비 HA 팜** 창의 7단계에서 설정을 지정한 다음 **만들기**를 클릭합니다.

고가용성 SharePoint 팜은 다음과 같은 구성으로 9개의 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

이 팜 구성을 사용하여 SharePoint 팜에 대해 보다 과도한 클라이언트 부하, 외부 SharePoint 사이트의 고가용성 및 SQL Server Always On을 테스트할 수 있습니다. 또한 고가용성 환경에서 SharePoint app 개발에 이 구성을 사용할 수 있습니다.

고가용성(9-서버) SharePoint 팜을 만들려면:

1. [여기](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)를 클릭합니다.
2. **배포**를 클릭합니다.
3. **SharePoint 2013 HA 팜** 창에서 **만들기**를 클릭합니다.
4. **SharePoint 2013 HA 팜** 창의 7단계에서 설정을 지정한 다음 **만들기**를 클릭합니다.

> [AZURE.NOTE] Azure 무료 평가판에서는 **SharePoint 2013 비 HA 팜** 또는 **SharePoint 2013 HA 팜**을 만들 수 없습니다.

Azure 포털은 인터넷 연결 웹 서비스를 사용하여 클라우드 전용 가상 네트워크에 이러한 팜을 모두 만듭니다. 조직 네트워크에 대한 사이트 간 VPN 또는 ExpressRoute 연결은 없습니다.

## SharePoint 팜 관리

원격 데스크톱 연결을 통해 이러한 팜의 서버를 관리할 수 있습니다. 자세한 내용은 [가상 컴퓨터에 로그온](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine)을 참조하세요.

중앙 관리 SharePoint 사이트에서 내 사이트, SharePoint 응용 프로그램 및 기타 기능을 구성할 수 있습니다. 자세한 내용은 [SharePoint 2013 구성](http://technet.microsoft.com/library/ee836142.aspx)을 참조하세요.

> [AZURE.NOTE] [SharePoint Server 2016 평가판 이미지](https://azure.microsoft.com/blog/test-sharepoint-server-2016/)를 사용하면, SharePoint Server 2016을 실행하는 가상 컴퓨터를 만들 수 있습니다.

## 다음 단계

- Azure 인프라 서비스에서 추가 [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) 구성을 검색합니다.

<!---HONumber=AcomDC_0518_2016-->