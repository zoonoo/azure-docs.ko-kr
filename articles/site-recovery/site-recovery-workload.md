<properties
	pageTitle="사이트 복구 워크로드 지침 | Microsoft Azure" 
	description="Azure Site Recovery는 온-프레미스에 있는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치 및 복구를 Azure 또는 보조 온-프레미스 사이트로 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/21/2015" 
	ms.author="pratshar"/>

# 사이트 복구 워크로드 지침

Azure 사이트 복구를 통해 고객은 응용 프로그램 인식 가용성 솔루션을 배포할 수 있습니다. Windows Server 또는 Linux 기반 응용 프로그램, Microsoft 자사 엔터프라이즈 응용 프로그램 또는 다른 공급 업체의 제품 이도록 하고 Azure 사이트 복구를 사용하여 재해 복구, 주문형 개발/테스트 환경 또는 클라우드 마이그레이션을 활성화할 수 있습니다.

Microsoft는 SharePoint, Exchange, Dynamics, SQL Server 및 Active Directory와 같은 엔터프라이즈급 응용 프로그램에서 풍부한 전문성과 최고의 개발 경험을 가지고 있습니다. Microsoft는 Oracle, SAP, IBM 및 Red Hat을 포함한 선두 공급 업체와 긴밀하게 협력하여 Azure 및 Hyper-V를 포함한 Microsoft 플랫폼에서 해당 응용 프로그램 및 서비스가 잘 작동하게 합니다. 이러한 고유 장점을 활용하여 각 응용 프로그램의 가용성 요구 사항을 깊이 이해하여 고객이 각 응용 프로그램에서 사용자 지정할 수 있는 최상급 재해 복구 및 가용성 솔루션을 배포할 수 있도록 합니다.


##주요 기능
Azure 사이트 복구 기능은 응용 프로그램 수준 보호/복구를 염두에 두고 설계되었습니다.

- 가장 중요한 응용 프로그램의 요구를 충족하는 30초의 낮은 RPO와 가까운 동기 복제입니다.
- 단일 또는 N계층 응용 프로그램에 대한 응용 프로그램 일관성 스냅숏
- 응용 프로그램 수준 복제와 통합됩니다. AD 복제, SQL Always On, Exchange 데이터베이스 가용성 그룹 및 Oracle 데이터 가드를 포함한 최상급 응용 프로그램 수준 기능 활용
- 유연한 복구 계획 복구를 통해 외부 스크립트 또는 수동 작업 실행을 포함하여 한 번의 클릭으로 전체 응용 프로그램 스택을 복구하도록 설정합니다. 
- ASR 및 Azure의 고급 네트워크 관리는 응용 프로그램에 해당되는 모든 네트워킹 구성을 자동화합니다. IP 주소 예약, 부하 분산 장치 구성 또는 낮은 RTO 네트워크 스위치 오버에 대한 Microsoft의 트래픽 관리자 사용
- 프로덕션 수준의 응용 프로그램 특정 스크립트를 제공하는 풍부한 자동화 라이브러리입니다. 다운로드하고 ASR 기반 솔루션에 통합합니다.


##워크로드 지침 요약

ASR 복제 기술은 가상 컴퓨터에서 실행 중인 모든 응용프로그램과 호환됩니다. 각 응용프로그램에 추가 지원을 하기 위해 응용프로그램 제품팀과 협력하여 추가 테스트를 수행했습니다.

**워크로드** | <p>**Hyper-V 가상 컴퓨터 복제**</p> <p>**(보조 사이트에)**</p> | <p>**Hyper-V 가상 컴퓨터 복제**</p> <p>**(Azure에)**</p> | <p>**VMware 가상 컴퓨터 복제**</p> <p>**(보조 사이트에)**</p> | <p>**VMware 가상 컴퓨터 복제**</p><p>**(Azure에)****</p>---|---|---|---|---Active Directory, DNS | Y | Y | Y | Y 웹앱(IIS, SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y | Y<p>SAP</p><p>비클러스터에 대해 Azure에 SAP 사이트 복제</p>| Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) Exchange(비 DAG) | Y | 서비스 예정 | Y | Y 원격 데스크톱/VDI | Y | Y | Y | 해당 없음 <p>Linux</p> <p>(운영 체제 및 앱)</p>| Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | 서비스 예정 | Y | 서비스 예정 Oracle| Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) Windows 파일 서버 | Y | Y | Y | Y

##Active Directory 및 DNS

