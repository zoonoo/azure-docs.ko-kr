---
title: "SQL Server 및 Azure Site Recovery를 사용한 앱 복제 | Microsoft Docs"
description: "이 문서에서는 SQL Server 재해 기능의 Azure Site Recovery를 사용하여 SQL Server를 복제하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f822756c0ce45c98b95a0cec2efd1353aff71561
ms.openlocfilehash: f2d8a9221a989f9e79b6f4e17d9448303544ffd6
ms.lasthandoff: 02/22/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호

이 문서에서는 SQL Server BCDR(비즈니스 연속성 및 재해 복구) 기술 및 [Azure Site Recovery](site-recovery-overview.md)를 조합하여 응용 프로그램의 SQL Server 백 엔드를 보호하는 방법을 설명합니다.

시작하기 전에 장애 조치 클러스터링, AlwaysOn 가용성 그룹, 데이터베이스 미러링 및 로그 전달을 비롯한 SQL Server 재해 복구 기능을 알고 있어야 합니다.


## <a name="sql-server-deployments"></a>SQL Server 배포

다양한 작업은 SQL Server를 기반으로 사용하고 데이터 서비스를 구현하도록 SharePoint, Dynamics 및 SAP와 같은 앱으로 통합될 수 있습니다.  SQL Server는 다양한 방법으로 배포될 수 있습니다.

* **독립 실행형 SQL Server**: SQL Server 및 모든 데이터베이스는 단일 컴퓨터(물리적 또는 가상)에서 호스트됩니다. 가상화된 경우 호스트 클러스터링은 고가용성을 위해 사용됩니다. 게스트 수준의 고가용성은 구현되지 않습니다.
* **SQL Server 장애 조치(Failover) 클러스터링 인스턴스(Always On FCI)**: 공유된 디스크와 함께 인스턴스화된 SQL Server를 실행하는 노드가&2;개 이상 Windows 장애 조치 클러스터에 구성됩니다. 노드가 다운되는 경우 클러스터는 다른 인스턴스로 SQL Server를 장애 조치할 수 있습니다. 이 설치 프로그램은 기본 사이트에 고가용성을 구현하는 데 일반적으로 사용됩니다. 이 배포는 공유 저장소 계층에서 정전이나 오류에 대해서는 보호하지 않습니다. 공유 디스크는 iSCSI, 파이버 채널 또는 공유 vhdx를 사용하여 구현할 수 있습니다.
* **SQL Always ON 가용성 그룹**: 동기 복제 및 자동 장애 조치(failover)를 사용하여 가용성 그룹에서 구성된 SQL Server 데이터베이스로 공유되지 않은 클러스터에서&2;개 이상의 노드를 설정합니다.

 이 문서에서는 원격 사이트에 대한 데이터베이스 복구에 다음과 같은 네이티브 SQL 재해 복구 기술을 활용합니다.

* SQL Server 2012 또는 2014 Enterprise 버전용 재해 복구에 대해 제공하는 SQL Always On 가용성 그룹.
* SQL Server Standard 버전(모든 버전) 또는 SQL Server 2008 R2에 대한 높은 보안 모드에서 SQL 데이터베이스 미러링.

## <a name="site-recovery-support"></a>Site Recovery 지원

### <a name="supported-scenarios"></a>지원되는 시나리오
Site Recovery는 표에 요약된 대로 SQL Server를 보호할 수 있습니다.

**시나리오** | **보조 사이트로** | **Azure로**
--- | --- | ---
**Hyper-V** | 예 | 예
**VMware** | 예 | 예
**물리적 서버** | 예 | 예

### <a name="supported-sql-server-versions"></a>지원되는 SQL Server 버전
지원되는 시나리오에 대해 이러한 SQL Server 버전이 지원됩니다.

* SQL Server 2014 Enterprise 및 Standard
* SQL Server 2012 Enterprise 및 Standard
* SQL Server 2008 R2 Enterprise 및 Standard

### <a name="supported-sql-server-integration"></a>지원되는 SQL Server 통합

