<properties 
	pageTitle="Azure 인프라 서비스에서 호스트되는 SharePoint 팜" 
	description="Microsoft Azure 인프라 서비스에서 개발/테스트 또는 프로덕션 SharePoint 2013 팜을 설정하는 방법을 설명하는 주요 문서를 알아봅니다." 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sharepoint" 
	ms.devlang="na" 
	ms.topic="index-page" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# Azure 인프라 서비스에서 호스트되는 SharePoint 팜

간편한 구성을 활용하고 새 용량 또는 주요 기능의 최적화를 포함하도록 팜을 신속하게 확장할 수 있는 Microsoft Azure 인프라 서비스에서 첫 번째 또는 다음 개발/테스트 또는 프로덕션 SharePoint 팜을 설정합니다.

## 기본 SharePoint 개발/테스트 팜 

서비스 관리에서 만든 가상 컴퓨터에 대해, 인터넷 연결 SharePoint 웹 사이트에 대한 기본 개발/테스트 팜을 만들려면 Azure 미리보기 포탈의 [SharePoint Server 팜](virtual-machines-sharepoint-farm-azure-preview.md) 기능을 사용합니다.

자동으로 만들어진 환경은 클라우드 전용 Azure 가상 네트워크에 있는 3개의 서버(각각 도메인 컨트롤러, SQL Server 및 SharePoint Server용)로 구성됩니다.

리소스 관리자에서 만든 가상 컴퓨터로 유사한 구성을 만들려면 템플릿을 사용합니다. [3개의 서버 SharePoint 팜 배포](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm)를 참조하세요.

## 고가용성 SharePoint 개발자/테스트 팜

서비스 관리에서 만든 가상 컴퓨터에 대해, 인터넷 연결 SharePoint 웹 사이트에 대한 고가용성 SharePoint 기본 개발/테스트 팜을 만들려면 Azure 미리보기 포탈의 [SharePoint Server 팜](virtual-machines-sharepoint-farm-azure-preview.md) 기능을 사용합니다.

자동으로 만들어진 환경은 클라우드 전용 Azure 가상 네트워크에 있는 9개의 서버(도메인 컨트롤러용 2개, SQL Server 클러스터용 3개, 응용 프로그램 계정 SharePoint Server용 2개 및 웹 계층 SharePoint Server용 2개)로 구성됩니다.

리소스 관리자에서 만든 가상 컴퓨터로 유사한 구성을 만들려면 템플릿을 사용합니다. [9개의 서버 SharePoint 팜 배포](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm)를 참조하세요.

## 하이브리드 클라우드 개발/테스트 팜

[하이브리드 클라우드 개발/테스트 환경의 SharePoint 인트라넷 팜](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)을 통해 인터넷상의 현재 위치에서 Azure에서 호스트되는 인트라넷 SharePoint 팜을 테스트하는 데 사용할 수 있는 간단한 2계층 SharePoint 팜을 호스트하는 시뮬레이션된 하이브리드 클라우드 구성을 만들 수 있습니다.

이 구성은 서비스 관리에서 만든 가상 컴퓨터를 사용합니다.

## 고가용성, 인트라넷 Sharepoint 프로덕션 팜

[Azure에서 SQL Server AlwaysOn 가용성 그룹이 있는 SharePoint 2013 배포](virtual-machines-workload-intranet-sharepoint-overview.md)를 통해 Azure에서 프로덕션을 지원하는 고가용성 인트라넷 SharePoint Server 2013 팜을 구축할 수 있습니다.

이 구성은 서비스 관리에서 만든 가상 컴퓨터를 사용합니다.

## 추가 리소스

[Azure 인프라 서비스의 SharePoint Server](https://msdn.microsoft.com/library/dn275955.aspx)

[Azure 인프라 서비스에서 SharePoint 2013 계획](https://msdn.microsoft.com/library/dn275958.aspx)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)
 

<!---HONumber=July15_HO2-->