<properties
   pageTitle="Windows 가상 컴퓨터에서 SAP 사용 | Microsoft Azure"
   description="Microsoft Azure의 Windows VM(가상 컴퓨터)에서 SAP를 사용하는 방법을 알아봅니다."
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="sedusch"/>

# Azure의 Windows 가상 컴퓨터에서 SAP 사용

클라우드 컴퓨팅은 중소 기업에서 대기업 및 다국적 기업까지 IT 업계 내에서 점점 더 중요해지는 널리 사용되는 용어입니다. Microsoft Azure는 다양한 새로운 가능성을 제공하는 Microsoft의 클라우드 서비스 플랫폼입니다. 이제 고객은 클라우드 서비스로 응용 프로그램을 신속하게 프로비전 및 프로비전 해제할 수 있으므로 기술 또는 예산 제한에 제한되지 않습니다. 하드웨어 인프라에 시간과 예산을 투자하는 대신 기업은 고객 및 사용자를 위한 응용 프로그램, 비즈니스 프로세스 및 그 이점에 집중할 수 있습니다.

Microsoft는 Microsoft Azure 가상 컴퓨터와 함께 포괄적인 IaaS(Infrastructure as a Service) 플랫폼을 제공합니다. SAP NetWeaver 기반 응용 프로그램은 Azure 가상 컴퓨터(IaaS)에서 지원됩니다. 아래 백서는 Azure의 Windows 가상 컴퓨터에서 SAP NetWeaver 기반 응용 프로그램을 계획하고 구현하는 방법에 대해 설명합니다. [Linux 가상 컴퓨터](virtual-machines-linux-classic-sap-get-started.md)에서 SAP NetWeaver 기반 응용 프로그램을 구현할 수도 있습니다.

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## Azure에서 SAP NetWeaver - HA

제목: Azure에서 SAP NetWeaver - SIOS Datakeeper를 통해 Azure에서 Windows Server 장애 조치 클러스터를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링

요약: '이 문서에서는 SIOS DataKeeper를 사용하여 Azure에서 고가용성 SAP ASCS/SCS 구성을 설정하는 방법을 설명합니다. SAP는 공유 디스크를 필요로 하는 Windows Server 장애 조치 클러스터 구성으로 SAP ASCS/SCS 또는 Enqueue Replication Services와 같은 오류 구성 요소의 단일 지점을 보호합니다. 이러한 SAP 구성 요소는 SAP 시스템의 기능에 필수적입니다. 따라서 고가용성 기능은 해당 구성 요소가 나금속 및 Hyper-V 환경에 대한 Windows 클러스터 구성으로 실행된 것처럼 서버 또는 VM의 오류를 유지할 수 있는지 확인하기 위해 작동되어야 합니다. 2015년 8월을 기준으로 Azure 자체는 중요한 SAP 구성 요소에 필요한 Windows 기반 고가용성 구성을 위해 필요한 공유 디스크를 제공할 수 없습니다. 그러나 SIOS로 DataKeeper 제품의 도움을 받아 SAP ASCS/SCS의 필요에 따라 Windows Server 장애 조치 클러스터 구성은 Azure IaaS 플랫폼에서 작성할 수 있습니다. 이 문서는 Azure에서 SIOS Datakeeper에서 제공하는 공유 디스크와 Windows Server 장애 조치 클러스터 구성을 설치하는 방법을 단계별 접근 방식으로 설명합니다. 이 문서에서는 고가용성 구성이 최적의 방식으로 작동하는 Azure, Windows와 SAP 측면에서 구성에 대한 세부 정보를 설명합니다. 이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

업데이트 날짜: 2015년 8월

[지금 이 가이드 다운로드](http://go.microsoft.com/fwlink/?LinkId=613056)

<!---HONumber=AcomDC_0824_2016-->