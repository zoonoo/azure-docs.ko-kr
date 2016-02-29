<properties
   pageTitle="Azure 가상 컴퓨터(VM)에서 SAP 사용 | Microsoft Azure"
   description="Azure 가상 컴퓨터(VM)에서 SAP 사용"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="sedusch"/>
   
# Azure 가상 컴퓨터(VM)에서 SAP 사용

클라우드 컴퓨팅은 중소 기업에서 대기업 및 다국적 기업까지 IT 업계 내에서 점점 더 중요해지는 널리 사용되는 용어입니다. Microsoft Azure는 다양한 새로운 가능성을 제공하는 Microsoft의 클라우드 서비스 플랫폼입니다. 이제 고객은 클라우드 서비스로 응용 프로그램을 신속하게 프로비전 및 프로비전 해제할 수 있으므로 기술 또는 예산 제한에 제한되지 않습니다. 하드웨어 인프라에 시간과 예산을 투자하는 대신 기업은 고객 및 사용자를 위한 응용 프로그램, 비즈니스 프로세스 및 그 이점에 집중할 수 있습니다.

Microsoft Azure 가상 컴퓨터 서비스와 함께 Microsoft는 포괄적인 IaaS(Infrastructure as a Service) 플랫폼을 제공합니다. SAP NetWeaver 기반 응용 프로그램은 Azure 가상 컴퓨터(IaaS)에서 지원됩니다. 아래 백서는 선택한 플랫폼으로 Microsoft Azure 내에서 SAP NetWeaver 기반 응용 프로그램을 계획하고 구현하는 방법에 대해 설명합니다.

## 계획 및 구현

제목: Azure 가상 컴퓨터에서 SAP NetWeaver - 계획 및 구현 가이드

요약: Azure 가상 컴퓨터에서 SAP NetWeaver를 실행하는 것을 고려하는 경우 시작하기 위한 문서입니다. 이 계획 및 구현 가이드를 사용하면 기존 또는 계획된 SAP NetWeaver 기반 시스템을 Azure 가상 컴퓨터 환경에 배포할 수 있는지 여부를 평가할 수 있습니다. 여러 SAP NetWeaver 배포 시나리오를 다루며 Azure에 고유한 SAP 구성을 포함합니다. 문서는 SAP/Azure 측면에서 하이브리드 SAP 환경을 실행하는 데 필요한 모든 구성 정보를 나열하고 설명합니다. IaaS에서 SAP NetWeaver 기반 시스템의 고가용성을 보장하기 위해 수행할 수 있는 측정값에 대해서도 다룹니다.

업데이트 날짜: 2015년 8월