Site Recovery는 재해 복구 솔루션을 제공하기 위해 표에 요약된 네이티브 SQL Server BCDR 기술과 함께 통합할 수 있습니다.

**기능** | **세부 정보** | **SQL Server** |
--- | --- | ---
**Always On 가용성 그룹** | SQL Server의 여러 독립 실행형 인스턴스는 여러 노드가 있는 장애 조치 클러스터에서 실행됩니다.<br/><br/>SQL Server 인스턴스에서 복사(미러링)할 수 있는 장애 조치 그룹으로 데이터베이스를 그룹화하여 공유 저장소가 필요하지 않습니다.<br/><br/>기본 사이트 및 하나 이상의 보조 사이트 간에 재해 복구를 제공합니다. 동기 복제 및 자동 장애 조치를 사용하여 가용성 그룹에서 구성된 SQL Server 데이터베이스로 공유되지 않은 클러스터에서 두 노드를 설정할 수 있습니다. | SQL Server 2014 및 2012 Enterprise 버전
**장애 조치 클러스터링(AlwaysOn FCI)** | SQL Server는 온-프레미스 SQL Server 작업의 고가용성을 위해 Windows 장애 조치(failover) 클러스터링을 활용합니다.<br/><br/>공유 디스크를 사용하는 SQL Server 서버의 인스턴스를 실행하는 노드는 장애 조치 클러스터에서 구성됩니다. 인스턴스가 다운되는 경우 클러스터는 다른 것을 장애 조치합니다.<br/><br/>클러스터는 공유 저장소의 오류 또는 중단을 보호하지 않습니다. 공유 디스크는 iSCSI, 파이버 채널 또는 VHDX와 함께 구현할 수 있습니다. | SQL Server Enterprise 버전<br/><br/>SQL Server Standard 버전(노드가 두 개로 제한됨)
**데이터베이스 미러링(높은 보안 모드)** | 하나의 보조 복사본으로 단일 데이터베이스를 보호합니다. 높은 보안(동기) 및 고성능(비동기) 복제 모드에서 모두 사용할 수 있습니다. 장애 조치 클러스터가 필요하지 않습니다. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise 모든 버전
**독립 실행형 SQL Server** | SQL Server 및 데이터베이스는 단일 서버(실제 또는 가상)에서 호스팅됩니다. 호스트 클러스터링은 가상 서버인 경우 고가용성을 위해 사용됩니다. 게스트 수준의 고가용성은 없습니다. | Enterprise 또는 Standard 에디션

## <a name="deployment-recommendations"></a>배포 권장 사항

**버전** | **배포웹사이트를** | **온-프레미스와 보조 간** | **온-프레미스와 Azure 간** |
--- | --- | --- | --- | --- |
**SQL Server 2014/2012 Enterprise FCI** | 장애 조치 클러스터 | AlwaysOn 가용성 그룹 | AlwaysOn 가용성 그룹
**SQL Server 2014/2012 AlwaysOn** | AlwaysOn 가용성 그룹 | AlwaysOn | AlwaysOn
**SQL Server 2014/2012 Standard FCI** | 장애 조치 클러스터 | 로컬 미러를 사용하는 Site Recovery 복제 | 로컬 미러를 사용하는 Site Recovery 복제
**SQL Server 2014/2012 Enterprise/Standard** | 독립 실행형 | Site Recovery 복제 | Site Recovery 복제
**SQL Server 2008 R2 Enterprise/Standard** | FCI |로컬 미러를 사용하는 Site Recovery 복제 |로컬 미러를 사용하는 Site Recovery 복제 |
**SQL Server 2008 R2 Enterprise/Standard** | 독립 실행형 |Site Recovery 복제 | Site Recovery 복제
**SQL Server(모든 버전) Enterprise/Standard** |FCI - DTC 응용 프로그램 | Site Recovery 복제 |지원되지 않음

## <a name="deployment-prerequisites"></a>배포 필수 조건

