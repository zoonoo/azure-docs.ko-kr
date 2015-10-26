<properties 
	pageTitle="SQL Server 및 Azure Site Recovery를 사용한 재해 복구" 
	description="Azure Site Recovery는 보조 온-프레미스 사이트 또는 Azure에 SQL Server의 복제, 장애 조치 및 복구를 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="raynew"/>


# SQL Server 및 Azure Site Recovery를 사용한 재해 복구 

사이트 복구는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 사이트 복구는 일관된 보호를 위해 다양한 복제 메커니즘을 지원하며, Azure 또는 보조 데이터센터로 컴퓨터를 복제 및 장애 조치합니다. [Azure Site Recovery 개요](site-recovery-overview.md)에서 모든 배포 시나리오의 개요를 참조하세요.

 이 문서에서는 SQL Server BCDR 기술 및 사이트 복구를 조합하 여 응용프로그램의 SQL Server 백엔드를 보호하는 방법을 설명합니다. 이 문서에 설명된 시나리오를 배포하기 전에 SQL Server BCDR 기능(장애 조치 클러스터링, AlwaysOn 가용성 그룹, 데이터베이스 미러링, 로그 전달) 및 사이트 복구를 제대로 이해해야 합니다.



## 개요

다양한 작업에서 SQL Server를 기본으로 사용합니다. SharePoint, Dynamics 및 SAP과 같은 응용프로그램은 SQL Server를 사용하여 데이터 서비스를 구현합니다. SQL Server 가용성 그룹과 같은 SQL Server의 고가용성 및 재해 복구 기능은 SQL Server 데이터베이스를 보호 및 복구합니다.

사이트 복구는 Hyper-V 가상 컴퓨터, VMware 가상 컴퓨터 또는 물리적 서버로 실행하여 SQL Server를 보호할 수 있습니다.

 |**온-프레미스 간** | **온-프레미스와 Azure 간** 
---|---|---
**Hyper-V** | 예 | 예
**VMware** | 예 | 예 
**물리적 서버** | 예 | 예


## 지원 및 통합

사이트 복구는 재해 복구 솔루션을 제공하기 위해 표에 요약된 네이티브 SQL Server BCDR 기술과 함께 통합할 수 있습니다.

**기능** |**세부 정보** | **SQL Server 버전** 
---|---|---
**AlwaysOn 가용성 그룹** | <p>SQL Server의 여러 독립 실행형 인스턴스는 여러 노드가 있는 장애 조치(failover) 클러스터에서 실행됩니다.</p> <p>SQL Server 인스턴스에서 복사(미러링)할 수 있는 장애 조치(failover) 그룹으로 데이터베이스를 그룹화하여 공유 저장소가 필요하지 않습니다.</p> <p>주 사이트 및 하나 이상의 보조 사이트 간에 재해 복구를 제공합니다. 동기 복제 및 자동 장애 조치(failover)를 사용하여 가용성 그룹에서 구성된 SQL Server 데이터베이스로 공유되지 않은 클러스터에서 두 노드를 설정할 수 있습니다.</p> | SQL Server 2014/2012 Enterprise 버전
**장애 조치 클러스터링(AlwaysOn FCI)** | <p>SQL Server는 온-프레미스 SQL Server 작업의 고가용성을 위해 Windows 장애 조치(failover) 클러스터링을 활용합니다.</p><p>공유 디스크를 사용하는 SQL Server의 인스턴스를 실행하는 노드는 장애 조치(failover) 클러스터에서 구성됩니다. 인스턴스가 다운되는 경우 클러스터는 다른 것을 장애 조치(failover)합니다.</p> <p>클러스터는 공유 저장소의 오류 또는 중단을 보호하지 않습니다. 공유 디스크는 iSCSI, 파이버 채널 또는 VHDX와 함께 구현할 수 있습니다.</p> | SQL Server Enterprise 버전</p><p>SQL Server Standard 버전(노드가 두 개로 제한됨)
**데이터베이스 미러링(높은 보안 모드)** | 하나의 보조 복사본으로 단일 데이터베이스를 보호합니다. 높은 보안(동기) 및 고성능(비동기) 복제 모드에서 모두 사용할 수 있습니다. 장애 조치 클러스터가 필요하지 않습니다. | <p>SQL Server 2008 R2</p><p>SQL Server Enterprise 모든 버전</p>
**독립 실행형 SQL Server** | SQL Server 및 데이터베이스는 단일 서버(실제 또는 가상)에서 호스팅됩니다. 호스트 클러스터링은 가상 서버인 경우 고가용성을 위해 사용됩니다. 게스트 수준의 고가용성은 없습니다. | Enterprise 또는 Standard 에디션





