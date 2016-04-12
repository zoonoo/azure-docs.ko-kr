<properties 
	pageTitle="Azure의 LOB(기간 업무) 응용 프로그램 | Microsoft Azure" 
	description="Azure의 LOB(기간 업무) 응용 프로그램의 가치를 알아보고, 테스트 환경을 설정하고, 고가용성 구성을 배포합니다." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="josephd"/>

# Azure 인프라 서비스 워크로드: 고가용성 LOB(기간 업무) 응용 프로그램

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


Microsoft Azure에서 첫 번째 또는 다음 인트라넷 전용 LOB(기간 업무) 응용 프로그램을 설정하고, 응용 프로그램의 간편한 구성 및 신속한 확장 기능을 통해 새 용량을 추가합니다.
 
Azure 인프라 서비스의 가상 컴퓨터 및 가상 네트워크 기능을 사용하면 조직 사용자가 액세스할 수 있는 LOB(기간 업무) 응용 프로그램을 신속하게 배포 및 실행할 수 있습니다. 예를 들어 이를 설정할 수 있습니다.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
Azure 가상 네트워크는 모두 올바른 이름 지정 및 적절한 트래픽 라우팅을 사용하는 온-프레미스 네트워크의 확장이기 때문에 사용자는 온-프레미스 데이터 센터에 있는 경우와 동일한 방식으로 LOB(기간 업무) 응용 프로그램 서버에 액세스할 수 있습니다.

이 구성을 통해 새 Azure 가상 컴퓨터를 추가하여 응용 프로그램 용량을 간편하게 확장할 수 있으며, 하드웨어와 유지 관리의 지속적인 비용 면에서 데이터 센터에서 해당 팜을 실행하는 것보다 낮습니다.

다음 단계는 Azure에서 호스팅되는 비즈니스 응용 프로그램의 개발/테스트 라인을 설정하는 것입니다.

## Azure에서 호스팅되는 비즈니스 응용 프로그램의 개발/테스트 라인 만들기

프레미스 간 가상 네트워크는 사이트 간 VPN 또는 Express 경로 연결을 사용하여 온-프레미스 네트워크에 연결됩니다. 최종 구성을 모방하는 개발/테스트 환경을 만든 후 응용 프로그램에 액세스하고 VPN 연결을 통해 원격 관리를 수행하는 작업을 시험하려는 경우 [하이브리드 클라우드에서 테스트를 위한 웹 기반 LOB 응용 프로그램 설정](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)을 참조하세요.

![](./media/virtual-machines-windows-lob/CreateLOBAppHybridCloud_3.png)
 
[MSDN 구독](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) 또는 [Azure 평가판 구독](https://azure.microsoft.com/pricing/free-trial/)을 사용하여 이러한 개발/테스트 환경을 무료로 만들 수 있습니다.

다음 단계는 Azure에서 고가용성 LOB(기간 업무) 응용 프로그램을 만드는 것입니다.

## Azure에서 호스팅되는 LOB(기간 업무) 응용 프로그램 배포

Azure의 고가용성 LOB(기간 업무) 응용 프로그램에 대한 기본적인 대표 구성은 다음과 같습니다.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
이 구성은 다음으로 이루어집니다.

- 웹과 데이터베이스 계층에 두 서버가 있는 인트라넷 전용 LOB(기간 업무) 응용 프로그램
- SQL Server를 실행하는 두 가상 컴퓨터와, 한 클러스터에 다수 노드 컴퓨터가 있는 SQL Server AlwaysOn 구성
- 두 복제본 도메인 컨트롤러를 사용하는 가상 네트워크의 Active Directory 도메인 서비스

LOB(기간 업무) 응용 프로그램 개요는 [LOB(기간 업무) 응용 프로그램 아키텍처 청사진](http://msdn.microsoft.com/dn630664)을 참조하세요.

이 구성을 배포하려면 다음 프로세스를 사용합니다.

- 1단계: Azure 구성 

	Azure PowerShell을 사용하여 저장소 계정, 가용성 집합 및 크로스-프레미스 가상 네트워크를 만들 수 있습니다. 자세한 구성 단계는 [1단계](virtual-machines-windows-ps-lob-ph1.md)를 참조하세요.

- 2단계: 도메인 컨트롤러 구성

	Active Directory 복제본 도메인 컨트롤러 2개 및 가상 네트워크에 대한 DNS 설정을 구성합니다. 자세한 구성 단계는 [2단계](virtual-machines-windows-ps-lob-ph2.md)를 참조하세요.

- 3단계: SQL Server 인프라 구성

	클러스터와 SQL Server를 실행 중인 가상 컴퓨터를 만듭니다. 자세한 구성 단계는 [3 단계](virtual-machines-windows-ps-lob-ph3.md)를 참조하세요.

- 4단계: 웹 서비스 구성

	웹 서버 가상 컴퓨터를 만들어 LOB(기간 업무) 응용 프로그램을 배포합니다. 자세한 구성은 [4 단계](virtual-machines-windows-ps-lob-ph4.md)를 참조하세요.

- 5단계: SQL Server AlwaysOn 가용성 그룹 구성

	응용 프로그램 데이터베이스를 준비하고, AlwaysOn 가용성 그룹을 만든 다음 해당 그룹에 응용 프로그램 데이터베이스를 추가합니다. 자세한 구성 단계는 [단계 5](virtual-machines-windows-ps-lob-ph5.md)를 참조하세요.

구성 후에는 SQL Server를 실행하는 다른 웹 서버나 가상 컴퓨터를 클러스터에 추가하여 간편하게 LOB(기간 업무) 응용 프로그램을 확장할 수 있습니다.

## 다음 단계

- 구성에 대해 자세히 알아보기 전에 프로덕션 워크로드의 [개요](virtual-machines-windows-lob-overview.md)를 확인합니다.

<!---HONumber=AcomDC_0323_2016-->