SharePoint, Dynamics AX 및 SAP와 같은 모든 엔터프라이즈 응용 프로그램은 올바르게 작동하기 위해 AD 및 DNS 인프라에 따라 달라집니다. 이러한 응용 프로그램에 대한 재해 복구(DR) 솔루션을 만드는 동안 응용 프로그램의 다른 구성 요소에 중단이 발생하기 전에 AD를 보호하고 복구하는 것이 중요합니다.

Azure 사이트 복구를 사용하여 AD에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 기본 사이트에 SharePoint 및 SAP와 같은 여러 응용 프로그램에 대한 AD가 있고 전체 사이트 장애 조치가 가능하도록 결정하는 경우 먼저 AD 복구 계획을 사용하여 AD를 장애 조치한 다음 응용 프로그램 특정 복구 계획을 사용하여 다른 응용 프로그램을 장애 조치할 수 있습니다.

[AD에 대한 Azure 사이트 복구 배포](http://aka.ms/asr-ad)에 대한 자세한 지침에 연결된 문서를 참조하세요.

##SQL Server
Microsoft SQL Server는 고객의 온-프레미스 데이터 센터 내에서 실행하는 많은 엔터프라이즈 수준의 자사, 타사 및 비즈니스 응용 프로그램의 사용자 지정 선에 대한 기초입니다. SharePoint, Dynamics 및 SAP과 같은 응용프로그램은 SQL Server를 사용하여 데이터 서비스를 제공합니다. Azure 사이트 복구 및 SQL Server HA/DR 기술은 상호 보완적이며 다중 계층 엔터프라이즈 응용 프로그램에 대한 종단간 보호를 제공하는데 사용할 수 있습니다. Azure 사이트 복구는 SQL Server 환경에 대한 다음 혜택을 제공합니다.

- Azure 또는 보조 사이트에 대한 독립 실행형 또는 클러스터된 SQL 서버를 손쉽게 보호합니다. SQL Server의 모든 버전 및 에디션에 대한 간단하고 비용 효율적인 DR 솔루션을 확장합니다.
- SQL Always On 가용성 그룹, 최상급 재해 복구 솔루션과 통합하고 ASR 복구 계획을 사용하여 장애 조치/장애 복구 작업을 관리합니다.
- SQL 데이터베이스를 포함한 전체 응용 프로그램 스택에 대한 종단간 복구 계획을 만듭니다.
- Azure 사이트 복구를 사용하여 Azure에서 더 큰 IaaS VM 크기로 “전환"하여 최대 로드 중에 SQL Server를 강화합니다.
- Azure 사이트 복구를 사용하여 Azure 또는 보조 사이트에서 SQL Server의 테스트 복사본을 만듭니다. 프로덕션 환경에 영향을 주지 않고 분석 및 데이터 규정 준수 검사에 대한 이 복사본을 사용합니다.

[SQL에 대한 Azure 사이트 복구 배포](http://aka.ms/asr-sql)에 대한 자세한 지침에 연결된 문서를 참조하세요.

##SharePoint
SharePoint는 널리 사용되는 응용 프로그램으로 인트라넷 포털, 문서 및 파일 관리, 소셜 네트워크, 웹 사이트 및 엔터프라이즈 검색 기능을 제공하여 조직이 공동 작업을 수행할 수 있도록 합니다. 사용자 지정 응용 프로그램 및 워크플로를 쉽게 배포하기 위한 응용 프로그램 플랫폼이기도 합니다. SharePoint에 대한 Azure 사이트 복구를 사용하여 다음 작업을 수행할 수 있습니다.

- 재해 복구를 위한 대기 팜에 대한 필요성 및 관련된 인프라 비용을 제거합니다. ASR을 사용하면 Azure 또는 보조 사이트에 대한 전체 팜(웹, 응용 프로그램 및 데이터베이스 계층)의 보호를 설정할 수 있습니다.
- 응용 프로그램 배포 및 관리 효율성을 간소화합니다. 팜이 보조 사이트에서 복구되면 기본 사이트에 배포된 업데이트는 자동으로 복제되고 사용할 수 있습니다. 대기 팜을 최신으로 유지하는 관리 복잡성을 제거하고 TCO를 절감합니다.
- 프로덕션 환경과 유사한 복사본 주문형 테스트 및 디버깅에 대한 프로덕션 환경과 유사한 복사본 주문형을 만들어 SharePoint 응용 프로그램 개발 및 테스트를 용이하게 합니다.
- ASR을 사용하여 SharePoint 배포를 Azure에 마이그레이션하여 경로를 클라우드로 단순화합니다.

[Sharepoint에 대한 Azure 사이트 복구 배포](http://aka.ms/asr-sharepoint)에 대한 자세한 지침에 연결된 문서를 참조하세요.


##Dynamics AX
Microsoft Dynamics AX는 배우고 사용하기 쉬워 가치를 신속하게 제공하고 비즈니스 기회를 활용하고 조직 전체에 걸친 사용자 참여 및 혁신을 이끌 수 있는 선두적인 엔터프라이즈 리소스 계획(ERP) 솔루션입니다.

- ASR을 사용하면 Azure 또는 보조 사이트에 대한 전체 Dynamics Ax(웹 및 AOS 계층, 데이터베이스 계층, SharePoint)의 보호를 설정할 수 있습니다.
- ASR을 사용하여 Dynamics Ax 배포를 Azure에 마이그레이션하여 경로를 클라우드로 단순화합니다.
- 프로덕션 환경과 유사한 복사본 주문형 테스트 및 디버깅에 대한 프로덕션 환경과 유사한 복사본 주문형을 만들어 Dynamics 응용 프로그램 개발 및 테스트를 용이하게 합니다.

[Dynamics AX에 대한 Azure 사이트 복구 배포](http://aka.ms/asr-dynamics)에 대한 자세한 지침에 연결된 문서를 참조하세요.

## RDS 
Windows Server 원격 데스크톱 서비스(RDS)는 중요한 지적 재산 보안을 유지하고 규정 준수를 간소화하면서 원격 작업자의 효율성을 개선하여 모든 장치에 대한 데스크톱 및 응용 프로그램 배포를 가속화하고 확장합니다. 원격 데스크톱 서비스는 사용자가 원하는 위치에서 사용할 수 있도록 가상 데스크톱 인프라(VDI), 세션 기반의 데스크톱 및 응용 프로그램을 활성화합니다.

ASR을 사용하여 보조 사이트에 대한 관리되거나 관리되지 않은 풀링된 가상 데스크톱 및 보조 사이트 또는 Azure에 대한 원격 응용 프로그램 및 세션의 보호를 설정할 수 있습니다.

[RDS/VDI에 대한 Azure 사이트 복구 배포](http://aka.ms/asr-rds)에 대한 자세한 지침에 연결된 문서를 참조하세요.


## Exchange
Microsoft Exchange는 기업에서 메시징 및 전자 메일 서비스를 호스팅하도록 사용되는 기본 설정 소프트웨어입니다. Exchange는 최고의 안정성, 관리 효율성 및 데이터 보호를 제공하면서 PC, 전화 또는 브라우저를 통한 통신에 대한 액세스를 유지합니다.

Microsoft Exchange는 기본적으로 엔터프라이즈급 고가용성 및 재해 복구 솔루션을 지원합니다. Exchange 데이터베이스 가용성 그룹 및 Azure 사이트 복구 기술은 상호 보완됩니다.

- Exchange DAG는 Exchange 배포에 대한 최상급 재해 복구를 사용할 수 있도록 권장되는 배포 옵션입니다. ASR 복구 계획은 DAG를 포함하여 사이트에 걸쳐 DAG 장애 조치를 오케스트레이션할 수 있습니다.
- 단일 서버 또는 클러스터되지 않은 서버와 같은 소규모 배포의 경우 Azure 사이트 복구는 Azure 또는 보조 사이트에 대한 개별 서버를 보호 및 장애 조치할 수 있습니다.

[Exchange에 대한 Azure 사이트 복구 배포](http://aka.ms/asr-exchange)에 대한 자세한 지침에 연결된 문서를 참조하세요.

## SAP

SAP는 전세계 많은 조직에서 사용되는 선두적인 엔터프라이즈 리소스 계획(ERP) 소프트웨어입니다. SAP는 기업 내에서 중요 업무용 응용 프로그램 중 하나로 보는 경우가 많습니다. 이러한 응용 프로그램의 아키텍처 및 작업은 대부분 매우 복잡하고 BCDR에 대한 요구를 충족하는 것이 매우 중요합니다.

- ASR을 사용하면 Azure 또는 보조 사이트에 대해 다른 계층으로 전체 SAP 배포의 보호를 설정할 수 있습니다.
- ASR을 사용하여 SAP 배포를 Azure에 마이그레이션하여 경로를 클라우드로 단순화합니다.
- 프로덕션 환경과 유사한 복사본 주문형 테스트 및 디버깅에 대한 프로덕션 환경과 유사한 복사본 주문형을 만들어 SAP 응용 프로그램 개발 및 테스트를 용이하게 합니다.

[SAP NetWeaver에 대한 Azure 사이트 복구 배포](http://aka.ms/asr-sap)에 대한 자세한 지침에 연결된 문서를 참조하세요.

<!---HONumber=Oct15_HO3-->