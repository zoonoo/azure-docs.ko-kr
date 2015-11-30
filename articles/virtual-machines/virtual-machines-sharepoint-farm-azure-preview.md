<properties
	pageTitle="SharePoint Server 팜 만들기 | Microsoft Azure"
	description="Azure Preview 포털 마켓플레이스를 통해 새로운 기본 또는 고가용성 SharePoint Server 2013 팜을 신속하게 만들 수 있습니다."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="josephd"/>

# SharePoint 서버 팜 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 모델입니다.

Microsoft Azure Preview 포털 마켓플레이스를 사용하면 신속히 사전 구성된 SharePoint Server 2013 팜을 만들 수 있습니다. 그러면 개발 및 테스팅 환경을 위해 기본 또는 고가용성 SharePoint 팜이 필요하거나 SharePoint Server 2013 을 조직의 협업 솔루션으로 평가하는 경우 상당한 시간이 줄어들 수 있습니다.

> [AZURE.NOTE]Azure Preview 포털의 Azure 마켓플레이스에서 **SharePoint 서버 팜** 항목이 제거되었습니다.

기본 SharePoint 팜은 다음 구성의 3가지 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

이 팜 구성을 간소화된 SharePoint 앱 개발 설정이나 SharePoint 2013의 최초 평가에 사용할 수 있습니다.

기본(3-서버) SharePoint 팜을 만들려면:

1. [여기](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)를 클릭합니다.
2. **배포**를 클릭합니다.
3. **SharePoint 2013 비 HA 팜** 창에서 **만들기**를 클릭합니다.
4. **SharePoint 2013 비 HA 팜** 창의 7단계에서 설정을 지정한 다음 **만들기**를 클릭합니다.

고가용성 SharePoint 팜은 다음과 같은 구성으로 9개의 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

이 팜 구성을 사용하여 SharePoint 팜에 대해 보다 과도한 클라이언트 부하, 외부 SharePoint 사이트의 고가용성 및 SQL Server AlwaysOn을 테스트할 수 있습니다. 또한 고가용성 환경에서 SharePoint app 개발에 이 구성을 사용할 수 있습니다.

고가용성(9-서버) SharePoint 팜을 만들려면:

1. [여기](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)를 클릭합니다.
2. **배포**를 클릭합니다.
3. **SharePoint 2013 HA 팜** 창에서 **만들기**를 클릭합니다.
4. **SharePoint 2013 HA 팜** 창의 7단계에서 설정을 지정한 다음 **SharePoint 2013 HA 팜 만들기**를 클릭합니다.

> [AZURE.NOTE]Microsoft은 SharePoint Server 2016 IT Preview를 출시했습니다. 이 Preview를 보다 쉽게 설치하고 테스트하기 위해 SharePoint Server 2016 IT Preview 및 해당 필수 구성 요소가 미리 설치된 상태에서 Azure 가상 컴퓨터 갤러리 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure에서 SharePoint Server 2016 IT Preview 테스트](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)를 참조하세요.

## SharePoint 팜 관리

원격 데스크톱 연결을 통해 이러한 팜의 서버를 관리할 수 있습니다. 자세한 내용은 [가상 컴퓨터에 로그온](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine)을 참조하세요.

중앙 관리 SharePoint 사이트에서 내 사이트, SharePoint 응용 프로그램 및 기타 기능을 구성할 수 있습니다. 자세한 내용은 [SharePoint 2013 구성](http://technet.microsoft.com/library/ee836142.aspx)을 참조하세요.

> [AZURE.NOTE]Azure Preview 포털은 인터넷 연결 웹 서비스를 사용하는 클라우드 전용 가상 네트워크에서 이러한 팜을 모두 만듭니다. 조직 네트워크에 대한 사이트 간 VPN 또는 ExpressRoute 연결은 없습니다.

## 추가 리소스

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[테스트용 하이브리드 클라우드에 SharePoint 인트라넷 팜 설치](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

<!---HONumber=Nov15_HO4-->