* 지원되는 SQL Server 버전을 실행하는 온-프레미스 SQL Server 배포입니다. 일반적으로 SQL Server에 대한 Active Directory도 필요합니다.
* 배포하려는 시나리오를 위한 요구 사항입니다. [Azure로 복제](site-recovery-support-matrix-to-azure.md) 및 [온-프레미스](site-recovery-support-matrix.md) 및 [배포 필수 구성 요소](site-recovery-prereq.md)에 대한 지원 요구 사항에 대해 자세히 알아봅니다.
* Azure에서 복구를 설정하려면 SQL Server 가상 컴퓨터의 [Azure 가상 컴퓨터 준비 평가](http://www.microsoft.com/download/details.aspx?id=40898) 도구를 실행하여 Azure 및 Site Recovery와 호환되도록 합니다.

## <a name="set-up-active-directory"></a>Active Directory 설정

SQL Server를 제대로 실행하려면 보조 복구 사이트에서 Active Directory를 설정합니다.

* **소형 엔터프라이즈**—온-프레미스 사이트에 대한 소량의 응용 프로그램 및 단일 도메인 컨트롤러가 있고 전체 사이트를 장애 조치하려는 경우, Site Recovery 복제를 사용하여 도메인 컨트롤러를 보조 데이터센터 또는 Azure로 복제하는 것이 좋습니다.
* **중형 및 대형 엔터프라이즈**—다량의 응용 프로그램 및 Active Directory 포리스트가 있고 응용 프로그램 또는 워크로드에 대한 장애 조치를 하려는 경우, 보조 데이터센터 또는 Azure에서 추가 도메인 컨트롤러를 설정하는 것이 좋습니다. AlwaysOn 가용성 그룹을 사용하여 원격 사이트로 복구하는 경우, 복구된 SQL Server 인스턴스에 대해 사용하기 위해 보조 사이트 또는 Azure에서 다른 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

이 문서의 지침에서는 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정합니다. [자세히 알아보세요](site-recovery-active-directory.md) .

## <a name="integrate-with-sql-server-always-on-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>Azure로 복제를 위해 SQL Server Always On과 통합(VMM/구성 서버와 클래식 포털)


Site Recovery는 기본적으로 SQL AlwaysOn을 지원합니다. Azure 가상 컴퓨터를 보조 위치로 설정한 상태에서 SQL 가용성 그룹을 만든 경우 Site Recovery를 사용하여 가용성 그룹의 장애 조치(failover)를 관리할 수 있습니다.

> [!NOTE]
> 이 기능은 현재 미리 보기로 제공되고 있습니다. 주 사이트에 System Center VMM 클라우드에서 관리되는 Hyper-V 호스트 서버가 있거나 [VMware 복제](site-recovery-vmware-to-azure.md)를 설정한 경우 사용 가능합니다. 현재 새 Azure Portal에서는 기능을 사용할 수 없습니다. 지금은 새 Azure 포털에서 이 기능을 사용할 수 없습니다.
>
>

#### <a name="before-you-start"></a>시작하기 전에

Site Recovery와 SQL AlwaysOn을 통합하려면 다음이 필요합니다.

* 온-프레미스 SQL Server(독립 실행형 서버 또는 장애 조치(failover) 클러스터).
* SQL Server가 설치된 한 대 이상의 Azure 가상 컴퓨터.
* 온-프레미스 SQL Server와 Azure에서 실행되는 SQL Server 간 SQL Server 가용성 그룹 설정.
* 온-프레미스 SQL Server에 활성화된 PowerShell 원격. VMM Server 또는 구성 서버는 SQL Server 컴퓨터에 원격 PowerShell 호출을 수행할 수 있어야 합니다.
* 사용자 계정은 온-프레미스 SQL Server 컴퓨터에 추가되어야 합니다. 최소한 다음과 같은 사용 권한으로 SQL Server 그룹에 추가합니다.
  * ALTER AVAILABILITY GROUP - 권한 [여기](https://msdn.microsoft.com/library/hh231018.aspx) 및 [여기](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE - 권한 [여기](https://msdn.microsoft.com/library/ff877956.aspx#Security)
* VMM을 실행하는 경우 VMM 서버에 실행 계정을 만들어야 합니다.
- VMware를 실행하는 경우 CSPSConfigtool.exe를 사용하여 구성 서버에 계정을 만들어야 합니다.
* 온-프레미스 및 Azure VM에서 실행되는 SQL Server에 SQL PS 모듈을 설치해야 합니다.
* Azure VM에 VM 에이전트를 설치해야 합니다.
* NTAUTHORITY\시스템은 Azure VM에서 실행되는 SQL Server에 다음과 같은 권한이 있어야 합니다.
  * ALTER AVAILABILITY GROUP - 권한 [여기](https://msdn.microsoft.com/library/hh231018.aspx) 및 [여기](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
  * ALTER DATABASE - 권한 [여기](https://msdn.microsoft.com/library/ff877956.aspx#Security)

### <a name="add-a-sql-server"></a>SQL Server 추가
1. 새 SQL Server를 추가하려면 **SQL 추가** 를 클릭합니다.

    ![SQL 추가](./media/site-recovery-sql/add-sql.png)
2. **SQL 설정 구성** > **이름**에서 SQL Server에 대한 친숙한 이름을 제공합니다.
3. **SQL Server(FQDN)**에서 추가하려는 원본 SQL Server의 FQDN을 지정합니다. SQL Server를 장애 조치(failover) 클러스터에 설치한 경우 클러스터 노드의 FQDN이 아닌 클러스터의 FQDN을 제공합니다.  
4. **SQL Server 인스턴스**에서 기본 인스턴스를 선택하거나 사용자 지정 이름을 제공합니다.
5. **관리 서버**에서 자격 증명 모음에 등록된 VMM 서버 또는 구성 서버를 선택합니다. Site Recovery는 이 서버를 사용하여 SQL Server와 통신합니다.
6. **실행 계정**에서 VMM 실행 계정 또는 구성 서버 계정 이름을 제공합니다. 이 계정은 SQL Server에 액세스하는 데 사용되며 SQL Server 컴퓨터의 가용성 그룹에 대한 읽기 및 장애 조치(Failover) 권한이 있어야 합니다.

    ![SQL 대화 상자 추가](./media/site-recovery-sql/add-sql-dialog.png)

SQL Server를 추가한 후 **SQL Server** 탭에 나타납니다.

![SQL Server 목록](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>SQL 가용성 그룹 추가

1. 추가한 SQL Server에서 **SQL 가용성 그룹 추가**를 클릭합니다.

    ![SQL AG 추가](./media/site-recovery-sql/add-sqlag.png)
2. 가용성 그룹은 하나 이상의 Azure VM으로 복제할 수 있습니다. 그룹을 추가하는 경우 Site Recovery를 통해 장애 조치하려는 그룹에 Azure VM의 이름 및 구독을 제공해야 합니다.

    ![SQL AG 대화 상자 추가](./media/site-recovery-sql/add-sqlag-dialog.png)
3. 이 예제에서 가용성 그룹 DB1-AG는 장애 조치(failover) 시 DevTesting2 구독 내에서 실행되는 가상 컴퓨터 SQLAGVM2의 기본이 됩니다.

> [!NOTE]
> 추가된 SQL Server의 기본 가용성 그룹만 Site Recovery에 추가할 수 있습니다. 가용성 그룹을 SQL Server의 기본으로 지정하거나 추가한 다음 SQL Server에 가용성 그룹을 추가한 경우 SQL Server에서 그룹을 새로 고칩니다.
>
>

### <a name="create-a-recovery-plan"></a>복구 계획 만들기

가상 컴퓨터와 가용성 그룹을 사용하여 복구 계획을 만듭니다. 원본으로 VMM 서버 또는 구성 서버를, 대상으로 Azure를 선택합니다.

![복구 계획 만들기](./media/site-recovery-sql/create-rp1.png)

![복구 계획 만들기](./media/site-recovery-sql/create-rp2.png)

이 예제에서 SharePoint 앱은 SQL 가용성 그룹을 백 엔드로 사용하는&3;개의 가상 컴퓨터로 구성되어 있습니다. 이 복구 계획에서 가용성 그룹 및 앱 VM 모두를 선택할 수 있습니다. VM을 다른 장애 조치(failover) 그룹으로 이동하여 장애 조치(failover) 순서를 조정하면 복구 계획을 자세히 사용자 지정할 수 있습니다. 가용성 그룹은 앱 백 엔드로 사용되기 때문에 항상 먼저 장애 조치됩니다.

![복구 계획 사용자 지정](./media/site-recovery-sql/customize-rp.png)

### <a name="fail-over"></a>장애 조치

가용성 그룹을 복구 계획에 추가하면 다른 장애 조치(failover) 옵션을 사용할 수 있습니다.

**장애 조치(Failover)** | **세부 정보**
--- | ---
**계획된 장애 조치** | 계획된 장애 조치(failover)는 데이터 손실이 없는 장애 조치(failover)를 의미합니다. 이를 달성하기 위해 SQL 가용성 그룹 모드를 동기로 설정한 다음 장애 조치(failover)를 트리거하여 그룹을 제공된 가상 컴퓨터의 기본으로 지정하는 동시에 Site Recovery에 추가합니다. 장애 조치(failover)가 완료되면 가용성 모드를 계획된 장애 조치(failover)가 트리거되기 전과 동일한 값으로 설정합니다.
**계획되지 않은 장애 조치** | 계획되지 않은 장애 조치(failover)는 데이터 손실이 발생할 수 있습니다. 계획되지 않은 장애 조치를 트리거하는 동안 그룹의 가용성 모드는 변경되지 않습니다. Site Recovery에 가용성 그룹을 추가하는 동안 제공된 가상 컴퓨터에 기본으로 구성됩니다. 계획되지 않은 장애 조치(failover)가 완료된 후 SQL Server를 실행하는 온-프레미스 서버를 다시 사용할 수 있게 되면 가용성 그룹에서 역방향 복제를 트리거해야 합니다. 이 작업은 **SQL Server** > **SQL 가용성 그룹**에서 사용할 수 있으며 복구 계획에서 사용할 수 없습니다.
**테스트 장애 조치** |SQL 가용성 그룹에 대한 테스트 장애 조치(failover)는 지원되지 않습니다. 테스트 장애 조치를 트리거하는 경우 가용성 그룹에 대한 장애 조치는 건너뜁니다.

이러한 장애 조치 옵션을 사용해 보세요.

**옵션** | **세부 정보**
--- | ---
**옵션 1** | 1. 응용프로그램 및 프런트 엔드 계층의 테스트 장애 조치를 수행합니다.<br/><br/>2. 읽기 전용 모드에서 복제 복사본에 액세스하도록 응용 프로그램 계층을 업데이트하고 응용 프로그램의 읽기 전용 테스트를 수행합니다.
**옵션 2** | 1. 복제본 SQL Server 가상 컴퓨터 인스턴스(사이트간 또는 Azure 백업용 VMM 복제 사용)를 만들고 이를 테스트 네트워크로 가져옵니다.<br/><br/> 2. 복구 계획을 사용하여 테스트 장애 조치를 수행합니다.

### <a name="fail-back"></a>장애 복구

온-프레미스 SQL Server에서 가용성 그룹을 다시 기본으로 지정하려면 복구 계획에서 계획된 장애 조치(failover)를 트리거하고 Microsoft Azure에서 온-프레미스 서버로 방향을 선택할 수 있습니다.

> [!NOTE]
> 계획되지 않은 장애 조치(failover) 이후에는 가용성 그룹에서 역방향 복제를 트리거하여 복제를 재개해야 합니다.  이 작업을 완료하기 전까지는 복제가 일시 중단됩니다.
>
>

## <a name="integrate-with-sql-server-always-on-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>Azure로 복제를 위해 SQL Server Always On과 통합(Azure Portal/VMM/구성 서버 없는 클래식 포털)

이러한 지침은 새 Azure Portal 또는 VMM 서버 또는 구성 서버를 사용하지 않는 경우 클래식 포털에서 SQL Server 가용성 그룹을 통합하는 경우에 관련됩니다. 이 시나리오에서 Azure Automation Runbook을 사용하여 SQL 가용성 그룹의 스크립팅된 장애 조치(failover)를 구성할 수 있습니다.

수행해야 할 사항:

1. 가용성 그룹을 장애 조치하는 스크립트용 로컬 파일을 만듭니다. 이 샘플 스크립트는 Azure 복제본에서 가용성 그룹에 대한 경로를 지정하고 해당 복제본 인스턴스에 장애 조치를 합니다. 이 스크립트는 사용자 지정 스크립트 확장으로 전달을 통해 SQL Server 복제본 가상 컴퓨터에서 실행됩니다.

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. Azure 저장소 계정에서 Blob으로 스크립트를 업로드합니다. 이 예제를 사용하여:

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. Azure의 SQL Server 복제본 가상 컴퓨터에서 스크립트를 호출하는 Azure 자동화 runbook을 만듭니다. 이 예제 스크립트를 사용하여 이 작업을 수행합니다. [자세히 알아봅니다](site-recovery-runbook-automation.md) .

4. 응용 프로그램에 대한 복구 계획을 만들 때 가용성 그룹을 장애 조치하기 위해 Automation runbook을 호출하는 "pre-Group 1 boot" 스크립트 단계를 추가합니다.


5. SQL AlwaysOn는 테스트 장애 조치를 고유하게 지원하지 않습니다. 따라서 다음 작업을 수행하는 것이 좋습니다.
    1. Azure에서 가용성 그룹 복제본을 호스팅하는 가상 컴퓨터에서 [Azure Backup](../backup/backup-azure-vms.md)을 설정합니다.
    1. 복구 계획의 테스트 장애 조치를 트리거하기 전에 이전 단계에서 수행된 백업에서 가상 머신을 복구합니다.
    1. 복구 계획에 대해 테스트 장애 조치(failover)를 수행합니다.


> [!NOTE]
> 아래의 스크립트는 SQL 가용성 그룹이 클래식 Azure VM에서 호스팅되고&2;단계에서 복구된 가상 컴퓨터의 이름은 SQLAzureVM-Test라고 가정합니다. 복구된 가상 컴퓨터에 사용할 이름을 기반으로 하는 스크립트를 수정합니다.
>
>


     ``workflow SQLAvailabilityGroupFailover
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
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>복제를 위해 SQL Server Always On을 보조 온-프레미스 사이트로 통합

SQL Server가 고가용성을 위해 고가용성 그룹(또는 FCI)을 사용하는 경우, 복구 사이트에서도 고가용성 그룹을 사용하는 것이 좋습니다. 이는 분산된 트랜잭션을 사용하지 않는 앱에 적용됩니다.

1. [데이터베이스를 구성](https://msdn.microsoft.com/library/hh213078.aspx) 합니다.
2. 보조 사이트에서 가상 네트워크를 만듭니다.
3. 가상 네트워크 및 기본 사이트 사이에서 사이트 간 VPN 연결을 설정합니다.
4. 복구 사이트에서 가상 컴퓨터를 만들고 SQL Server를 설치합니다.
5. 기존의 AlwaysOn 가용성 그룹을 새 SQL Server VM으로 확장합니다. 이 SQL Server 인스턴스를 비동기 복제 복사본으로 구성합니다.
6. 가용성 그룹 수신기를 만들거나 비동기 복제 가상 컴퓨터를 포함하도록 기존 수신기를 업데이트합니다.
7. 수신기를 사용하여 응용 프로그램 팜이 설정되었는지 확인합니다. 데이터베이스 서버 이름을 사용하여 설정하는 경우, 수신기를 사용하도록 업데이트하여 장애 조치 후에 이를 다시 인식할 필요가 없도록 합니다.

분산된 트랜잭션을 사용하는 응용 프로그램의 경우 [SAN 복제](site-recovery-vmm-san.md)로 Site Recovery 또는 [VMware/물리적 서버 사이트 간 복제](site-recovery-vmware-to-vmware.md)를 배포하는 것이 좋습니다.

### <a name="recovery-plan-considerations"></a>복구 계획 고려 사항
1. 이 샘플 스크립트를 기본 및 보조 사이트의 VMM 라이브러리에 추가합니다.

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. 응용 프로그램에 대한 복구 계획을 만들 때 가용성 그룹을 장애 조치하기 위해 스크립트를 호출하는 "pre-Group 1 boot" 스크립트 단계를 추가합니다.

## <a name="protect-a-standalone-sql-server"></a>독립 실행형 SQL Server 보호

이 시나리오에서 SQL Server 컴퓨터를 보호하기 위해 Site Recovery 복제를 사용하는 것이 좋습니다. 정확한 단계는 SQL Server가 VM 또는 물리적 서버인지에 따라, Azure 또는 보조 온-프레미스 사이트로 복제하려는지에 따라 달라집니다. [Site Recovery 시나리오](site-recovery-overview.md)에 대해 자세히 알아봅니다.

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>SQL Server 클러스터 보호(표준 에디션/Windows Server 2008 R2)

SQL Server Standard 에디션 또는 SQL Server 2008 R2를 실행하는 클러스터의 경우 SQL Server를 보호하기 위해 Site Recovery 복제를 사용하는 것이 좋습니다.

### <a name="on-premises-to-on-premises"></a>온-프레미스 간

* 앱이 분산된 트랜잭션을 사용하는 경우 Hyper-V 환경에서는 [SAN 복제로 Site Recovery](site-recovery-vmm-san.md)를 배포하거나 VMware 환경에서는 [VMware/물리적 서버에서 VMware로](site-recovery-vmware-to-vmware.md) 배포하는 것이 좋습니다.
* DTC가 아닌 응용 프로그램인 경우 로컬의 높은 보안 DB 미러를 활용하여 클러스터를 독립 실행형 서버로 복구하려면 위의 접근 방식을 사용합니다.

### <a name="on-premises-to-azure"></a>온-프레미스와 Azure 간

Azure로 복제할 때 Site Recovery는 게스트 클러스터 지원을 제공하지 않습니다. 또한 SQL Server는 Standard 에디션에 대해 저비용 재해 복구 솔루션을 제공하지 않습니다. 이 시나리오에서 독립 실행형 SQL Server에 온-프레미스 SQL Server 클러스터를 보호하고 이를 Azure에서 복구하는 것이 좋습니다.

1. 온-프레미스 사이트에 추가 독립 실행형 SQL Server 인스턴스를 구성합니다.
2. 보호하려는 데이터베이스에 대한 미러로 제공하기 위해 인스턴스를 구성합니다. 높은 보안 모드에서 미러링을 구성합니다.
3. [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 또는 [VMware VM/물리적 서버](site-recovery-vmware-to-azure-classic.md)에 대해 온-프레미스 사이트에서 Site Recovery를 구성합니다.
4. Azure에 새 SQL Server 인스턴스를 복제하도록 Site Recovery 복제를 사용합니다. 높은 보안 미러 복사이므로 기본 클러스터와 동기화되지만 Site Recovery 복제를 사용하여 Azure에 복제됩니다.


![표준 클러스터](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>장애 복구 고려 사항

SQL Server Standard 클러스터의 경우, 계획되지 않은 장애 조치 후의 장애 복구는 SQL Server 백업이 필요하며 미러의 재설정으로 미러 인스턴스에서 원래 클러스터로 복구해야 합니다.

## <a name="next-steps"></a>다음 단계
Site Recovery 아키텍처에 대해 [자세히 알아봅니다](site-recovery-components.md).

