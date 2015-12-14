<properties
	pageTitle="Azure의 SharePoint Server 2013 팜 | Microsoft Azure"
	description="Microsoft Azure에서 개발/테스트 환경 또는 프로덕션 SharePoint Server 2013 팜을 설정하는 방법이 설명된 문서를 찾습니다."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Azure 인프라 서비스에서 호스트되는 SharePoint 팜

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

간편한 구성을 활용하고 새 용량 또는 주요 기능의 최적화를 포함하도록 팜을 신속하게 확장할 수 있는 Microsoft Azure 인프라 서비스에서 첫 번째 또는 다음 개발/테스트 또는 프로덕션 SharePoint 팜을 설정합니다.

> [AZURE.NOTE]Microsoft은 SharePoint Server 2016 IT Preview를 출시했습니다. 이 Preview를 보다 쉽게 설치하고 테스트하기 위해 SharePoint Server 2016 IT Preview 및 해당 필수 구성 요소가 미리 설치된 상태에서 Azure 가상 컴퓨터 갤러리 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure에서 SharePoint Server 2016 IT Preview 테스트](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)를 참조하세요.

## 기본 SharePoint 개발/테스트 팜

자동으로 만들어진 환경은 클라우드 전용 Azure 가상 네트워크에 있는 3개의 서버(각각 도메인 컨트롤러, SQL Server 및 SharePoint Server용)로 구성됩니다.

Azure 포털에서 Azure 마켓플레이스의 [SharePoint 2013 비 HA 팜](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) 항목을 참조하세요. 인터넷 연결 SharePoint 웹사이트에 대한 기본 개발/테스트 팜을 만듭니다. 추가 정보는 [SharePoint 서버 팜 만들기](virtual-machines-sharepoint-farm-azure-preview.md)를 참조하세요.

Azure 리소스 관리자 템플릿을 사용할 수도 있습니다. [3개의 서버 SharePoint 팜 배포](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm)를 참조하세요.

> [AZURE.NOTE]Azure 포털에서 Azure 마켓플레이스의 **SharePoint 서버 팜** 항목이 제거되었습니다.

## 고가용성 SharePoint 개발자/테스트 팜

자동으로 만들어진 환경은 클라우드 전용 Azure 가상 네트워크에 있는 9개의 서버(도메인 컨트롤러용 2개, SQL Server 클러스터용 3개, 응용 프로그램 계정 SharePoint Server용 2개 및 웹 계층 SharePoint Server용 2개)로 구성됩니다.

Azure 포털에서 Azure 마켓플레이스의 [SharePoint 2013 HA 팜](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) 항목을 참조하세요. 인터넷 연결 SharePoint 웹사이트에 대한 고가용성 개발/테스트 팜을 만듭니다. 추가 정보는 [SharePoint 서버 팜 만들기](virtual-machines-sharepoint-farm-azure-preview.md)를 참조하세요.

Azure 리소스 관리자 템플릿을 사용할 수도 있습니다. [9개의 서버 SharePoint 팜 배포](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm)를 참조하세요.

> [AZURE.NOTE]Azure 포털에서 Azure 마켓플레이스의 **SharePoint 서버 팜** 항목이 제거되었습니다.

## 하이브리드 클라우드 개발/테스트 팜

[하이브리드 클라우드 개발/테스트 환경의 SharePoint 인트라넷 팜](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)을 통해 인터넷상의 현재 위치에서 Azure에서 호스트되는 인트라넷 SharePoint 팜을 테스트하는 데 사용할 수 있는 간단한 2계층 SharePoint 팜을 호스트하는 시뮬레이션된 하이브리드 클라우드 구성을 만들 수 있습니다.

이 구성은 클래식 배포 모델을 사용합니다.

## 고가용성, 인트라넷 Sharepoint 프로덕션 팜

[Azure에서 SQL Server AlwaysOn 가용성 그룹이 있는 SharePoint 2013](virtual-machines-workload-intranet-sharepoint-overview.md) 배포를 통해 Azure에서 프로덕션을 지원하는 고가용성 인트라넷 SharePoint Server 2013 팜을 구축합니다.

이 구성은 클래식 배포 모델을 사용합니다.

## 추가 리소스

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

[SharePoint Server 2013을 사용하는 Microsoft Azure의 인터넷 사이트](https://technet.microsoft.com/library/dn635307.aspx)

[Microsoft Azure의 SharePoint Server 2013 재해 복구](https://technet.microsoft.com/library/dn635313.aspx)

[SharePoint 2013 인증에 Microsoft Azure Active Directory 사용](https://technet.microsoft.com/library/dn635311.aspx)

[Microsoft Azure에서 Office 365 디렉터리 동기화(DirSync) 배포](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=AcomDC_1203_2015-->