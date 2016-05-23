<properties
	pageTitle="Azure의 SharePoint Server 2013 팜 | Microsoft Azure"
	description="Microsoft Azure에서 개발/테스트 환경 또는 프로덕션 SharePoint Server 2013 팜을 설정하는 방법이 설명된 문서를 찾습니다."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# Azure 인프라 서비스에서 호스트되는 SharePoint 팜

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

간편한 구성을 활용하고 새 용량 또는 주요 기능의 최적화를 포함하도록 팜을 신속하게 확장할 수 있는 Microsoft Azure 인프라 서비스에서 첫 번째 또는 다음 개발/테스트 또는 프로덕션 SharePoint Server 2013 팜을 설정합니다.

## 기본 SharePoint 개발/테스트 팜

자동으로 만들어진 환경은 클라우드 전용 Azure 가상 네트워크에 있는 3개의 서버(각각 도메인 컨트롤러, SQL Server 및 SharePoint Server용)로 구성됩니다.

Azure 포털에서 Azure 마켓플레이스의 [SharePoint 2013 비 HA 팜](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) 항목을 참조하세요. 인터넷 연결 SharePoint 웹사이트에 대한 기본 개발/테스트 팜을 만듭니다. 추가 정보는 [SharePoint 서버 팜 만들기](virtual-machines-windows-sharepoint-farm.md)를 참조하세요.

Azure 리소스 관리자 템플릿을 사용할 수도 있습니다. [SharePoint](virtual-machines-linux-app-frameworks.md)를 참조하세요.

> [AZURE.NOTE] Azure 포털에서 Azure 마켓플레이스의 **SharePoint 서버 팜** 항목이 제거되었습니다.

## 고가용성 SharePoint 개발자/테스트 팜

자동으로 만들어진 환경은 클라우드 전용 Azure 가상 네트워크에 있는 9개의 서버(도메인 컨트롤러용 2개, SQL Server 클러스터용 3개, 응용 프로그램 계정 SharePoint Server용 2개 및 웹 계층 SharePoint Server용 2개)로 구성됩니다.

Azure 포털에서 Azure 마켓플레이스의 [SharePoint 2013 HA 팜](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) 항목을 참조하세요. 인터넷 연결 SharePoint 웹사이트에 대한 고가용성 개발/테스트 팜을 만듭니다. 추가 정보는 [SharePoint 서버 팜 만들기](virtual-machines-windows-sharepoint-farm.md)를 참조하세요.

Azure 리소스 관리자 템플릿을 사용할 수도 있습니다. [9개의 서버 SharePoint 팜 배포](virtual-machines-windows-app-frameworks.md#deploy-a-nine-server-sharepoint-farm)를 참조하세요.

> [AZURE.NOTE] Azure 포털에서 Azure 마켓플레이스의 **SharePoint 서버 팜** 항목이 제거되었습니다.

## 하이브리드 클라우드 개발/테스트 팜

[하이브리드 클라우드 개발/테스트 환경의 SharePoint 인트라넷 팜](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md)을 통해 인터넷상의 현재 위치에서 Azure에서 호스트되는 인트라넷 SharePoint 팜을 테스트하는 데 사용할 수 있는 간단한 2계층 SharePoint 팜을 호스트하는 시뮬레이션된 하이브리드 클라우드 구성을 만들 수 있습니다.

## 고가용성, 인트라넷 Sharepoint 프로덕션 팜

[Azure에서 SQL Server AlwaysOn 가용성 그룹이 있는 SharePoint 2013](virtual-machines-windows-sp-intranet-overview.md) 배포를 통해 Azure에서 프로덕션을 지원하는 고가용성 인트라넷 SharePoint Server 2013 팜을 구축합니다.

## 다음 단계

- Azure 인프라 서비스에서 추가 [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) 구성을 검색합니다.

<!---HONumber=AcomDC_0511_2016-->