다음 표에서는 사이트 복구 배포에 SQL Server BCDR 기술을 통합하기 위한 권장 사항을 요약합니다.

**버전** |**에디션** | **배포**웹사이트를 | **온-프레미스에서 온-프레미스로** | **온-프레미스에서 Azure로** 
---|---|---|---|---
SQL Server 2014 또는 2012 | Enterprise | 장애 조치 클러스터 인스턴스 | AlwaysOn 가용성 그룹 | AlwaysOn 가용성 그룹
 | Enterprise | 고가용성을 위한 AlwaysOn 가용성 그룹 | AlwaysOn 가용성 그룹 | AlwaysOn 가용성 그룹
 | 표준 | 장애 조치 클러스터 인스턴스 | 로컬 미러를 사용하는 사이트 복구 복제 | 로컬 미러를 사용하는 사이트 복구 복제
 | Enterprise 또는 Standard | 독립 실행형 | 로컬 미러를 사용하는 사이트 복구 복제 | 로컬 미러를 사용하는 사이트 복구 복제
SQL Server 2008 R2 | Enterprise 또는 Standard | 독립 실행형 | 로컬 미러를 사용하는 사이트 복구 복제 | 로컬 미러를 사용하는 사이트 복구 복제


## 배포 필수 조건

시작하기 전에 필요한 사항:


