<properties
	pageTitle="Azure의 SharePoint Server 2013 팜 | Microsoft Azure"
	description="Azure의 SharePoint Server 2013 팜의 가치를 알아보고, 테스트 환경을 설정하고, 고가용성 구성을 배포합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# Azure 인프라 서비스 워크로드: 인트라넷 SharePoint 팜

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

Microsoft Azure에서 첫 번째 또는 다음 SharePoint 팜을 설정하고, 간편한 구성과 새 용량 또는 주요 기능의 최적화를 포함하도록 팜을 신속하게 확장할 수 있는 기능을 활용하세요. 많은 SharePoint 팜은 가용성이 뛰어난 표준 3계층 구성에서 성능 또는 별도 역할(예: 분산 캐싱 또는 검색)에 최적화된 수십 가지 이상의 서버가 포함된 팜으로 확장됩니다.

Azure 인프라 서비스의 가상 컴퓨터 및 가상 네트워크 기능을 사용하면 온-프레미스 네트워크에 투명하게 연결된 SharePoint 팜에 빠르게 배포하고 실행할 수 있습니다. 예를 들어, 다음 네트워크를 설정할 수 있습니다.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

Azure 가상 네트워크는 모두 올바른 이름 지정 및 적절한 트래픽 라우팅을 사용하는 온-프레미스 네트워크의 확장이기 때문에 사용자는 온-프레미스 데이터 센터에 있는 경우와 동일한 방식으로 액세스할 수 있습니다.

이 구성을 통해 새 Azure 가상 컴퓨터를 추가하여 SharePoint 팜을 쉽게 확장할 수 있으며, 하드웨어와 유지 관리의 지속적인 비용 면에서 SharePoint 팜을 확장하는 것이 데이터 센터에서 해당 팜을 실행하는 것보다 낮습니다.

Azure 인프라 서비스에서 인트라넷 SharePoint 팜을 호스트하는 경우가 LOB(기간 업무) 응용 프로그램의 한 예입니다. 개요에 대해서는 [LOB(기간 업무) 응용 프로그램 아키텍처 청사진](http://msdn.microsoft.com/dn630664)을 참조하세요.

다음 단계는 Azure에서 호스트되는 개발/테스트 인트라넷 SharePoint 팜을 설정하는 것입니다.

> [AZURE.NOTE]Microsoft은 SharePoint Server 2016 IT Preview를 출시했습니다. 이 Preview를 보다 쉽게 설치하고 테스트하기 위해 SharePoint Server 2016 IT Preview 및 해당 필수 구성 요소가 미리 설치된 상태에서 Azure 가상 컴퓨터 갤러리 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure에서 SharePoint Server 2016 IT Preview 테스트](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)를 참조하세요.

## Azure에서 호스트되는 개발/테스트 인트라넷 SharePoint 팜 만들기

Azure에서 호스트되는 SharePoint 팜에 대한 개발/테스트 환경을 만들 수 있는 두 가지 옵션이 있습니다.

- 클라우드 전용 가상 네트워크
- 프레미스 간 가상 네트워크

[MSDN 구독](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) 또는 [Azure 평가판 구독](http://azure.microsoft.com/pricing/free-trial/)을 사용하여 이러한 개발/테스트 환경을 무료로 만들 수 있습니다.

### 클라우드 전용 가상 네트워크

클라우드 전용 가상 네트워크는 온-프레미스 네트워크에 연결되지 않습니다. 기본 또는 고가용성 SharePoint 팜을 빠르게 만들려면 [SharePoint 서버 팜](virtual-machines-sharepoint-farm-azure-preview.md)을 참조하세요. 기본 SharePoint 팜 구성 예는 다음과 같습니다.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/SPFarm_Basic.png)

### 프레미스 간 가상 네트워크

