---
title: SQL Server 및 Azure Site Recovery를 사용하여 SQL Server에 대한 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 SQL Server 및 Azure Site Recovery를 사용하여 SQL Server에 대한 재해 복구를 설정하는 방법을 설명합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491787"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server에 대한 재해 복구 설정

이 문서에서는 SQL Server BCDR(비즈니스 연속성 및 재해 복구) 기술 및 [Azure Site Recovery](site-recovery-overview.md)를 조합하여 애플리케이션의 SQL Server 백 엔드를 보호하는 방법을 설명합니다.

시작 하기 전에 SQL Server 재해 복구 기능을 장애 조치 클러스터링, Always On 가용성 그룹, 데이터베이스 미러링, 로그 전달, 활성 지리적 복제 및 자동 장애 조치 그룹을 이해 해야 합니다.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>Site Recovery를 사용 하 여 SQL Server BCDR 기술과의 통합에 대 한 DR 권장 사항

복구 SQL server BCDR 기술을 선택 기준으로 RTO 및 RPO 요구 사항에 기반 해야는 아래 테이블입니다. 선택 항목을 설정 되 면 전체 응용 프로그램의 복구를 오케스트레이션 하는 기술의 장애 조치 작업을 사용 하 여 Site Recovery는 통합할 수 있습니다.