- 지원되는 SQL Server 버전을 실행하는 온-프레미스 SQL Server 배포입니다. 일반적으로 SQL server에 대한 Active Directory도 필요합니다.
- 배포하려는 시나리오를 위한 필수 조건입니다. 각 배포 문서에서 필수 조건을 찾을 수 있습니다. [사이트 복구 개요](site-recovery-overview.md)에 나열 및 연결되어 있습니다.
- Azure에서 복구를 설정하려는 경우,SQL Server 가상 컴퓨터의 [Azure 가상 컴퓨터 준비 평가](http://www.microsoft.com/download/details.aspx?id=40898)를 실행하여 Azure 및 사이트 복구와 호환되도록 해야 합니다.

## 보호 설정

다음과 같은 몇 가지 단계를 수행해야 합니다.

- Active Directory 설정
- SQL Server 클러스터 또는 독립 실행형 서버에 대한 보호 설정

### Active Directory 설정

제대로 실행 하려면 SQL Server에 대해 보조 복구 사이트에서 Active Directory가 필요합니다. 두 가지 선택 사항이 있습니다.

- **소형 엔터프라이즈**—온-프레미스 사이트에 대한 소량의 응용프로그램 및 단일 도메인 컨트롤러가 있고 전체 사이트를 장애 조치하려는 경우, 사이트 복구 복제를 사용하여 도메인 컨트롤러를 보조 데이터센터 또는 Azure로 복제하는 것이 좋습니다.

- **중형 및 대형 엔터프라이즈**—다량의 응용프로그램이 있고 Active Directory 포리스트를 실행하며 응용프로그램 또는 워크로드에 대한 장애 조치를 하려는 경우, 보조 데이터센터 또는 Azure에서 추가 도메인 컨트롤러를 설정하는 것이 좋습니다. AlwaysOn 가용성 그룹을 사용하여 원격 사이트로 복구하는 경우, 복구된 SQL Server 인스턴스에 대해 사용하기 위해 보조 사이트 또는 Azure에서 다른 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

이 문서의 지침에서는 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정합니다.

### 독립 실행형 SQL Server에 대한 보호 설정


이 구성에서 SQL Server 컴퓨터를 보호하기 위해 사이트 복구 복제를 사용하는 것이 좋습니다. 정확한 단계는 SQL Server가 가상 컴퓨터 또는 물리적 서버로 설정되었는지에 따라, Azure 또는 보조 온-프레미스 사이트로 복제하려는지에 따라 달라집니다. [사이트 복구 개요](site-recovery-overview.md)에서 모든 배포 시나리오에 대한 지침을 참조하세요.


### SQL Server 클러스터(2012 또는 2014 Enterprise)에 대한 보호 설정

SQL Server가 고가용성을 위해 고가용성 그룹 또는 장애 조치 클러스터 인스턴스를 사용하는 경우, 복구 사이트에서도 고가용성 그룹을 사용하는 것이 좋습니다. 이 지침은 분산된 트랜잭션을 사용하지 않는 응용프로그램에 대한 것입니다.

##### 온-프레미스 간

1. 가용성 그룹으로 [데이터베이스를 구성](https://msdn.microsoft.com/library/hh213078.aspx)합니다.
2. 보조 사이트에서 새 가상 네트워크를 만듭니다.
3. 새 가상 네트워크 및 기본 사이트 사이에서 사이트 간 VPN을 설정합니다.
4. 복구 사이트에서 가상 컴퓨터를 만들고 SQL Server를 설치합니다.
5. 기존의 AlwaysOn 가용성 그룹을 새 SQL Server 가상 컴퓨터로 확장합니다. 이 SQL Server 인스턴스를 비동기 복제 복사본으로 구성합니다.
6. 가용성 그룹 수신기를 만들거나 비동기 복제 가상 컴퓨터를 포함하도록 기존 수신기를 업데이트합니다.
7. 수신기를 사용하여 응용프로그램 팜이 설정되었는지 확인합니다. 데이터베이스 서버 이름을 사용하여 설정하는 경우, 수신기를 사용하도록 업데이트하여 장애 조치 후에 이를 다시 인식할 필요가 없도록 합니다.

#### 온-프레미스와 Azure 간

Azure으로 복제할 때 여러 가용성 그룹 구성은 각 가용성 그룹에는 전용 수신기가 필요하기 때문에 어렵습니다. 또한 각 수신기 구성에는 개별 클라우드 서비스가 필요합니다. 포함되는 모든 데이터베이스와 함께 하나의 가용성 그룹을 구성하는 것이 좋습니다.

1. Azure 가상 네트워크를 만듭니다.
2. 온-프레미스 사이트와 이 네트워크 사이에서 사이트 간 VPN을 설정합니다.
3. 네트워크에 새 SQL Server Azure 가상 컴퓨터를 만들고 비동기 가용성 그룹 복제본으로 이를 구성합니다. Azure에 장애 조치 후 SQL Server 계층에 대해 고가용성이 필요한 경우 Azure에 두 개의 비동기 복제 복사본을 구성합니다.
4. 가상 네트워크에 도메인 컨트롤러의 복제본을 설정합니다.
5. 가상 컴퓨터에서 가상 컴퓨터 확장을 사용하고 있는지 확인합니다. 복구 계획에서 SQL Server 특정 스크립트를 넣어야 합니다.
6. Azure의 내부 부하 분산 장치를 사용하여 가용성 그룹에 대한 SQL Server 수신기를 구성합니다.
7. 데이터베이스 계층에 액세스하는 데 수신기를 사용하도록 응용프로그램 계층을 구성합니다. 분산된 트랜잭션을 사용하는 응용프로그램의 경우, SAN 복제 또는 VMWare 사이트간 복제로 사이트 복구를 사용하는 것이 좋습니다.

### SQL Server 클러스터(Standard 또는 2008 R2)에 대한 보호 설정

SQL Server Standard 에디션 또는 SQL Server 2008 R2를 실행하는 클러스터의 경우 SQL Server를 보호하기 위해 사이트 복구 복제를 사용하는 것이 좋습니다.

#### 온-프레미스 간

- Hyper-V 환경의 경우, 응용프로그램이 분산된 트랜잭션을 사용하는 경우 [SAN 복제로 사이트 복구](site-recovery-vmm-san.md)를 배포하는 것이 좋습니다.

- VMware 환경의 경우, [VMware간](site-recovery-vmware-to-vmware.md) 보호를 배포하는 것이 좋습니다.

#### 온-프레미스와 Azure 간

Azure로 복제할 때 사이트 복구는 게스트 클러스터 지원을 지원하지 않습니다. 또한 SQL Server는 Standard 에디션에 대해 저비용 재해 복구 솔루션을 제공하지 않습니다. 독립 실행형 SQL Server에 온-프레미스 SQL Server 클러스터를 보호하고 이를 Azure에서 복구하는 것이 좋습니다.


1. 온-프레미스 사이트에 추가 독립 실행형 SQL Server 인스턴스를 구성합니다.
2. 보호가 필요한 데이터베이스에 대한 미러로 제공하기 위해 이 인스턴스를 구성합니다. 높은 보안 모드에서 미러링을 구성합니다.
3.	환경([Hyper-V](site-recovery-hyper-v-site-to-azure.md) 또는 [VMware](site-recovery-vmware-to-azure.md))에 따라 온-프레미스 사이트에서 사이트 복구를 구성합니다.
4.	Azure에 새 SQL Server 인스턴스를 복제하도록 사이트 복구 복제를 사용합니다. 높은 보안으로 미러를 복사하여 기본 클러스터와 동기화할 수 있지만 사이트 복구 복제를 사용하여 Azure에 복제됩니다.

다음 그래픽에서는 이러한 설정을 보여줍니다.

![표준 클러스터](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##SQL AlwaysOn을 Azure에 통합

ASR()은 기본적으로 SQL AlwaysOn을 지원합니다. Azure 가상 컴퓨터를 '보조'로 설정한 상태에서 SQL 가용성 그룹을 만든 경우 ASR을 사용하여 가용성 그룹의 장애 조치(failover)를 관리할 수 있습니다.

이 기능은 현재 미리 보기이며 기본 데이터 센터를 System Center VMM(Virtual Machine Manager)으로 관리할 경우 사용할 수 있습니다.

### VMM Server에서 관리하는 환경
ASR 자격 증명 모음을 표시하면 '보호된 항목' 탭에 'SQL Servers' 탭이 있습니다.

![보호된 항목](./media/site-recovery-sql/protected-items.png)

다음은 ASR에 SQL AlwaysOn을 통합하는 단계입니다.

#### 필수 조건
- 독립 실행형 서버 또는 장애 조치(failover) 클러스터의 온-프레미스 SQL Server 
- SQL Server가 설치된 한 대 이상의 Azure 가상 컴퓨터
- 온-프레미스 SQL Server와 Azure에서 실행되는 SQL server 간 SQL 가용성 그룹 설정
- 온-프레미스 SQL Server에 PowerShell 원격을 사용하도록 설정해야 합니다. VMM Server는 SQL Server에 원격 PowerShell 호출을 수행할 수 있어야 함
- 온-프레미스 SQL Server에서 최소한 다음과 같은 사용 권한이 있는 SQL 사용자 그룹에 사용자 계정을 추가해야 합니다.
	- 가용성 그룹 수정 - [참조 1](https://msdn.microsoft.com/ko-KR/library/hh231018.aspx), [참조 2](https://msdn.microsoft.com/ko-KR/library/ff878601.aspx#Anchor_3)
	- 데이터베이스 수정 - [참조 1](https://msdn.microsoft.com/ko-KR/library/ff877956.aspx#Security)
- 이전 단계의 계정에 대해 VMM Server에 실행 계정을 만들어야 함
- 온-프레미스 및 Azure 가상 컴퓨터에서 실행되는 SQL Server에 SQL PS 모듈을 설치해야 함
- Azure에서 실행되는 가상 컴퓨터에 VM 에이전트를 설치해야 함
- NTAUTHORITY\\시스템은 Azure의 가상 컴퓨터에서 실행되는 SQL Server에 다음과 같은 권한이 있어야 함
	- 가용성 그룹 수정 - [참조 1](https://msdn.microsoft.com/ko-KR/library/hh231018.aspx), [참조 2](https://msdn.microsoft.com/ko-KR/library/ff878601.aspx#Anchor_3)
	- 데이터베이스 수정 - [참조 1](https://msdn.microsoft.com/ko-KR/library/ff877956.aspx#Security)

#### SQL Server 추가

새 SQL Server를 추가하려면 SQL 추가를 클릭합니다.

![SQL 추가](./media/site-recovery-sql/add-sql.png)

SQL Server 및 VMM 정보와 SQL Server를 관리하는 사용할 자격 증명을 입력합니다.

![SQL 대화 상자 추가](./media/site-recovery-sql/add-sql-dialog.png)

##### 매개 변수
1. 이름: 이 SQL Server를 가리키는 데 사용할 이름
2. SQL Server(FQDN): 추가하려는 원본 SQL Server의 FQDN(정규화된 도메인 이름) SQL Server를 장애 조치(failover) 클러스터에 설치한 경우 클러스터의 FQDN을 입력합니다. 클러스터 노드의 FQDN은 입력하지 않습니다. 
3. SQL Server 인스턴스: 기본 SQL 인스턴스를 선택하거나 사용자 지정 SQL 인스턴스의 이름을 입력합니다.
4. VMM 서버: 이미 ASR(Azure Site Recovery)에 등록된 VMM Server 중 하나를 선택합니다. ASR는 이 VMM 서버를 사용하여 SQL Server와 통신합니다.
5. 실행 계정: 위에서 선택한 VMM 서버에서 만든 실행 계정 중 하나의 이름을 입력합니다. 이 실행 계정은 SQL Server에 액세스하는 데 사용하며 SQL Server의 가용성 그룹에서 읽기 및 장애 조치(failover) 권한이 있어야 합니다. 

SQL Server를 추가하면 'SQL Servers' 탭에 표시됩니다.

![SQL Server 목록](./media/site-recovery-sql/sql-server-list.png)

#### SQL 가용성 그룹 추가

SQL Server를 추가한 다음에는 ASR에 가용성 그룹을 추가해야 합니다. 그러려면 이전 단계에서 추가한 SQL Server 안을 드릴다운하고 'SQL 가용성 그룹 추가'를 클릭합니다.

![SQL AG 추가](./media/site-recovery-sql/add-sqlag.png)

SQL 가용성 그룹은 Azure에서 하나 이상의 가상 컴퓨터로 복제할 수 있습니다. SQL 가용성 그룹을 추가할 경우 ASR에서 가용성 그룹을 장애 조치(failover)할 Azure 가상 컴퓨터의 이름과 구독을 입력해야 합니다.

![SQL AG 대화 상자 추가](./media/site-recovery-sql/add-sqlag-dialog.png)

위 예제에서 가용성 그룹 DB1-AG는 장애 조치(failover) 시 DevTesting2 구독 내에서 실행되는 가상 컴퓨터 SQLAGVM2의 기본이 됩니다.

>[AZURE.NOTE]위 단계에서 추가한 SQL Server의 기본 가용성 그룹만 ASR에 추가할 수 있습니다. 가용성 그룹을 SQL Server의 기본으로 지정하거나 추가한 다음 SQL Server에 가용성 그룹을 추가한 경우 SQL Server에서 사용 가능한 새로 고침 옵션을 사용하여 새로 고칩니다.

#### 복구 계획 만들기

다음으로 가상 컴퓨터와 가용성 그룹을 사용하여 복구 계획을 만듭니다. 1단계에서 사용한 것과 동일한 VMM 서버를 원본으로 선택하고 Microsoft Azure를 대상으로 선택합니다.

![복구 계획 만들기](./media/site-recovery-sql/create-rp1.png)

![복구 계획 만들기](./media/site-recovery-sql/create-rp2.png)

이 예제에서 SharePoint 응용 프로그램은 SQL 가용성 그룹을 백 엔드로 사용하는 3개의 가상 컴퓨터로 구성되어 있습니다. 이 복구 계획에서는 가용성 그룹과 응용 프로그램을 구성하는 가상 컴퓨터를 모두 선택할 수 있습니다.

가상 컴퓨터를 다른 장애 조치(failover) 그룹으로 이동하여 장애 조치(failover) 순서를 조정하면 복구 계획을 자세히 사용자 지정할 수 있습니다. 가용성 그룹은 모든 응용 프로그램의 백 엔드로 사용되므로 언제나 가장 먼저 장애 조치(failover)됩니다.

![복구 계획 사용자 지정](./media/site-recovery-sql/customize-rp.png)

#### 장애 조치(Failover)

가용성 그룹을 복구 계획에 추가하면 다른 장애 조치(failover) 옵션을 사용할 수 있습니다.

##### 계획된 장애 조치(failover)

계획된 장애 조치(failover)는 데이터 손실이 없는 장애 조치(failover)를 의미합니다. 이를 달성하기 위해 우선 SQL 가용성 그룹의 가용성 모드를 동기로 설정한 다음 장애 조치(failover)를 트리거하여 가용성 그룹을 제공된 가상 컴퓨터의 기본으로 지정하는 동시에 ASR에 추가합니다. 장애 조치(failover)가 완료되면 가용성 모드를 계획된 장애 조치(failover)가 트리거되기 전과 동일한 값으로 설정합니다.

##### 계획되지 않은 장애 조치

계획되지 않은 장애 조치(failover)는 데이터 손실이 발생할 수 있습니다. 계획되지 않은 장애 조치(failover)를 트리거하는 동안 가용성 그룹의 가용성 모드는 변경되지 않으며 가용성 그룹을 제공된 가상 컴퓨터에 기본으로 지정하는 동시에 ASR에 추가합니다. 계획되지 않은 장애 조치(failover)가 완료되고 SQL Server를 실행하는 온-프레미스 서버를 다시 사용할 수 있게 되면 가용성 그룹에서 역방향 복제를 트리거해야 합니다. 이 작업은 복구 계획에서 사용할 수 없으며 'SQL Servers' 탭의 SQL 가용성 그룹에서 수행할 수 있습니다.

##### 테스트 장애 조치(Failover)
SQL 가용성 그룹에 대한 테스트 장애 조치(failover)는 지원되지 않습니다. SQL 가용성 그룹이 포함된 역방향 계획의 테스트 장애 조치(failover)를 트리거할 경우 가용성 그룹에 대한 장애 조치(failover)를 건너뜁니다.

##### 장애 복구

온-프레미스 SQL Server에서 가용성 그룹을 다시 기본으로 지정하려면 복구 계획에서 계획된 장애 조치(failover)를 트리거하고 Microsoft Azure에서 온-프레미스 VMM 서버로 방향을 선택합니다.

##### 역방향 복제

계획되지 않은 장애 조치(failover) 이후에는 가용성 그룹에서 역방향 복제를 트리거하여 복제를 재개해야 합니다. 이 작업을 완료하기 전까지는 복제가 일시 중단됩니다.


### VMM에서 관리하지 않는 환경

VMM 서버에서 관리하지 않는 환경의 경우 Azure 자동화 Runbook을 사용하여 SQL 가용성 그룹의 스크립팅된 장애 조치(failover)를 구성할 수 있습니다. 다음은 구성 단계입니다.


1.	가용성 그룹을 장애 조치하기 위해 스크립트용 로컬 파일을 만듭니다. 이 샘플 스크립트는 Azure 복제본에서 가용성 그룹에 대한 경로를 지정하고 해당 복제본 인스턴스에 장애 조치를 합니다. 이 스크립트는 사용자 지정 스크립트 확장으로 전달을 통해 SQL Server 복제본 가상 컴퓨터에서 실행됩니다.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Azure 저장소 계정에서 Blob으로 스크립트를 업로드합니다. 이 예제를 사용하여:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Azure의 SQL Server 복제본 가상 컴퓨터에서 스크립트를 호출하는 Azure 자동화 runbook을 만듭니다. 이 예제 스크립트를 사용하여 이 작업을 수행합니다. 복구 계획에서 자동화 runbook 사용에 대해 [자세히 알아봅니다](site-recovery-runbook-automation.md).

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	응용프로그램에 대한 복구 계획을 만들 때 가용성 그룹을 장애 조치하기 위해 자동화 runbook을 호출하는 "pre-Group 1 boot" 스크립트 단계를 추가합니다.

#### 보조 사이트에 장애 조치를 위한 SQL Server 스크립트 구성

1. 이 샘플 스크립트를 기본 및 보조 사이트의 VMM 라이브러리에 추가합니다.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. 응용프로그램에 대한 복구 계획을 만들 때 가용성 그룹을 장애 조치하기 위해 스크립트를 호출하는 "pre-Group 1 boot" 스크립트 단계를 추가합니다.


## 테스트 장애 조치 시 고려 사항

AlwaysOn 가용성 그룹을 사용하는 경우 SQL Server 계층의 테스트 장애 조치를 수행할 수 없습니다. 대신 다음과 같은 옵션을 고려합니다.

###옵션 1



1. 응용프로그램 및 프런트엔드 계층의 테스트 장애 조치를 수행합니다.

2. 읽기 전용 모드에서 복제 복사본에 액세스하도록 응용프로그램 계층을 업데이트하고 응용프로그램의 읽기 전용 테스트를 수행합니다.

###옵션 2

1.	복제본 SQL Server 가상 컴퓨터 인스턴스(사이트간 또는 Azure 백업용 VMM 복제 사용)를 만들고 이를 테스트 네트워크로 가져옵니다.
2.	복구 계획을 사용하여 테스트 장애 조치를 수행합니다.







 

<!---HONumber=Oct15_HO3-->