프레미스 간 가상 네트워크는 사이트 간 VPN 또는 Express 경로 연결을 사용하여 온-프레미스 네트워크에 연결됩니다. SharePoint 서버에 액세스하고 VPN 연결을 통해 원격 관리를 수행하여 최종 구성을 모방하는 개발/테스트 환경을 만들려는 경우 [하이브리드 클라우드에서 테스트를 위한 SharePoint 인트라넷 팜 설정](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)을 참조하세요.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)

다음 단계는 Azure에서 고가용성 인트라넷 SharePoint 팜을 만드는 것입니다.

## Azure에서 호스트되는 인트라넷 SharePoint 팜 배포

기능적인 고가용성 인트라넷 SharePoint 팜에 대한 기본적인 대표 구성은 다음과 같습니다.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)

이 구성은 다음으로 이루어집니다.

- 웹, 응용 프로그램 및 데이터베이스 계층에 두 서버가 있는 인트라넷 SharePoint 팜
- 클러스터에 두 SQL 서버 및 과반수 노드 컴퓨터가 있는 SQL Server AlwaysOn 가용성 그룹 구성
- 두 복제본 도메인 컨트롤러를 사용하는 가상 네트워크의 Azure Active Directory

이 구성을 인포그래픽으로 보려면 [SQL Server AlwaysOn을 지원하는 SharePoint](http://go.microsoft.com/fwlink/?LinkId=394788)를 참조하세요.

### 제품 구성 정보

이 기본 구성을 사용하려면 다음과 같은 Azure 서비스 및 구성 요소 집합이 필요합니다.

- 가상 컴퓨터 9대
- 도메인 컨트롤러 및 SQL Server용 추가 데이터 디스크 4개
- 클라우드 서비스 3개
- 가용성 집합 4개
- 프레미스 간 가상 네트워크 1개
- 저장소 계정 1개
- Azure 구독 1개

### 배포 단계

이 구성을 배포하려면 다음 프로세스를 사용합니다.

- 1단계: Azure 구성

	Azure 포털 및 Azure PowerShell을 사용하여 저장소 계정, 클라우드 서비스 및 프레미스 간 가상 네트워크를 만듭니다. 자세한 구성 단계는 [1단계](virtual-machines-workload-intranet-sharepoint-phase1.md)를 참조하세요.

- 2단계: 도메인 컨트롤러 구성

	Azure Active Directory 복제본 도메인 컨트롤러 2개 및 가상 네트워크에 대한 DNS 설정을 구성합니다. 자세한 구성 단계는 [2단계](virtual-machines-workload-intranet-sharepoint-phase2.md)를 참조하세요.

- 3단계: SQL Server 인프라 구성

	SharePoint와 함께 사용할 SQL Server 가상 컴퓨터를 준비하고, SQL Server 클러스터를 만듭니다. 자세한 구성 단계는 [3 단계](virtual-machines-workload-intranet-sharepoint-phase3.md)를 참조하세요.

- 4단계: SharePoint 서버 구성

	새 SharePoint 팜에 대한 SharePoint 가상 컴퓨터 4대를 구성합니다. 자세한 구성은 [4 단계](virtual-machines-workload-intranet-sharepoint-phase4.md)를 참조하세요.

- 5 단계: AlwaysOn 가용성 그룹 만들기

	SharePoint 데이터베이스를 준비하고, AlwaysOn 가용성 그룹을 만든 다음, 해당 그룹에 SharePoint 데이터베이스를 추가합니다. 자세한 구성 단계는 [단계 5](virtual-machines-workload-intranet-sharepoint-phase5.md)를 참조하세요.

구성된 SharePoint 팜은 [SharePoint 2013용 Microsoft Azure 아키텍처](http://technet.microsoft.com/library/dn635309.aspx)의 지침을 사용하여 확장할 수 있습니다.

## 추가 리소스

[Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-deploy-spsqlao-overview.md)

[테스트용 하이브리드 클라우드에 SharePoint 인트라넷 팜 설치](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

[SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 인프라 서비스 워크로드: 고가용성 LOB(기간 업무) 응용 프로그램](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Sept15_HO3-->