---
title: Azure Site Recovery를 사용 하 여 SQL Server에 대 한 재해 복구 설정
description: 이 문서에서는 SQL Server 및 Azure Site Recovery를 사용 하 여 SQL Server에 대 한 재해 복구를 설정 하는 방법을 설명 합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 4146553d59607e1512d8f15391d143d44815cea9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84016477"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server에 대한 재해 복구 설정

이 문서에서는 응용 프로그램의 SQL Server 백 엔드를 보호 하는 방법을 설명 합니다. SQL Server BCDR (비즈니스 연속성 및 재해 복구) 기술과 [Azure Site Recovery](site-recovery-overview.md)의 조합을 사용 하 여이 작업을 수행 합니다.

시작 하기 전에 재해 복구 기능 SQL Server 이해 하 고 있어야 합니다. 이러한 기능은 다음과 같습니다.

* 장애 조치(failover) 클러스터링
* Always On 가용성 그룹
* 데이터베이스 미러링
* 로그 전달
* 활성 지리적 복제
* 자동 장애 조치 그룹

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Site Recovery와 BCDR 기술 결합

SQL Server 인스턴스를 복구 하는 BCDR 기술의 선택은 다음 표에 설명 된 대로 RTO (복구 시간 목표) 및 RPO (복구 지점 목표)를 기반으로 해야 합니다. Site Recovery를 선택한 기술의 장애 조치 (failover) 작업과 결합 하 여 전체 응용 프로그램의 복구를 오케스트레이션 합니다.

