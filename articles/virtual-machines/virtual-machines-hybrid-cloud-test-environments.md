<properties
	pageTitle="Azure에서 하이브리드 클라우드 테스트 환경 | Microsoft Azure"
	description="Azure 기반 하이브리드 클라우드에 개발/테스트 또는 개념 증명 IT 전문가 환경을 구축하는 방법을 설명하는 문서를 찾습니다."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="09/16/2015"
	ms.author="josephd"/>

# Azure 하이브리드 클라우드 테스트 환경

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

개발/테스트 또는 개념 증명을 위해 하이브리드 클라우드 테스트 환경에서는 로컬 인터넷 연결 및 공용 IP 주소 중 하나를 사용하고 작동하는 프레미스 간 Azure VNet(가상 네트워크) 설정을 단계별로 안내합니다. 가져오기가 완료되면 응용 프로그램을 개발 및 테스트하고, 간단한 IT 워크로드를 실험하고, 현재 인터넷 위치를 기준으로 사이트 간 VPN(가상 사설망) 연결 성능을 측정할 수 있습니다.

## 하이브리드 클라우드 기본 구성

[하이브리드 클라우드 기본 구성](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)은 다음과 같습니다.

- 네 개의 가상 컴퓨터(도메인 컨트롤러, 응용 프로그램 서버, 클라이언트 컴퓨터 및 Windows 서버와 라우팅 및 원격 액세스를 실행하는 VPN 장치)가 있는 간단한 온-프레미스 네트워크
- 복제본 도메인 컨트롤러가 있는 Azure 가상 네트워크
- 사이트 간 VPN 연결

## 하이브리드 클라우드의 SharePoint 인트라넷 팜

[하이브리드 클라우드 테스트 환경의 SharePoint 인트라넷 팜](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)은 SQL Server 2014 서버와 SharePoint Server 2013 서버를 하이브리드 클라우드 기본 구성에 추가합니다. 따라서 간단한 온-프레미스 네트워크의 클라이언트 컴퓨터에서 액세스할 수 있는 2계층 SharePoint 팜이 만들어집니다.

## 하이브리드 클라우드의 웹 기반 LOB(기간 업무) 응용 프로그램

[하이브리드 클라우드 테스트 환경의 웹 기반 LOB 응용 프로그램](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)은 SQL Server 2014 서버와 IIS(인터넷 정보 서비스) 서버를 하이브리드 클라우드 기본 구성에 추가합니다. 따라서 계층화된 웹 기반 LOB 응용 프로그램을 배포하고 테스트할 수 있는 인프라가 만들어집니다.

## 하이브리드 클라우드의 Office 365 디렉터리 동기화(DirSync) 서버

[하이브리드 클라우드 테스트 환경의 Office 365 DirSync 서버](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md)는 DirSync 서버를 하이브리드 클라우드 기본 구성에 추가하고 평가판 Office 365 구독에 대한 암호 동기화를 사용하여 Office 365 DirSync를 보여 줍니다.

## 시뮬레이션된 하이브리드 클라우드 테스트 환경

쉽게 인터넷에 직접 연결할 수 없고 공용 IP 주소를 사용할 수 없는 조직 및 개인을 위해 [시뮬레이션된 하이브리드 클라우드 테스트 환경](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)에서는 별도의 Azure 가상 네트워크에 간단한 온-프레미스 네트워크를 구축한 다음 VNet 간 VPN 연결을 사용하여 두 가상 네트워크를 연결합니다.


## 추가 리소스

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[Azure 인프라 서비스 워크로드: 고가용성 LOB(기간 업무) 응용 프로그램](virtual-machines-workload-high-availability-LOB-application.md)

[Microsoft Azure에서 Office 365 디렉터리 동기화(DirSync) 배포](https://technet.microsoft.com/library/dn635310.aspx)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=Sept15_HO3-->