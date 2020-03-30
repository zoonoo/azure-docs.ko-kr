---
title: Azure 사이트 복구를 사용 하 고 SQL 서버에 대 한 재해 복구 설정
description: 이 문서에서는 SQL Server 및 Azure 사이트 복구를 사용하여 SQL Server에 대한 재해 복구를 설정하는 방법에 대해 설명합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084747"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server에 대한 재해 복구 설정

이 문서에서는 응용 프로그램의 SQL Server 백 엔드를 보호하는 방법을 설명합니다. SQL Server 비즈니스 연속성 및 BCDR(재해 복구) 기술과 [Azure 사이트 복구를](site-recovery-overview.md)조합하여 사용합니다.

시작하기 전에 SQL Server 재해 복구 기능을 이해해야 합니다. 이러한 기능은 다음과 같습니다.

* 장애 조치(failover) 클러스터링
* Always On 가용성 그룹
* 데이터베이스 미러링
* 로그 전달
* 활성 지리적 복제
* 자동 장애 조치 그룹

## <a name="combining-bcdr-technologies-with-site-recovery"></a>BCDR 기술과 사이트 복구 결합

SQL Server 인스턴스를 복구하는 BCDR 기술을 선택하는 것은 다음 표에 설명된 대로 복구 시간 목표(RTO) 및 RPO(복구 지점 목표) 요구 사항을 기반으로 해야 합니다. 사이트 복구를 선택한 기술의 장애 조치 작업과 결합하여 전체 응용 프로그램의 복구를 오케스트레이션합니다.