배포 유형 | BCDR 기술 | SQL Server에 대 한 RTO 필요 | SQL Server에 대 한 예상 RPO |
--- | --- | --- | ---
Azure IaaS (infrastructure as a service) VM (가상 머신) 또는 온-프레미스에서 SQL Server.| [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | 보조 복제본을 주 복제본으로 만드는 데 걸린 시간입니다. | 보조 복제본에 대 한 복제는 비동기 이기 때문에 데이터 손실이 발생 합니다.
Azure IaaS VM 또는 온-프레미스에서 SQL Server 합니다.| [장애 조치(failover) 클러스터링(Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | 노드 간 장애 조치 (failover)에 소요 된 시간입니다. | Always On FCI는 공유 저장소를 사용 하므로 장애 조치 (failover) 시 저장소 인스턴스의 동일한 뷰를 사용할 수 있습니다.
Azure IaaS VM 또는 온-프레미스에서 SQL Server 합니다.| [데이터베이스 미러링 (성능 우선 모드)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | 미러 서버를 웜 대기 서버로 사용 하는 서비스를 강제 적용 하는 데 걸린 시간입니다. | 복제는 비동기적이며, 미러 데이터베이스에 주 데이터베이스보다 약간 뒤처질 수 있습니다. 지연 시간은 일반적으로 작습니다. 하지만 주 서버 또는 미러 서버의 시스템 부하가 많은 경우에는 크기가 커질 수 있습니다.<br/><br/>로그 전달은 데이터베이스 미러링을 보완할 수 있습니다. 비동기 데이터베이스 미러링의 좋은 대안입니다.
SQL as Azure의 PaaS (platform as a service)<br/><br/>이 배포 유형에는 단일 데이터베이스 및 탄력적 풀이 포함 됩니다. | 활성 지리적 복제 | 장애 조치 (failover)가 트리거된 후 30 초<br/><br/>보조 데이터베이스 중 하나에 대해 장애 조치 (failover)가 활성화 되 면 다른 모든 보조 데이터베이스는 새 주 데이터베이스에 자동으로 연결 됩니다. | RPO는 5 초입니다.<br/><br/>활성 지역 복제는 SQL Server의 Always On 기술을 사용 합니다. Snapshot 격리를 사용 하 여 주 데이터베이스의 커밋된 트랜잭션을 보조 데이터베이스로 비동기적으로 복제 합니다.<br/><br/>보조 데이터에는 부분적 트랜잭션이 포함 되지 않을 수 있습니다.
Azure에서 활성 지역 복제를 사용 하 여 구성 된 SQL as PaaS.<br/><br/>이 배포 유형에는 SQL Database 관리 되는 인스턴스, 탄력적 풀 및 단일 데이터베이스가 포함 됩니다. | 자동 장애 조치 그룹 | 1 시간의 RTO | RPO는 5 초입니다.<br/><br/>자동 장애 조치 그룹은 활성 지역 복제의 맨 위에 그룹 의미 체계를 제공 합니다. 하지만 동일한 비동기 복제 메커니즘이 사용 됩니다.
Azure IaaS VM 또는 온-프레미스에서 SQL Server 합니다.| Azure Site Recovery 복제 | RTO는 일반적으로 15 분 미만입니다. 자세한 내용은 [Site Recovery에서 제공 하는 RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)를 읽어 보세요. | 응용 프로그램 일관성을 위해 1 시간, 크래시 일관성의 경우 5 분 더 낮은 RPO를 찾고 있는 경우 다른 BCDR 기술을 사용 합니다.

> [!NOTE]
> Site Recovery를 사용 하 여 SQL 작업을 보호 하는 데 도움이 되는 몇 가지 중요 한 고려 사항은 다음과 같습니다.
> * Site Recovery는 응용 프로그램에 독립적입니다. Site Recovery은 지원 되는 운영 체제에 배포 된 모든 버전의 SQL Server를 보호 하는 데 도움이 됩니다. 자세히 알아보려면 복제 된 컴퓨터 [복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md#replicated-machines) 를 참조 하세요.
> * Azure, Hyper-v, VMware 또는 물리적 인프라의 모든 배포에 대해 Site Recovery를 사용 하도록 선택할 수 있습니다. Site Recovery를 사용 하 여 [SQL Server 클러스터를 보호 하는 방법](#how-to-help-protect-a-sql-server-cluster) 에 대 한 자세한 내용은이 문서의 끝에 있는 지침을 따르세요.
> * 컴퓨터에서 관찰 된 데이터 변경 률이 [Site Recovery 제한](vmware-physical-azure-support-matrix.md#churn-limits)내에 있는지 확인 하십시오. 변경 비율은 초당 쓰기 바이트 수로 측정 됩니다. Windows를 실행 하는 컴퓨터의 경우 작업 관리자에서 **성능** 탭을 선택 하 여이 변경 률을 확인할 수 있습니다. 각 디스크에 대 한 쓰기 속도를 관찰 합니다.
> * Site Recovery은 스토리지 공간 다이렉트에서 장애 조치 (Failover) 클러스터 인스턴스의 복제를 지원 합니다. 자세히 알아보려면 [스토리지 공간 다이렉트 복제를 사용 하도록 설정 하는 방법](azure-to-azure-how-to-enable-replication-s2d-vms.md)을 참조 하세요.

## <a name="disaster-recovery-of-an-application"></a>응용 프로그램의 재해 복구

복구 계획의 도움으로 전체 응용 프로그램의 테스트 장애 조치 (failover) 및 장애 조치 (failover)를 오케스트레이션 Site Recovery.

사용자의 요구에 따라 복구 계획이 완전히 사용자 지정 되도록 하기 위한 몇 가지 필수 구성 요소가 있습니다. 모든 SQL Server 배포에는 일반적으로 Active Directory 배포가 필요 합니다. 응용 프로그램 계층에 대 한 연결도 필요 합니다.

### <a name="step-1-set-up-active-directory"></a>1 단계: Active Directory 설정

SQL Server를 제대로 실행 하려면 보조 복구 사이트에서 Active Directory를 설정 합니다.

* **Small enterprise**: 적은 수의 응용 프로그램과 온-프레미스 사이트에 대 한 단일 도메인 컨트롤러를 보유 하 고 있습니다. 전체 사이트를 장애 조치 (failover) 하려는 경우 Site Recovery 복제를 사용 합니다. 이 서비스는 도메인 컨트롤러를 보조 데이터 센터 또는 Azure에 복제 합니다.
* **Medium-대기업**: 추가 도메인 컨트롤러를 설정 해야 할 수 있습니다.
  - 응용 프로그램 수가 많은 경우 Active Directory 포리스트가 있고 응용 프로그램 또는 워크 로드에 의해 장애 조치 (failover) 하려는 경우 보조 데이터 센터 또는 Azure에서 다른 도메인 컨트롤러를 설정 합니다.
  -  Always On 가용성 그룹를 사용 하 여 원격 사이트로 복구 하는 경우 보조 사이트 또는 Azure에서 다른 도메인 컨트롤러를 설정 합니다. 이 도메인 컨트롤러는 복구 된 SQL Server 인스턴스에 사용 됩니다.

이 문서의 지침에서는 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정 합니다. 자세히 알아보려면 [Site Recovery를 사용 하 여 Active Directory를 보호 하](site-recovery-active-directory.md)는 절차를 참조 하세요.

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>2 단계: 다른 계층과의 연결 확인

데이터베이스 계층이 대상 Azure 지역에서 실행 된 후에는 응용 프로그램 및 웹 계층과 연결 되어 있는지 확인 합니다. 테스트 장애 조치 (failover)와의 연결 유효성을 검사 하기 위해 필요한 단계를 미리 수행 합니다.

연결 고려 사항을 위해 응용 프로그램을 디자인 하는 방법을 이해 하려면 다음 예제를 참조 하세요.

* [클라우드 재해 복구를 위한 응용 프로그램 디자인](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [탄력적 풀 재해 복구 전략](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>3 단계: Always On, 활성 지역 복제 및 자동 장애 조치 (failover) 그룹과 상호 운용

BCDR 기술 Always On, 활성 지역 복제 및 자동 장애 조치 (failover) 그룹에는 대상 Azure 지역에서 실행 되는 SQL Server의 보조 복제본이 있습니다. 응용 프로그램 장애 조치 (failover)의 첫 번째 단계는이 복제본을 기본 복제본으로 지정 하는 것입니다. 이 단계에서는 보조 데이터베이스에 도메인 컨트롤러가 이미 있다고 가정 합니다. 자동 장애 조치 (failover)를 수행 하도록 선택 하는 경우에는이 단계가 필요 하지 않을 수 있습니다. 데이터베이스 장애 조치 (failover)가 완료 된 후에만 웹 및 응용 프로그램 계층을 장애 조치 합니다.

> [!NOTE]
> Site Recovery를 사용 하 여 SQL 컴퓨터를 보호 하는 데 도움이 되 면 해당 컴퓨터의 복구 그룹만 만들고 복구 계획에 장애 조치를 추가 해야 합니다.

응용 프로그램 및 웹 계층 가상 컴퓨터를 사용 하 여 [복구 계획을 만듭니다](site-recovery-create-recovery-plans.md) . 다음 단계에서는 데이터베이스 계층의 장애 조치 (failover)를 추가 하는 방법을 보여 줍니다.

1. [리소스 관리자 가상 머신과](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) [클래식 가상 머신에서](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)SQL 가용성 그룹 장애 조치 (failover)를 수행 하는 스크립트를 가져옵니다. 스크립트를 Azure Automation 계정으로 가져옵니다.

    [!["Azure에 배포" 로고의 이미지](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 복구 계획의 첫 번째 그룹에 대 한 사전 작업으로 ASR-SQL-FailoverAG 스크립트를 추가 합니다.

1. 스크립트에서 사용할 수 있는 지침에 따라 자동화 변수를 만듭니다. 이 변수는 가용성 그룹의 이름을 제공 합니다.

### <a name="step-4-conduct-a-test-failover"></a>4 단계: 테스트 장애 조치 (failover) 수행

SQL Always On와 같은 일부 BCDR 기술은 기본적으로 테스트 장애 조치 (failover)를 지원 하지 않습니다. *이러한 기술을 사용 하는 경우에만 다음 방법을 사용 하*는 것이 좋습니다.

1. Azure에서 가용성 그룹 복제본을 호스트 하는 VM에 [Azure Backup](../backup/backup-azure-arm-vms.md) 을 설정 합니다.

1. 복구 계획의 테스트 장애 조치 (failover)를 트리거하기 전에 이전 단계에서 수행한 백업에서 VM을 복구 합니다.

    ![Azure Backup에서 구성을 복원 하는 창을 보여 주는 스크린샷](./media/site-recovery-sql/restore-from-backup.png)

1. 백업에서 복원 된 VM에서 [쿼럼을 강제 적용](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) 합니다.

1. 수신기의 IP 주소를 테스트 장애 조치 (failover) 네트워크에서 사용할 수 있는 주소로 업데이트 합니다.

    ![규칙 창 및 IP 주소 속성 대화 상자의 스크린샷](./media/site-recovery-sql/update-listener-ip.png)

1. 수신기를 온라인 상태로 전환합니다.

    ![서버 이름 및 상태를 표시 하 Content_AG 레이블이 지정 된 창의 스크린샷](./media/site-recovery-sql/bring-listener-online.png)

1. 장애 조치 (failover) 네트워크의 부하 분산 장치에 각 가용성 그룹 수신기에 해당 하는 프런트 엔드 IP 주소 풀에서 그리고 백 엔드 풀의 SQL Server VM 하나의 IP 주소가 있는지 확인 합니다.

     !["SQL-AlwaysOn-LB-프런트 엔드 IP 풀" 이라는 창의 스크린샷](./media/site-recovery-sql/create-load-balancer1.png)

    !["SQL-AlwaysOn--백 엔드 IP 풀" 이라는 창의 스크린샷](./media/site-recovery-sql/create-load-balancer2.png)

1. 이후 복구 그룹에서 응용 프로그램 계층의 장애 조치 (failover)를 추가 하 고이 복구 계획에 대 한 웹 계층을 추가 합니다.

1. 복구 계획의 테스트 장애 조치 (failover)를 수행 하 여 응용 프로그램의 종단 간 장애 조치 (failover)를 테스트 합니다.

## <a name="steps-to-do-a-failover"></a>장애 조치(failover)를 수행하는 단계

3 단계에서 스크립트를 추가 하 고 4 단계에서 해당 스크립트의 유효성을 검사 한 후 3 단계에서 만든 복구 계획의 장애 조치 (failover)를 수행할 수 있습니다.

응용 프로그램 및 웹 계층에 대 한 장애 조치 단계는 테스트 장애 조치 (failover) 및 장애 조치 (failover) 복구 계획에서 동일 해야 합니다.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>SQL Server 클러스터를 보호 하는 방법

SQL Server Standard edition 또는 SQL Server 2008 r 2를 실행 하는 클러스터의 경우 Site Recovery 복제를 사용 하 여 SQL Server를 보호 하는 것이 좋습니다.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure와 온-프레미스에서 azure로

Azure 지역으로 복제 하는 경우 Site Recovery는 게스트 클러스터 지원을 제공 하지 않습니다. 또한 SQL Server Standard edition은 저렴 한 재해 복구 솔루션을 제공 하지 않습니다. 이 시나리오에서는 SQL Server 클러스터를 기본 위치의 독립 실행형 SQL Server 인스턴스로 보호 하 고 보조에서 복구 하는 것이 좋습니다.

1. 기본 Azure 지역 또는 온-프레미스 사이트에 독립 실행형 SQL Server 인스턴스를 추가로 구성 합니다.

1. 보호 하려는 데이터베이스에 대 한 미러 역할을 하도록 인스턴스를 구성 합니다. 보호 우선 모드에서 미러링을 구성 합니다.

1. [Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-v](site-recovery-hyper-v-site-to-azure.md)또는 [VMware vm 및 물리적 서버](site-recovery-vmware-to-azure-classic.md)에 대 한 기본 사이트에서 Site Recovery를 구성 합니다.

1. Site Recovery 복제를 사용 하 여 새 SQL Server 인스턴스를 보조 사이트로 복제 합니다. 안전 하지 않은 미러 복사는 주 클러스터와 동기화 되지만 Site Recovery 복제를 사용 하 여 복제 됩니다.

   ![기본 사이트, Site Recovery 및 Azure 간의 관계와 흐름을 보여 주는 표준 클러스터 이미지](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>장애 복구 고려 사항

SQL Server Standard 클러스터의 경우 계획 되지 않은 장애 조치 (failover) 후 장애 복구 (failback)에 SQL Server 백업 및 복원이 필요 미러 서버는 미러 서버를 다시 설정 하 여 미러 인스턴스에서 원래 클러스터로 작업을 수행 합니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Site Recovery와 함께 사용 하는 경우 SQL Server 어떻게 사용이 허가 됩니까?

SQL Server에 대 한 Site Recovery 복제는 소프트웨어 보증 재해 복구 혜택에서 다룹니다. 이 범위는 온-프레미스에서 Azure로의 재해 복구 및 지역 간 Azure IaaS 재해 복구와 같은 모든 Site Recovery 시나리오에 적용 됩니다. 자세한 내용은 [Azure Site Recovery 가격 책정](https://azure.microsoft.com/pricing/details/site-recovery/) 을 참조 하세요.

### <a name="will-site-recovery-support-my-sql-server-version"></a>SQL Server 버전을 지원할 Site Recovery 있나요?

Site Recovery는 응용 프로그램에 독립적입니다. Site Recovery은 지원 되는 운영 체제에 배포 된 모든 버전의 SQL Server를 보호 하는 데 도움이 됩니다. 자세한 내용은 복제 된 컴퓨터 [복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md#replicated-machines) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Site Recovery 아키텍처](site-recovery-components.md)에 대해 자세히 알아보세요.
* Azure의 SQL Server 보조 Azure 지역에서 복구를 위한 [고가용성 솔루션](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) 에 대해 자세히 알아보세요.
* SQL Database는 보조 Azure 지역에서 복구 하기 위한 [비즈니스 연속성](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) 및 [고가용성](../azure-sql/database/high-availability-sla.md) 옵션에 대해 자세히 알아보세요.
* 온-프레미스에 있는 SQL Server 컴퓨터의 경우 Azure Virtual Machines에서 복구에 대 한 [고가용성 옵션](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) 에 대해 자세히 알아보세요.
