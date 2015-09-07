<properties 
	pageTitle="LOB(기간 업무) 응용 프로그램 배포 | Microsoft Azure"
	description="다섯 단계를 통해 Azure의 SQL Server AlwaysOn 가용성 그룹으로 웹 기반 고가용성 LOB(기간 업무) 응용 프로그램을 배포합니다."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# 고가용성 LOB(기간 업무) 응용 프로그램 배포

이 항목에는 Azure 인프라 서비스에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 고가용성 인트라넷 전용 웹 기반 LOB(기간 업무) 응용 프로그램을 배포하는 단계별 지침에 대한 링크가 포함되어 있습니다. 응용 프로그램은 다음 컴퓨터에서 호스팅됩니다.

- 웹 서버 두 대
- 데이터베이스 서버 두 대
- 클러스터 주 노드 서버 한 대
- 도메인 컨트롤러 두 개

이 구성은 다음과 같습니다. 각 서버에는 자리 표시자 이름이 표시됩니다.

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
각 역할에 대해 컴퓨터가 두 대 이상이므로 고가용성이 보장됩니다. 모든 가상 컴퓨터가 단일 Azure 위치(지역이라고도 함)에 있습니다. 특정 역할에 대한 각 가상 컴퓨터 그룹은 자체 가용성 집합에 있습니다.

다음 단계를 통해 이 구성을 배포합니다.

- [1단계: Azure 구성](virtual-machines-workload-high-availability-LOB-application-phase1.md). 저장소 계정, 가용성 집합 및 크로스-프레미스 가상 네트워크를 만들 수 있습니다.
- [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-high-availability-LOB-application-phase2.md). 복제본 AD DS(Active Directory 도메인 서비스) 도메인 컨트롤러를 만들고 구성합니다.
- [3단계: SQL Server 인프라 구성](virtual-machines-workload-high-availability-LOB-application-phase3.md). SQL Server를 실행하는 가상 컴퓨터를 만들어 구성하고, 클러스터를 만들고, SQL Server AlwaysOn 가용성 그룹을 사용하도록 설정합니다.
- [4단계: 웹 서비스 구성](virtual-machines-workload-high-availability-LOB-application-phase4.md) 웹 서버 가상 커퓨터 2대를 만들고 구성합니다.
- [5단계: 응용 프로그램 데이터베이스를 SQL Server AlwaysOn 가용성 그룹에 추가](virtual-machines-workload-high-availability-LOB-application-phase5.md). LOB(기간 업무) 응용 프로그램 데이터베이스를 준비하여 SQL Server AlwaysOn 가용성 그룹에 추가합니다.

이 배포는 [LOB(기간 업무) 응용 프로그램 아키텍처 청사진](http://msdn.microsoft.com/dn630664)을 함께 제공하도록 설계되었으며 최신 권장 사항을 통합합니다.

규범적이고 미리 정의된 아키텍처입니다. 다음 사항을 고려하세요.

- 웹 기반 LOB(기간 업무) 응용 프로그램 구현 작업에 익숙한 경우 3~5단계의 지침을 조정하여 요구 사항에 가장 적합한 응용 프로그램 인프라를 구축할 수 있습니다. 
- 기존 Azure 하이브리드 클라우드 구현이 이미 있는 경우 1~2단계의 지침을 조정하거나 건너뛰어 적절한 서브넷에서 새 가상 컴퓨터를 호스팅할 수 있습니다.
- 모든 서버는 Azure 가상 네트워크에서 단일 서브넷에 있습니다. 서브넷 격리에 해당하는 추가 보안을 제공하려는 경우 [네트워크 보안 그룹](../virtual-networks/virtual-networks-nsg.md)을 사용할 수 있습니다.

이 구성의 개념 증명 또는 개발/테스트 환경을 빌드하려면 [테스트용 하이브리드 클라우드에 웹 기반 LOB 응용 프로그램 설치](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)를 참조하세요.

Azure에 대한 IT 작업 부하 설계에 관한 자세한 내용은 [Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)을 참조하세요.

## 다음 단계

이 작업의 구성을 시작하려면 [1단계: Azure 구성](virtual-machines-workload-high-availability-LOB-application-phase1.md)으로 진행하세요.

## 추가 리소스

[LOB(기간 업무) 응용 프로그램 아키텍처 청사진](http://msdn.microsoft.com/dn630664)

[테스트를 위한 하이브리드 클라우드에서 웹 기반 LOB 응용 프로그램 설정](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 인프라 서비스 작업: SharePoint Server 2013 팜](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO9-->