배포 유형 | BCDR 기술 | SQL 서버에 대한 예상 RTO | SQL 서버에 대한 예상 RPO |
--- | --- | --- | ---
서비스(IaaS) 가상 시스템(VM) 또는 온-프레미스에서 Azure 인프라의 SQL Server입니다.| [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | 보조 복제본을 기본 복제본으로 만드는 데 걸린 시간입니다. | 보조 복제본에 대한 복제는 비동기이므로 일부 데이터 손실이 있습니다.
Azure IaaS VM 또는 온-프레미스의 SQL 서버입니다.| [장애 조치(failover) 클러스터링(Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | 노드 간에 장애 조치(failover)하는 데 걸린 시간입니다. | Always On FCI는 공유 저장소를 사용하기 때문에 장애 조치(failover)에서 저장소 인스턴스의 동일한 보기를 사용할 수 있습니다.
Azure IaaS VM 또는 온-프레미스의 SQL 서버입니다.| [데이터베이스 미러링(고성능 모드)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | 미러 서버를 웜 대기 서버로 사용하는 서비스를 강제로 사용하는 데 걸린 시간입니다. | 복제는 비동기적이며, 미러 데이터베이스에 주 데이터베이스보다 약간 뒤처질 수 있습니다. 지연은 일반적으로 작습니다. 그러나 주 서버 또는 미러 서버의 시스템이 과부하를 겪고 있는 경우 커질 수 있습니다.<br/><br/>로그 전달은 데이터베이스 미러링에 대한 보충이 될 수 있습니다. 비동기 데이터베이스 미러링에 대한 유리한 대안입니다.
Azure에서 PaaS(서비스)로 플랫폼으로 SQL을 사용합니다.<br/><br/>이 배포 유형에는 탄력적 풀및 Azure SQL Database 서버가 포함됩니다. | 활성 지리적 복제 | 장애 조치 후 30초 가 트리거됩니다.<br/><br/>보조 데이터베이스 중 하나에 대해 장애 조치(failover)가 활성화되면 다른 모든 보조 데이터베이스가 새 주 데이터베이스에 자동으로 연결됩니다. | 5초의 RPO.<br/><br/>활성 지역 복제는 SQL Server의 Always On 기술을 사용합니다. 스냅숏 격리를 사용하여 기본 데이터베이스에서 커밋된 트랜잭션을 보조 데이터베이스로 비동기적으로 복제합니다.<br/><br/>보조 데이터는 부분 트랜잭션이 없는 것으로 보장됩니다.
Azure에서 활성 지역 복제로 구성된 PaaS로 SQL입니다.<br/><br/>이 배포 유형에는 SQL Database 관리 인스턴스, 탄력적 풀 및 SQL Database 서버가 포함됩니다. | 자동 장애 조치 그룹 | 1 시간의 RTO. | 5초의 RPO.<br/><br/>자동 장애 조치 그룹은 활성 지역 복제 위에 그룹 의미 체계를 제공합니다. 그러나 동일한 비동기 복제 메커니즘이 사용됩니다.
Azure IaaS VM 또는 온-프레미스의 SQL 서버입니다.| Azure 사이트 복구를 사용 하 고 복제 | RTO는 일반적으로 15분 미만입니다. 자세한 내용은 사이트 [복구에서 제공하는 RTO SLA를](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)참조하십시오. | 응용 프로그램 일관성을 위해 1시간, 충돌 일관성을 위해 5분. 낮은 RPO를 찾고 있다면 다른 BCDR 기술을 사용하십시오.

> [!NOTE]
> 사이트 복구를 통해 SQL 워크로드를 보호하는 데 도움이 되는 몇 가지 중요한 사항은 다음과 같다.
> * 사이트 복구는 응용 프로그램에 구애받지 않습니다. 사이트 복구는 지원되는 운영 체제에 배포된 모든 버전의 SQL Server를 보호하는 데 도움이 될 수 있습니다. 자세한 내용은 복제된 컴퓨터의 [복구를 위한 지원 매트릭스를](vmware-physical-azure-support-matrix.md#replicated-machines) 참조하십시오.
> * Azure, Hyper-V, VMware 또는 물리적 인프라의 모든 배포에 사이트 복구를 사용하도록 선택할 수 있습니다. 사이트 복구를 통해 SQL Server [클러스터를 보호하는 방법에](#how-to-help-protect-a-sql-server-cluster) 대한 이 문서의 끝부분에 있는 지침을 따르십시오.
> * 컴퓨터에서 관찰된 데이터 변경 률이 [사이트 복구 제한](vmware-physical-azure-support-matrix.md#churn-limits)내에 있는지 확인합니다. 변경 률은 초당 쓰기 바이트단위로 측정됩니다. Windows를 실행하는 컴퓨터의 경우 작업 관리자에서 **성능** 탭을 선택하여 이 변경 률을 볼 수 있습니다. 각 디스크의 쓰기 속도를 관찰합니다.
> * 사이트 복구는 저장소 공간 직접에서 장애 조치 클러스터 인스턴스의 복제를 지원합니다. 자세한 내용은 [저장소 공간 직접 복제를 사용하도록 설정하는 방법을 참조하세요.](azure-to-azure-how-to-enable-replication-s2d-vms.md)

## <a name="disaster-recovery-of-an-application"></a>응용 프로그램의 재해 복구

사이트 복구는 복구 계획의 도움으로 테스트 장애 조치 및 전체 응용 프로그램의 장애 조치(failover)를 오케스트레이션합니다.

복구 계획이 필요에 따라 완전히 사용자 지정되었는지 확인하는 몇 가지 전제 조건이 있습니다. 모든 SQL Server 배포에는 일반적으로 Active Directory 배포가 필요합니다. 또한 응용 프로그램 계층에 대한 연결이 필요합니다.

### <a name="step-1-set-up-active-directory"></a>1단계: Active 디렉토리 설정

SQL Server가 제대로 실행되도록 보조 복구 사이트에서 Active Directory를 설정합니다.

* **소규모 엔터프라이즈**: 온-프레미스 사이트에 대한 적은 수의 응용 프로그램과 단일 도메인 컨트롤러가 있습니다. 전체 사이트에서 장애 조치(failover)하려면 사이트 복구 복제를 사용합니다. 이 서비스는 도메인 컨트롤러를 보조 데이터 센터 또는 Azure에 복제합니다.
* **중소기업:** 추가 도메인 컨트롤러를 설정해야 할 수 있습니다.
  - 많은 수의 응용 프로그램이 있고 Active Directory 포리스트가 있고 응용 프로그램 또는 워크로드별로 장애 조치(failover)하려는 경우 보조 데이터 센터 또는 Azure에서 다른 도메인 컨트롤러를 설정합니다.
  -  Always On 가용성 그룹을 사용하여 원격 사이트로 복구하는 경우 보조 사이트 또는 Azure에서 다른 도메인 컨트롤러를 설정합니다. 이 도메인 컨트롤러는 복구된 SQL Server 인스턴스에 사용됩니다.

이 문서의 지침은 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정합니다. 자세한 내용은 [사이트 복구를 사용하여 Active Directory를 보호하는 데 도움이 되는](site-recovery-active-directory.md)절차를 참조하세요.

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>2단계: 다른 계층과의 연결 보장

데이터베이스 계층이 대상 Azure 리전에서 실행된 후 응용 프로그램 및 웹 계층과 연결되어 있는지 확인합니다. 테스트 장애 조치(failover)를 통한 연결의 유효성을 검사하기 위해 필요한 단계를 미리 수행합니다.

연결 고려 사항에 대한 응용 프로그램을 디자인하는 방법을 이해하려면 다음 예제를 참조하십시오.

* [클라우드 재해 복구를 위한 응용 프로그램 설계](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [탄성 풀 재해 복구 전략](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>3단계: Always On, 활성 지역 복제 및 자동 장애 조치 그룹과 상호 운용

BCDR 기술 Always On, 활성 지역 복제 및 자동 장애 조치 그룹에는 대상 Azure 지역에서 실행 중인 SQL Server의 보조 복제본이 있습니다. 응용 프로그램 장애 조치의 첫 번째 단계는 이 복제본을 기본 으로 지정하는 것입니다. 이 단계에서는 이미 보조 컨트롤러에 도메인 컨트롤러가 있다고 가정합니다. 자동 장애 조치(fail-failover)를 수행하도록 선택한 경우 이 단계가 필요하지 않을 수 있습니다. 데이터베이스 장애 조치(failover)가 완료된 후에만 웹 및 응용 프로그램 계층을 장애 조치합니다.

> [!NOTE]
> 사이트 복구를 사용하여 SQL 컴퓨터를 보호하는 데 도움이 된 경우 이러한 컴퓨터의 복구 그룹을 만들고 복구 계획에 장애 조치(failover)를 추가하기만 하면 됩니다.

응용 프로그램 및 웹 계층 가상 컴퓨터로 [복구 계획을 만듭니다.](site-recovery-create-recovery-plans.md) 다음 단계는 데이터베이스 계층의 장애 복구를 추가하는 방법을 보여 주며 다음과 같은 단계를 보여 주며 있습니다.

1. [리소스 관리자 가상 컴퓨터와](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) 클래식 [가상 컴퓨터](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)모두에서 SQL 가용성 그룹을 장애 조치하는 스크립트를 가져옵니다. Azure 자동화 계정으로 스크립트를 가져옵니다.

    [!["Azure에 배포" 로고 이미지](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. ASR-SQL-FailoverAG 스크립트를 복구 계획의 첫 번째 그룹의 사전 작업으로 추가합니다.

1. 스크립트에서 사용할 수 있는 지침에 따라 자동화 변수를 만듭니다. 이 변수는 가용성 그룹의 이름을 제공합니다.

### <a name="step-4-conduct-a-test-failover"></a>4단계: 테스트 장애 조치(failover) 수행

SQL Always On과 같은 일부 BCDR 기술은 테스트 장애 조치(failover)를 기본적으로 지원하지 않습니다. *이러한 기술을 사용할 때만*다음 방법을 사용하는 것이 좋습니다.

1. Azure에서 가용성 그룹 복제본을 호스팅하는 VM에서 Azure [백업을](../backup/backup-azure-arm-vms.md) 설정합니다.

1. 복구 계획의 테스트 장애 조치를 트리거하기 전에 이전 단계에서 수행한 백업에서 VM을 복구합니다.

    ![Azure Backup에서 구성을 복원하기 위한 창을 보여주는 스크린샷](./media/site-recovery-sql/restore-from-backup.png)

1. 백업에서 복원된 VM의 [쿼럼을 강제로 합니다.](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)

1. 리스너가 테스트 장애 조치 네트워크에서 사용할 수 있는 주소로 IP 주소를 업데이트합니다.

    ![규칙 창 및 IP 주소 속성 대화 상자의 스크린샷](./media/site-recovery-sql/update-listener-ip.png)

1. 수신기를 온라인 상태로 전환합니다.

    ![서버 이름 및 상태를 표시하는 Content_AG 레이블이 지정된 창의 스크린샷](./media/site-recovery-sql/bring-listener-online.png)

1. 장애 조치 네트워크의 로드 밸런서에 각 가용성 그룹 수신기에 해당하는 프런트 엔드 IP 주소 풀과 백 엔드 풀의 SQL Server VM을 사용하여 하나의 IP 주소가 있는지 확인합니다.

     !["SQL-AlwaysOn-LB - 프런트 엔드 IP 풀"이라는 제목의 창 스크린 샷](./media/site-recovery-sql/create-load-balancer1.png)

    !["SQL-AlwaysOn-LB - 백엔드 IP 풀"이라는 제목의 창 스크린샷](./media/site-recovery-sql/create-load-balancer2.png)

1. 이후 복구 그룹에서는 응용 프로그램 계층의 장애 조치(failover)를 추가한 다음 이 복구 계획에 대한 웹 계층을 추가합니다.

1. 복구 계획의 테스트 장애 수행을 수행하여 응용 프로그램의 종단 간 장애 조치(failover)를 테스트합니다.

## <a name="steps-to-do-a-failover"></a>장애 조치(failover)를 수행하는 단계

3단계에서 스크립트를 추가하고 4단계에서 유효성을 검사한 후 3단계에서 만든 복구 계획의 장애 조치(failover)를 수행할 수 있습니다.

응용 프로그램 및 웹 계층에 대한 장애 조치 단계는 테스트 장애 조치 및 장애 조치 복구 계획 모두에서 동일해야 합니다.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>SQL Server 클러스터를 보호하는 방법

SQL Server 표준 에디션 또는 SQL Server 2008 R2를 실행하는 클러스터의 경우 사이트 복구 복제를 사용하여 SQL Server를 보호하는 것이 좋습니다.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure에서 Azure로, 온-프레미스에서 Azure로

사이트 복구Azure 지역으로 복제할 때 게스트 클러스터 지원을 제공하지 않습니다. SQL Server 표준 에디션은 저비용 재해 복구 솔루션도 제공하지 않습니다. 이 시나리오에서는 기본 위치에 있는 독립 실행형 SQL Server 인스턴스에 SQL Server 클러스터를 보호 하 고 보조에서 복구 하는 것이 좋습니다.

1. 기본 Azure 지역 또는 온-프레미스 사이트에서 추가 독립 실행형 SQL Server 인스턴스를 구성합니다.

1. 보호할 데이터베이스에 대한 미러 역할을 하도록 인스턴스를 구성합니다. 높은 안전 모드에서 미러링을 구성합니다.

1. [Azure,](azure-to-azure-tutorial-enable-replication.md) [Hyper-V](site-recovery-hyper-v-site-to-azure.md)또는 [VM웨어 VM 및 물리적 서버에](site-recovery-vmware-to-azure-classic.md)대한 기본 사이트에서 사이트 복구를 구성합니다.

1. 사이트 복구 복제를 사용하여 새 SQL Server 인스턴스를 보조 사이트에 복제합니다. 안전도가 높은 미러 복사본이기 때문에 기본 클러스터와 동기화되지만 사이트 복구 복제를 사용하여 복제됩니다.

   ![기본 사이트, 사이트 복구 및 Azure 간의 관계 및 흐름을 보여 주는 표준 클러스터이미지](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>장애 복구 고려 사항

SQL Server 표준 클러스터의 경우 계획되지 않은 장애 조치 후 장애 복구에는 SQL Server 백업 및 복원이 필요합니다. 이 작업은 미러 인스턴스에서 미러를 다시 설치하여 원래 클러스터까지 수행됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>사이트 복구와 함께 사용할 때 SQL Server의 라이선스는 어떻게 됩니까?

SQL Server에 대한 사이트 복구 복제는 소프트웨어 보증 재해 복구 이점에 따라 다룹니다. 이 범위는 모든 사이트 복구 시나리오에 적용됩니다: 온-프레미스 에서 Azure 재해 복구 및 지역 간 Azure IaaS 재해 복구. 자세한 내용은 [Azure 사이트 복구 가격을](https://azure.microsoft.com/pricing/details/site-recovery/) 참조하십시오.

### <a name="will-site-recovery-support-my-sql-server-version"></a>사이트 복구가 내 SQL Server 버전을 지원합니까?

사이트 복구는 응용 프로그램에 구애받지 않습니다. 사이트 복구는 지원되는 운영 체제에 배포된 모든 버전의 SQL Server를 보호하는 데 도움이 될 수 있습니다. 자세한 내용은 복제된 컴퓨터의 [복구를 위한 지원 매트릭스를](vmware-physical-azure-support-matrix.md#replicated-machines) 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [사이트 복구 아키텍처에](site-recovery-components.md)대해 자세히 알아봅니다.
* Azure의 SQL Server의 경우 보조 Azure 리전에서 복구를 위한 [고가용성 솔루션에](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) 대해 자세히 알아봅니다.
* SQL Database의 경우 보조 Azure 리전에서 복구를 위한 [비즈니스 연속성](../sql-database/sql-database-business-continuity.md) 및 [고가용성](../sql-database/sql-database-high-availability.md) 옵션에 대해 자세히 알아봅니다.
* 온-프레미스의 SQL Server 컴퓨터의 경우 Azure 가상 머신에서 복구를 위한 [고가용성 옵션에](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) 대해 자세히 알아봅니다.
