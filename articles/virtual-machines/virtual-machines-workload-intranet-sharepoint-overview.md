<properties
	pageTitle="Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포"
	description="5단계를 통해 Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint를 배포할 수 있습니다."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="josephd"/>

# Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포

이 항목에는 Azure 서비스 관리에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 단계별 지침에 대한 링크가 포함되어 있습니다. 팜은 다음 컴퓨터로 구성됩니다.

- SharePoint 웹 서버 두 대
- SharePoint 응용 프로그램 서버 두 대
- 데이터베이스 서버 두 대
- 클러스터 주 노드 서버 한 대
- 도메인 컨트롤러 두 개

이 구성은 다음과 같습니다. 각 서버에는 자리 표시자 이름이 표시됩니다.

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

각 역할에 대해 컴퓨터가 두 대씩이므로 고가용성이 보장됩니다. 모든 가상 컴퓨터는 단일 영역에 있습니다. 특정 역할에 대한 각 가상 컴퓨터 그룹은 자체 가용성 집합에 있습니다.

다음 단계를 통해 이 구성을 배포합니다.

- [1단계: Azure 구성](virtual-machines-workload-intranet-sharepoint-phase1.md). 저장소 계정, 클라우드 서비스 및 크로스-프레미스 가상 네트워크를 만듭니다.
- [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-intranet-sharepoint-phase2.md). 복제본 AD DS(Active Directory 도메인 서비스) 도메인 컨트롤러를 만들고 구성합니다.
- [3단계: SQL Server 인프라 구성](virtual-machines-workload-intranet-sharepoint-phase3.md). SQL Server 가상 컴퓨터를 작성/구성하고, 이러한 가상 컴퓨터를 SharePoint에 사용하도록 준비하고, 클러스터를 만듭니다.
- [4단계: SharePoint 서버 구성](virtual-machines-workload-intranet-sharepoint-phase4.md). SharePoint 가상 컴퓨터 4대를 만들고 구성합니다.
- [5단계: 가용성 그룹을 만들고 SharePoint 데이터베이스 추가](virtual-machines-workload-intranet-sharepoint-phase5.md). 데이터베이스를 준비하고 SQL Server AlwaysOn 가용성 그룹을 만듭니다.

SQL Server AlwaysOn을 사용하는 이 SharePoint 배포에서는 [SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)이 함께 제공되며, 최신 권장 사항이 통합됩니다.

이 구성은 Azure 인프라 서비스에서 정상적으로 작동하며 항상 사용 가능한 인트라넷 SharePoint 팜을 만들기 위한 미리 정의된 아키텍처를 규정하는 단계별 가이드입니다. Azure에서 SharePoint 2013를 구현하기 위한 추가 아키텍처 지침은 [SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)를 참조하세요.

다음 사항을 고려하세요.

- SharePoint 구현 작업에 익숙한 경우 3\~5단계의 지침을 조정하여 요구 사항에 가장 적합한 팜을 구축할 수 있습니다.
- 기존 Azure 하이브리드 클라우드 구현이 이미 있는 경우 1\~2단계의 지침을 조정하거나 건너뛰어 적절한 서브넷에서 새 SharePoint 팜을 호스팅할 수 있습니다.
- 모든 서버는 Azure 가상 네트워크에서 단일 서브넷에 있습니다. 서브넷 격리에 해당하는 추가 보안을 제공하려는 경우 [네트워크 보안 그룹](https://msdn.microsoft.com/library/azure/dn848316.aspx)을 사용할 수 있습니다.

이 구성의 개념 증명 또는 개발/테스트 환경을 빌드하려면 [테스트용 하이브리드 클라우드에 SharePoint 인트라넷 팜 설치](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)를 참조하세요.

SQL Server AlwaysOn 가용성 그룹을 사용하는 SharePoint에 대한 자세한 내용은 [SQL Server 2013에 대해 SQL Server 2012 AlwaysOn 가용성 그룹 구성](https://technet.microsoft.com/library/jj715261.aspx)을 참조하세요.

## 다음 단계

이 작업의 구성을 시작하려면 [1단계: Azure 구성](virtual-machines-workload-intranet-sharepoint-phase1.md)으로 진행하세요.


## 추가 리소스

[SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=July15_HO5-->