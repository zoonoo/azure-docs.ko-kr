<properties 
	pageTitle="LOB(기간 업무) 응용 프로그램 배포 | Microsoft Azure" 
	description="다섯 단계를 통해 Azure의 SQL Server AlwaysOn 가용성 그룹으로 웹 기반 고가용성 LOB(기간 업무) 응용 프로그램을 배포합니다." 
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
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="josephd"/>

# 고가용성 LOB(기간 업무) 응용 프로그램 배포

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

이 항목에는 Azure 인프라 서비스에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 고가용성 인트라넷 전용 웹 기반 LOB(기간 업무) 응용 프로그램을 배포하는 단계별 지침에 대한 링크가 포함되어 있습니다. 응용 프로그램은 다음 컴퓨터에서 호스팅됩니다.

- 웹 서버 두 대
- 데이터베이스 서버 두 대
- 클러스터 주 노드 서버 한 대
- 도메인 컨트롤러 두 개

이 구성은 다음과 같습니다. 각 서버에는 자리 표시자 이름이 표시됩니다.

![](./media/virtual-machines-windows-lob-overview/workload-lobapp-phase4.png)
 
각 역할에 대해 컴퓨터가 두 대 이상이므로 고가용성이 보장됩니다. 모든 가상 컴퓨터가 단일 Azure 위치(지역이라고도 함)에 있습니다. 특정 역할에 대한 각 가상 컴퓨터 그룹은 자체 가용성 집합에 있습니다.

## 제품 구성 정보

이 기본 구성을 사용하려면 다음과 같은 Azure 서비스 및 구성 요소 집합이 필요합니다.

- 가상 컴퓨터 7대
- 도메인 컨트롤러 및 SQL 서버를 실행하는 가성 컴퓨터용 추가 데이터 디스크 4개
- 가용성 집합 3개
- 프레미스 간 가상 네트워크 1개
- 두 저장소 계정

가상 컴퓨터와, 이 구성에 해당하는 크기 기본값은 다음과 같습니다.

항목 | 가상 컴퓨터 설명 | 갤러리 이미지 | 기본 크기 
--- | --- | --- | --- 
1\. | 최초 도메인 컨트롤러 | Windows Server 2012 R2 Datacenter | D1
2\. | 보조 도메인 컨트롤러 | Windows Server 2012 R2 Datacenter | D1
3\. | 주 데이터베이스 서버 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
4\. | 보조 데이터베이스 서버 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
5\. | 클러스터에 대한 주 노드 | Windows Server 2012 R2 Datacenter | D1
6\. | 최초 웹 서버 | Windows Server 2012 R2 Datacenter | D3
7\. | 보조 웹 서버 | Windows Server 2012 R2 Datacenter | D3

이 구성에 대한 추정 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요.

1. 필요한 만큼 **모듈**에서 **계산**을 클릭하고 **가상 컴퓨터**를 클릭하여 7개 가상 컴퓨터의 목록을 만듭니다.
2. 각 가상 컴퓨터에 대해 다음을 선택합니다.
	- 원하는 지역
	- 유형에 관한 **Windows**
	- 가격 책정 계층에 관한 **표준**
	- 이전 테이블의 기본 크기 또는 원하는 **인스턴스 크기**

> [AZURE.NOTE] Azure 가격 계산기에는 SQL Server 2014 Enterprise에서 실행되는 두 대의 가상 컴퓨터의 SQL Server 라이선스에 대한 추가 비용이 포함되지 않습니다. 자세한 내용은 [가상 컴퓨터 가격 책정 - SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)을 참조하세요.

## 배포 단계

다음 단계를 통해 이 구성을 배포합니다.

- [1단계: Azure 구성](virtual-machines-windows-ps-lob-ph1.md). 저장소 계정, 가용성 집합 및 크로스-프레미스 가상 네트워크를 만들 수 있습니다.
- [2단계: 도메인 컨트롤러 구성](virtual-machines-windows-ps-lob-ph2.md). 복제본 AD DS(Active Directory 도메인 서비스) 도메인 컨트롤러를 만들고 구성합니다.
- [3단계: SQL Server 인프라 구성](virtual-machines-windows-ps-lob-ph3.md). SQL Server를 실행하는 가상 컴퓨터를 만들어 구성하고, 클러스터를 만들고, SQL Server AlwaysOn 가용성 그룹을 사용하도록 설정합니다.
- [4단계: 웹 서버 구성](virtual-machines-windows-ps-lob-ph4.md). 웹 서버 가상 커퓨터 2대를 만들고 구성합니다.
- [5단계: SQL Server AlwaysOn 가용성 그룹에 응용 프로그램 데이터베이스 추가](virtual-machines-windows-ps-lob-ph5.md). LOB(기간 업무) 응용 프로그램 데이터베이스를 준비하여 SQL Server AlwaysOn 가용성 그룹에 추가합니다.

이 배포는 [LOB(기간 업무) 응용 프로그램 아키텍처 청사진](http://msdn.microsoft.com/dn630664)과 함께 제공되며 최신 권장 사항을 통합합니다.

규범적이고 미리 정의된 아키텍처입니다. 다음 사항을 고려하세요.

- 웹 기반 LOB(기간 업무) 응용 프로그램 구현 작업에 익숙한 경우 3~5단계의 지침을 조정하여 요구 사항에 가장 적합한 응용 프로그램 인프라를 구축할 수 있습니다. 
- 기존 Azure 하이브리드 클라우드 구현이 이미 있는 경우 1~2단계의 지침을 조정하거나 건너뛰어 적절한 서브넷에서 새 가상 컴퓨터를 호스팅할 수 있습니다.
- 모든 서버는 Azure 가상 네트워크에서 단일 서브넷에 있습니다. 서브넷 격리에 해당하는 추가 보안을 제공하려는 경우 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)을 사용할 수 있습니다.

이 구성의 개념 증명 또는 개발/테스트 환경을 구축하려면 [테스트를 위한 하이브리드 클라우드에서 웹 기반 LOB 응용 프로그램 설정](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)을 참조하세요.

Azure에 대한 IT 워크로드를 디자인하는 방법에 대한 자세한 내용은 [Azure 인프라 서비스 구현 지침](virtual-machines-linux-infrastructure-service-guidelines.md)을 참조하세요.

## 다음 단계

이 작업의 구성을 시작하려면 [1단계: Azure 구성](virtual-machines-windows-ps-lob-ph1.md)으로 진행하세요.

<!---HONumber=AcomDC_0323_2016-->