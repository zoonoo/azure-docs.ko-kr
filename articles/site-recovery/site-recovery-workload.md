<properties
	pageTitle="Azure Site Recovery로 어떤 워크로드를 보호할 수 있습니까?" 
	description="Azure Site Recovery는 온-프레미스 가상 컴퓨터 및 물리적 서버의 복제, 장애 조치 및 복구를 Azure 또는 보조 온-프레미스 사이트로 조정하여 워크로드 및 응용 프로그램을 보호합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/01/2015" 
	ms.author="raynew"/>

# Azure Site Recovery로 어떤 워크로드를 보호할 수 있습니까?

Azure Site Recovery를 사용하면 고객이 조직의 비즈니스 연속성 및 재해 복구(BCDR) 전략에 기여하는 응용 프로그램 인식 가용성 솔루션을 배포할 수 있습니다. Windows Server 또는 Linux 기반 앱, Microsoft 엔터프라이즈 응용 프로그램 또는 다른 공급 업체의 제품인지와 관계 없이 Azure Site Recovery를 사용하여 재해 복구, 주문형 개발 및 테스트 환경, 클라우드 마이그레이션을 사용할 수 있습니다.

사이트 복구는 SharePoint, Exchange, Dynamics, SQL Server 및 Active Directory를 포함하여 Microsoft 응용 프로그램을 통합합니다. Microsoft는 Oracle, SAP, IBM 및 Red Hat을 포함한 선두 공급 업체와 긴밀하게 협력하여 Azure 및 Hyper-V와 같은 Microsoft 플랫폼에서 해당 응용 프로그램 및 서비스가 잘 작동하게 합니다. 각 특정 응용 프로그램에 대한 재해 복구 솔루션을 사용자 지정할 수 있습니다.


##주요 기능
응용 프로그램 수준 보호 및 복구 전략에 기여하는 Azure Site Recovery 기능은 다음과 같습니다.

- 가장 중요한 응용 프로그램의 요구를 충족하는 30초의 낮은 RPO를 통한 근거리 동기 복제입니다.
- 단일 또는 N계층 응용 프로그램에 대한 앱 일관성 스냅숏
- AD 복제, SQL AlwaysOn, Exchange 데이터베이스 가용성 그룹(DAG) 및 Oracle Data Guard를 포함하는 다른 응용 프로그램 수준 복제 기술을 사용하여 SQL Server AlwaysOn, 파트너쉽과 통합합니다.
- 유연한 복구는 한 번의 클릭으로 전체 응용 프로그램 스택을 복구하도록 설정하고 외부 스크립트 또는 수동 작업을 포함합니다. 
- 사이트 복구 및 Azure의 고급 네트워크 관리는 낮은 RTO 네트워크 전환을 위해 IP 주소 예약, 부하 분산 장치 구성 또는 Azure 트래픽 관리자의 통합을 포함하는 앱에 대한 네트워크 요구 사항을 단순화합니다.
-  다운로드하고 사이트 복구와 통합할 수 있는 프로덕션 준비된 응용 프로그램 특정 스크립트를 제공하는 다양한 자동화 라이브러리입니다.



##워크로드 요약

사이트 복구 복제 기술은 가상 컴퓨터에서 실행 중인 모든 응용프로그램과 호환됩니다. 또한 각 앱에 추가 지원을 하기 위해 응용 프로그램 제품팀과 협력하여 추가 테스트를 수행했습니다.

**워크로드** | <p>**Hyper-V VM 복제**</p> <p>**(보조 사이트에)**</p> | <p>**Hyper-V VM 복제**</p> <p>**(Azure에)**</p> | <p>**VMware VM 복제**</p> <p>**(보조 사이트에)**</p> | <p>**VMware VM 복제**</p><p>**(Azure에)****</p>---|---|---|---|---Active Directory, DNS | Y | Y | Y | Y 웹앱(IIS, SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y | Y<p>SAP</p><p>비클러스터에 대해 Azure에 SAP 사이트 복제</p>| Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) Exchange(비 DAG) | Y | 서비스 예정 | Y | Y 원격 데스크톱/VDI | Y | Y | Y | 해당 없음 <p>Linux</p> <p>(운영 체제 및 앱)</p>| Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | 서비스 예정 | Y | 서비스 예정 Oracle| Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) | Y(Microsoft에서 테스트) Windows 파일 서버 | Y | Y | Y | Y

##Active Directory 및 DNS 보호

SharePoint, Dynamics AX 및 SAP와 같은 모든 엔터프라이즈 응용 프로그램은 Active Directory 및 DNS 인프라에 따라 달라집니다. BCDR 솔루션의 일부로 워크로드 및 앱을 복구하기 전에 이러한 인프라 구성 요소를 보호하고 복구해야 합니다.

사이트 복구를 사용하여 Active Directory 및 DNS에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 예를 들어 기본 사이트에서 SharePoint 및 SAP와 같은 여러 응용 프로그램에 Active Directory를 사용하고 전체 사이트에 장애 조치하려면 처음으로 복구 계획 사용에 Active Directory가 실패하고 응용 프로그램 특정 복구 계획을 사용하는 Active Directory에 의존하는 응용 프로그램을 장애 조치할 수 있습니다.