**배포 유형** | **BCDR 기술** | **SQL에 대 한 예상된 RTO** | **SQL에 대 한 예상된 RPO** |
--- | --- | --- | ---
온-프레미스 또는 Azure IaaS VM에서 SQL Server| **[Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | 보조 복제본을 기본으로 확인 하는 데 걸리는 시간에 해당 | 복제는 보조 복제본으로 비동기적 이며, 따라서 일부 데이터 손실이 발생 하는.
온-프레미스 또는 Azure IaaS VM에서 SQL Server| **[장애 조치 클러스터링 (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | 노드 간 장애 조치 하는 데 걸리는 시간에 해당 | 공유 저장소를 사용 하 여, 따라서 저장소 인스턴스의 동일한 뷰는 장애 조치에 사용할 수 있습니다.
온-프레미스 또는 Azure IaaS VM에서 SQL Server| **[데이터베이스 미러링 (성능 우선 모드)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | 웜 대기 서버로 미러 서버를 사용 하는 서비스를 강제 적용 하는 데 걸리는 시간에 해당 합니다. | 복제는 비동기적이며, 미러 데이터베이스에 주 데이터베이스보다 약간 뒤처질 수 있습니다. 간격은 일반적으로 작지만 howvever, 주 또는 미러 서버 시스템이 부하가 높을 경우 상당한 될 수 있습니다.<br></br>로그 전달은 데이터베이스 미러링을 보완 수 있으며 비동기 데이터베이스 미러링의 대 안으로
SQL Azure에서 PaaS로<br></br>(탄력적 풀을 SQL database 서버) | **활성 지역 복제** | 트리거된 후 30 초<br></br>장애 조치가 보조 데이터베이스 중 하나로 활성화된 경우 모든 다른 보조가 새 보조로 자동으로 연결됩니다. | 5 초의 RPO<br></br>활성 지역 복제는 스냅숏 격리를 사용 하 여 보조 데이터베이스를 주 데이터베이스에서 커밋된 트랜잭션을 비동기적으로 복제 하도록 SQL server Always On 기술을 활용 합니다. <br></br>보조 데이터는 절대 부분 트랜잭션을 갖지 하도록 보장 됩니다.
SQL Azure에서 활성 지역 복제를 사용 하 여 구성 하는 PaaS로<br></br>(SQL Database 관리 되는 인스턴스 탄력적 풀을 SQL database 서버) | **자동 장애 조치(failover) 그룹** | 1 시간 RTO | 5 초의 RPO<br></br>자동 장애 조치 그룹은 활성 지역 복제를 기반으로 하는 그룹 의미 체계를 제공하지만 동일한 비동기 복제 메커니즘이 사용됩니다.
온-프레미스 또는 Azure IaaS VM에서 SQL Server| **Azure Site Recovery로 복제** | 일반적으로 15 분 미만입니다. [자세한](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 에 Azure Site Recovery에서 제공 하는 RTO SLA에 알아봅니다. | 응용 프로그램 일관성 및 크래시 일관성에 대 일 분 동안 1 시간입니다. 

> [!NOTE]
> 몇 가지 중요 한 고려 사항 Azure Site Recovery를 사용 하 여 SQL 워크 로드를 보호 하는 경우:
> * Azure Site Recovery는 응용 프로그램을 알 수 없는 하 고 따라서 Azure Site Recovery에서 모든 버전의 지원된 운영 체제에 배포 된 SQL server 보호할 수 있습니다. [자세히 알아보기](vmware-physical-azure-support-matrix.md#replicated-machines).
> * Site Recovery를 사용 하 여 Azure에 Hyper-v, VMware 또는 물리적 인프라에 배포 하도록 선택할 수 있습니다. 따르세요 합니다 [지침](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) Azure Site Recovery를 사용 하 여 SQL Server 클러스터를 보호 하는 방법에 대 한 문서의 끝입니다.
> * 확인에 데이터 변경 률 (초당 쓰기 바이트) 컴퓨터에서 관찰 되는 [Site Recovery 제한](vmware-physical-azure-support-matrix.md#churn-limits)합니다. Windows 컴퓨터에서 작업 관리자 성능 탭에서이 볼 수 있습니다. 쓰기를 관찰 합니다. 각 디스크에 대 한 속도입니다.
> * Azure Site Recovery는 저장소 공간 다이렉트에 장애 조치 클러스터 인스턴스는 복제를 지원합니다. [자세히 알아보기](azure-to-azure-how-to-enable-replication-s2d-vms.md).
 

## <a name="disaster-recovery-of-application"></a>응용 프로그램의 재해 복구

**Azure Site Recovery는 테스트 장애 조치 및 복구 계획을 통해 전체 응용 프로그램의 장애 조치를 오케스트레이션합니다.** 

필요에 따라 완전히 사용자 지정 된 복구 계획을 확인 하기 위한 몇 가지 필수 있습니다. 모든 SQL Server 배포는 일반적으로 Active Directory를 해야합니다. 또한 응용 프로그램 계층에 대 한 연결도 필요합니다.

### <a name="step-1-set-up-active-directory"></a>1단계: Active Directory 설정

SQL Server를 제대로 실행하려면 보조 복구 사이트에서 Active Directory를 설정합니다.

* **소형 엔터프라이즈**—온-프레미스 사이트에 대한 소량의 애플리케이션 및 단일 도메인 컨트롤러가 있고 전체 사이트를 장애 조치하려는 경우, Site Recovery 복제를 사용하여 도메인 컨트롤러를 보조 데이터센터 또는 Azure로 복제하는 것이 좋습니다.
* **중형 및 대형 엔터프라이즈**—다량의 애플리케이션 및 Active Directory 포리스트가 있고 애플리케이션 또는 워크로드에 대한 장애 조치를 하려는 경우, 보조 데이터센터 또는 Azure에서 추가 도메인 컨트롤러를 설정하는 것이 좋습니다. Always On 가용성 그룹을 사용하여 원격 사이트로 복구하는 경우, 복구된 SQL Server 인스턴스에 대해 사용하기 위해 보조 사이트 또는 Azure에서 다른 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

이 문서의 지침에서는 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정합니다. Site Recovery를 사용한 Active Directory 보호에 대해 [자세히 알아보세요](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>2단계: 다른 응용 프로그램 계층 및 웹 계층을 사용 하 여 연결 확인

데이터베이스 계층에 대상 Azure 지역에서에서 실행 되 면 응용 프로그램 및 웹 계층의 연결을 확인 합니다. 테스트 장애 조치를 사용 하 여 연결 유효성을 검사 하려면 사전에 필요한 단계를 수행 해야 합니다.

몇 가지 예를 사용 하 여 연결 고려 사항에 대 한 응용 프로그램을 디자인할 수 있습니다 하는 방법을 이해 합니다.
* [클라우드 재해 복구를 위한 응용 프로그램 디자인](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [탄력적 풀 재해 복구 전략](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>3단계: Always On, 활성 지리적 복제 또는 응용 프로그램 장애 조치에 대 한 자동 장애 조치 그룹을 사용 하 여 통합

Always On BCDR 기술과, 활성 지역 복제 및 자동 장애 조치 그룹 보조 복제본이 대상 Azure 지역에서에서 실행 중인 SQL server를 포함 합니다. 따라서 응용 프로그램 장애 조치에 대 한 첫 번째 단계 (보조에서 도메인 컨트롤러를 이미 있다고 가정) 되는 기본이 복제본을 확인 하는 것입니다. 이 단계에는 자동 장애 조치를 수행 하려는 경우에 필요 하지 않을 수 있습니다. 데이터베이스 장애 조치가 완료 된 후에 장애 조치 웹 또는 응용 프로그램 계층 해야 합니다.

> [!NOTE] 
> Azure Site Recovery를 사용 하 여 SQL 컴퓨터를 보호 하는 경우 이러한 컴퓨터의 복구 그룹을 만들고 복구 계획의 장애 조치를 추가 하기만 하면 됩니다.

[복구 계획을 만듭니다](site-recovery-create-recovery-plans.md) 응용 프로그램 및 웹 계층 가상 머신을 사용 하 여 합니다. 수행 된 데이터베이스 계층의 장애 조치를 추가 하려면 다음 단계:

1. Azure Automation 계정에 스크립트를 가져옵니다. 여기에는 [Resource Manager 가상 머신](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) 및 [클래식 가상 머신](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)에서 SQL 가용성 그룹의 장애 조치(failover) 스크립트가 포함됩니다.

    [![Azure에 배포](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. ASR-SQL-FailoverAG를 복구 계획의 첫 번째 그룹에 대한 이전 작업으로 추가합니다.

1. 스크립트에서 사용할 수 있는 지침에 따라 가용성 그룹의 이름을 제공하는 자동화 변수를 만듭니다.

### <a name="step-4-conduct-a-test-failover"></a>4단계: 테스트 장애 조치를 수행 합니다.

SQL Always On과 같은 일부 BCDR 기술과 테스트 장애 조치를 기본적으로 지원 하지 않습니다. 따라서 다음과 같은 방법이 권장 **이러한 기술을 통합 하는 경우에**:

1. Azure에서 가용성 그룹 복제본을 호스팅하는 가상 머신에서 [Azure Backup](../backup/backup-azure-arm-vms.md)을 설정합니다.

1. 복구 계획의 테스트 장애 조치를 트리거하기 전에 이전 단계에서 수행된 백업에서 가상 머신을 복구합니다.

    ![Azure Backup에서 복원](./media/site-recovery-sql/restore-from-backup.png)

1. 백업에서 복원된 가상 머신에 [쿼럼을 강제](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)합니다.

1. 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 IP에 수신기의 IP를 업데이트합니다.

    ![수신기 IP 업데이트](./media/site-recovery-sql/update-listener-ip.png)

1. 수신기를 온라인 상태로 만듭니다.

    ![수신기를 온라인 상태로 만들기](./media/site-recovery-sql/bring-listener-online.png)

1. 각 가용성 그룹 수신기에 해당하는 프런트 엔드 IP 풀에서 만든 IP와 백 엔드 풀에 추가된 SQL 가상 머신을 통해 부하 분산 장치를 만듭니다.

     ![부하 분산 장치 만들기 - 프런트 엔드 IP 풀](./media/site-recovery-sql/create-load-balancer1.png)

    ![부하 분산 장치 만들기 - 백 엔드 풀](./media/site-recovery-sql/create-load-balancer2.png)

1. 웹 계층 뒤에 후속 복구 그룹에서이 복구 계획에서 응용 프로그램 계층의 장애 조치를 추가 합니다. 
1. 테스트 응용 프로그램의 종단 간 장애 조치 하려면 복구 계획의 테스트 장애 조치를 수행 합니다.

## <a name="steps-to-do-a-failover"></a>장애 조치(failover)를 수행하는 단계

3 단계에서에서 복구 계획에 스크립트를 추가 하 고 4 단계에서에서 특수 한 방법으로 테스트 장애 조치를 수행 하 여 유효성을 검사 했으면, 3 단계에서에서 만든 복구 계획의 장애 조치를 수행할 수 있습니다.

응용 프로그램 및 웹 계층에 대 한 장애 조치 단계는 동일 하 게 테스트 장애 조치 및 장애 복구 계획에서 note 합니다.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>SQL Server 클러스터 (표준 에디션/SQL Server 2008 R2) 보호 하는 방법

SQL Server Standard 에디션 또는 SQL Server 2008 R2를 실행하는 클러스터의 경우 SQL Server를 보호하기 위해 Site Recovery 복제를 사용하는 것이 좋습니다.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure 및 온-프레미스와 Azure에 azure

Site Recovery는 게스트를 제공 하지 않는 Azure 지역에 복제 하는 경우 클러스터 지원 합니다. 또한 SQL Server는 Standard 에디션에 대해 저비용 재해 복구 솔루션을 제공하지 않습니다. 이 시나리오에서는 독립 실행형 기본 위치에서 SQL Server를 SQL Server 클러스터를 보호 하 고 보조의 복구는 것이 좋습니다.

1. 기본 Azure 지역 또는 온-프레미스 사이트에서 추가 독립 실행형 SQL Server 인스턴스를 구성 합니다.
1. 보호하려는 데이터베이스에 대한 미러로 제공하기 위해 인스턴스를 구성합니다. 높은 보안 모드에서 미러링을 구성합니다.
1. 기본 사이트에서 Site Recovery 구성 ([Azure](azure-to-azure-tutorial-enable-replication.md)를 [Hyper-v](site-recovery-hyper-v-site-to-azure.md) 하거나 [VMware v m/물리적 서버)](site-recovery-vmware-to-azure-classic.md)합니다.
1. Site Recovery 복제를 사용 하 여 새 SQL Server 인스턴스를 보조 사이트로 복제 합니다. 높은 보안 미러 복사 이므로 기본 클러스터를 사용 하 여 동기화 하지만 해당 Site Recovery 복제를 사용 하 여 복제 됩니다.


![표준 클러스터](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>장애 복구 고려 사항

SQL Server Standard 클러스터의 경우, 계획되지 않은 장애 조치 후의 장애 복구는 SQL Server 백업이 필요하며 미러의 재설정으로 미러 인스턴스에서 원래 클러스터로 복구해야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>어떻게 않습니다 SQL 가져오기 사용이 허가 된 Azure Site Recovery로 보호 하는 경우
SQL Server에 대한 Azure Site Recovery 복제에는 모든 Azure Site Recovery 시나리오(온-프레미스와 Azure 간 재해 복구 또는 지역 간 Azure IaaS 재해 복구)에 대한 Software Assurance – 재해 복구 혜택이 적용됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Azure Site Recovery는 필자의 SQL 버전을 지원 하나요?
Azure Site Recovery는 응용 프로그램을 알 수 없는 합니다. 따라서 Azure Site Recovery에서 모든 버전의 지원된 운영 체제에 배포 된 SQL server 보호할 수 있습니다. [자세히 알아보기](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>다음 단계
* Site Recovery 아키텍처에 대해 [자세히 알아봅니다](site-recovery-components.md).
* Azure에서 SQL 서버에 대 한에 대해 자세히 알아보세요 [고가용성 솔루션](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) 보조 Azure 지역에서 복구 합니다.
* Azure에서 SQL Database에 대 한 자세한 정보에 대 한 합니다 [비즈니스 연속성](../sql-database/sql-database-business-continuity.md) 및 [고가용성](../sql-database/sql-database-high-availability.md) 보조 Azure 지역에서 복구를 위한 옵션입니다.
* 온-프레미스에서 SQL server 컴퓨터에 대 한 [자세한](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) 복구 Azure Virtual Machines에서의 고가용성 옵션에 대 한 합니다.