[지금 이 가이드 다운로드](http://go.microsoft.com/fwlink/?LinkId=397963)
## 배포
제목: Azure 가상 컴퓨터에서 SAP NetWeaver - 배포 가이드

요약: 이 문서에서는 Azure의 가상 컴퓨터에 SAP NetWeaver 소프트웨어를 배포하기 위한 절차 지침을 제공합니다. 이 문서에서는 SAP용 Azure 모니터링 확장에 대한 권장 문제 해결 방법을 비롯한 SAP용 Azure 모니터링 확장 사용을 중점적으로 세 가지 특정 배포 시나리오에 중점을 둡니다. 이 문서에서는 계획 및 구현 가이드를 읽은 것을 가정합니다.

업데이트 날짜: 2015년 9월

[지금 이 가이드 다운로드](http://go.microsoft.com/fwlink/?LinkId=397964)

## Azure에서 SAP DBMS
제목: Azure 배포 가이드의 SAP DBMS

요약: 이 문서에서는 SAP와 함께 실행해야 하는 DBMS 시스템에 대한 계획 및 구현 고려 사항을 살펴봅니다. 첫 번째 부분에서는 일반적인 고려 사항이 나열되고 표시됩니다. 문서의 다음 부분은 SAP에서 지원되는 Azure의 여러 DBMS 배포와 관련되어 있습니다. 제공된 다른 DBMS는 Linux, Unix 및 Windows용 SQL Server, SAP ASE, Oracle, SAP MaxDB 및 IBM DB2입니다. 해당 특정 부분 고려 사항에서 설명된 DBMS와 함께 Azure에서 SAP를 실행 중인 경우에 대해 설명해야 합니다. Azure의 각 DMS에서 지원되는 백업 및 고가용성 방법과 같은 주제가 SAP 응용 프로그램과 함께 사용하기 위해 표시됩니다.

업데이트한 날짜: 2015년 12월

[지금 이 가이드 다운로드](http://go.microsoft.com/fwlink/?LinkId=397965)

## Azure에서 SAP NetWeaver

제목: SAP NetWeaver - Azure 기반 재해 복구 솔루션 빌드

요약: 이 문서는 SAP NetWeaver용 Azure 기반 재해 복구 솔루션 빌드에 대한 단계별 지침을 제공합니다. 설명된 솔루션은 SAP 환경이 Hyper-V에 따라 가상화된 온-프레미스를 실행하고 있는 것을 가정합니다. 문서의 첫 번째 부분에서 ASR(Azure Site Recovery) 서비스가 해당 구성 요소에서 도입됩니다. 문서의 두 번째 부분에서는 SAP NetWeaver 기반 환경에 대한 세부 사항을 설명합니다. SAP NetWeaver 응용 프로그램 인스턴스 및 SAP Central Services와 함께 ASR 사용에 대한 가능성을 소개하고 설명합니다. 두 번째 부분의 초점은 Windows Server 장애 조치 클러스터 구성을 통해 보호되는 SAP Central Services에 대한 ASR 활용입니다.

업데이트 날짜: 2015년 9월

[지금 이 가이드 다운로드](http://go.microsoft.com/fwlink/?LinkID=521971)

## Azure에서 SAP NetWeaver - HA

제목: Azure에서 SAP NetWeaver - SIOS Datakeeper를 통해 Azure에서 Windows Server 장애 조치 클러스터를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링

요약: '이 문서에서는 SIOS DataKeeper를 사용하여 Azure에서 고가용성 SAP ASCS/SCS 구성을 설정하는 방법을 설명합니다. SAP는 공유 디스크를 필요로 하는 Windows Server 장애 조치 클러스터 구성으로 SAP ASCS/SCS 또는 Enqueue Replication Services와 같은 오류 구성 요소의 단일 지점을 보호합니다. 이러한 SAP 구성 요소는 SAP 시스템의 기능에 필수적입니다. 따라서 고가용성 기능은 해당 구성 요소가 나금속 및 Hyper-V 환경에 대한 Windows 클러스터 구성으로 실행된 것처럼 서버 또는 VM의 오류를 유지할 수 있는지 확인하기 위해 작동되어야 합니다. 2015년 8월을 기준으로 Azure 자체는 중요한 SAP 구성 요소에 필요한 Windows 기반 고가용성 구성을 위해 필요한 공유 디스크를 제공할 수 없습니다. 그러나 SIOS로 DataKeeper 제품의 도움을 받아 SAP ASCS/SCS의 필요에 따라 Windows Server 장애 조치 클러스터 구성은 Azure IaaS 플랫폼에서 작성할 수 있습니다. 이 문서는 Azure에서 SIOS Datakeeper에서 제공하는 공유 디스크와 Windows Server 장애 조치 클러스터 구성을 설치하는 방법을 단계별 접근 방식으로 설명합니다. 이 문서에서는 고가용성 구성이 최적의 방식으로 작동하는 Azure, Windows와 SAP 측면에서 구성에 대한 세부 정보를 설명합니다. 이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

업데이트 날짜: 2015년 8월

[지금 이 가이드 다운로드](http://go.microsoft.com/fwlink/?LinkId=613056)

## Azure SUSE Linux 가상 컴퓨터에서 SAP NetWeaver

제목: Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트

요약: 현 시점에는 Azure Linux VM에서의 SAP NetWeaver 실행에 대해 SAP에서 공식적으로 지원하지 않습니다. 그렇기는 하지만 고객이 SAP 지원에 문의할 필요가 없다면 Azure Linux VM에서 일부 테스트를 수행하거나 SAP 데모 또는 교육 시스템 실행을 고려할 수 있습니다. 이 문서는 SAP를 실행할 수 있도록 Azure SUSE Linux VM을 설정하는 데 도움을 주며 일반적으로 일어날 수 있는 위험을 방지하기 위한 몇 가지 기본적인 힌트를 제공합니다.

업데이트한 날짜: 2015년 12월

[이 문서는 여기서 확인할 수 있습니다.](virtual-machines-sap-on-linux-suse-quickstart.md)

<!---HONumber=AcomDC_0218_2016-->