[자세한 정보](http://aka.ms/asr-ad)

##SQL Server 보호

SQL Server는 온-프레미스 데이터 센터에서 많은 비즈니스 응용 프로그램에 데이터 서비스에 대 한 기반을 제공합니다. 사이트 복구 및 SQL Server HA/DR 기술은 상호 보완적이며 다중 계층 엔터프라이즈 응용 프로그램에 대한 종단간 보호를 제공하는 데 함께 사용할 수 있습니다. 사이트 복구는 SQL Server 환경에 대한 다음 혜택을 제공합니다.

- Azure 또는 보조 사이트에 대한 독립 실행형 또는 클러스터된 SQL 서버를 쉽게 보호 및 복제. 여러 SQL Server 버전과 에디션에 대해 간단하고 비용 효율적인 재해 복구 솔루션을 제공합니다.
- SQL AlwaysOn 가용성 그룹 통합은 Azure Site Recovery에서 복구 계획을 통해 장애 조치 관리 및 장애 복구 처리를 관리합니다.
- SQL Server 데이터베이스를 포함한 전체 응용 프로그램 스택에 대한 종단간 복구 계획.
- Azure에서 더 큰 IaaS 가상 컴퓨터로 "폭발적으로 증가"하여 Azure Site Recovery를 사용하는 최대 부하에 대한 SQL Server의 규모 증가.
- Azure Site Recovery를 사용하여 Azure 또는 보조 사이트에서 SQL Server 테스트. 테스트 장애 조치는 프로덕션 환경에 영향을 주지 않고 분석 데이터 규정 준수 검사에 사용될 수 있습니다.

[자세히 알아보기](http://aka.ms/asr-sql)

##SharePoint 보호

Azure Site Recovery를 사용하면 SharePoint 배포를 보호할 수 있습니다. 사이트 복구를 통해 다음을 수행할 수 있습니다.

- 재해 복구를 위한 대기 팜에 대한 필요성 및 관련된 인프라 비용을 제거합니다. 사이트 복구을 사용하면 Azure 또는 보조 사이트에 대한 전체 팜(웹, 응용 프로그램 및 데이터베이스 계층)의 보호를 설정할 수 있습니다.
- 응용 프로그램 배포 및 관리 효율성을 간소화합니다. 팜이 보조 사이트에서 복구되면 기본 사이트에 배포된 업데이트는 자동으로 복제되고 사용할 수 있습니다. 낮은 비용에 대기 팜을 최신으로 유지하는 관리 복잡성을 제거합니다.
- 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형 복사본 환경을 만들어 SharePoint 응용 프로그램 개발 및 테스트를 용이하게 합니다.
- 사이트 복구를 사용하여 SharePoint 배포를 Azure에 마이그레이션하여 전환을 클라우드로 단순화합니다.

[자세히 알아보기](http://aka.ms/asr-sharepoint)


## Dynamics AX 보호

Azure Site Recovery를 사용하면 Dynamics AX ERP 솔루션을 보호하도록 합니다.

- 사이트 복구를 사용하여 Azure 또는 보조 사이트에 대한 전체 Dynamics AX 환경(웹 및 AOS 계층, 데이터베이스 계층, SharePoint)의 보호를 설정할 수 있습니다.
- 사이트 복구를 사용하여 Dynamics AX 배포를 Azure에 마이그레이션하여 마이그레이션을 클라우드로 단순화합니다.
- 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형을 만들어 Dynamics AX 응용 프로그램 개발 및 테스트를 용이하게 합니다.

[자세히 알아보기](http://aka.ms/asr-dynamics)

## RDS 보호 
원격 데스크톱 서비스는 사용자가 원하는 위치에서 사용할 수 있도록 가상 데스크톱 인프라(VDI), 세션 기반의 데스크톱 및 응용 프로그램을 활성화합니다. 사이트 복구를 사용하여 보조 사이트에 대한 관리되거나 관리되지 않은 풀링된 가상 데스크톱 및 보조 사이트 또는 Azure에 대한 원격 응용 프로그램 및 세션의 보호를 설정할 수 있습니다.

[자세히 알아보기](http://aka.ms/asr-rds)


## Exchange 보호

Microsoft Exchange는 고가용성 및 재해 복구에 대한 기본 제공 지원을 포함합니다. Exchange DAG 및 Azure Site Recovery는 함께 작동될 수 있습니다.

- Exchange DAG는 엔터프라이즈에서 Exchange 재해 복구를 위한 권장된 솔루션입니다. 사이트 복구의 복구 계획은 DAG를 포함하여 사이트에 걸쳐 DAG 장애 조치를 오케스트레이션할 수 있습니다.
- 단일 또는 클러스터되지 않은 서버와 같은 소규모 배포의 경우 사이트 복구는 Azure 또는 보조 사이트에 보호 및 장애 조치가 가능합니다.

[수익 창출](http://aka.ms/asr-exchange)

## SAP 보호

사이트 복구를 사용하여 SAP 배포를 보호합니다.

- Azure 또는 보조 사이트에 대해 다른 계층으로 전체 SAP 배포의 보호를 설정할 수 있습니다.
- 사이트 복구를 사용하여 클라우드 마이그레이션을 간소화하여 Azure에 SAP 배포를 마이그레이션합니다.
- 테스트 및 디버깅 응용 프로그램에 프로덕션 환경과 유사한 복사본 주문형을 만들어 SAP 개발 및 테스트를 용이하게 합니다.

[자세히 알아보기](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_1203_2015-->