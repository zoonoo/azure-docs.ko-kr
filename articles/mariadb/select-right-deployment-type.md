---
title: Azure Database for MariaDB에 적합 한 배포 유형 선택
description: 이 문서에서는 Azure Database for MariaDB에 대 한 IaaS (Infrastructure as a service) 또는 PaaS (Platform as a service)로 이동 하기 전에 수행 해야 하는 고려 사항을 설명 합니다.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817437"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Azure에서 적절 한 MariaDB 서버 옵션 선택

Azure를 사용 하는 경우에는 Azure를 사용 하 여 호스트 된 인프라 VM (IaaS) 또는 호스 티 드 서비스 (PaaS)로 서의 Fadb 서버 작업을 실행할 수 있습니다. PaaS는 여러 배포 옵션을 포함 하며 각 배포 옵션 내에 서비스 계층이 있습니다. PaaS 또는 IaaS를 결정할 때 데이터베이스를 관리 하 고, 패치를 적용 하 고, 백업을 수행할지, 아니면 이러한 작업을 Azure로 위임할 지를 결정 해야 합니다.</br>

해당 질문에 대 한 답변에 따라 다음 옵션을 고려 합니다. <br/>

**Azure Database for MariaDB** 은 안정적인 버전의 community edition을 기반으로 하는 완전히 관리 되는 Aadb 데이터베이스 엔진입니다. Azure 클라우드에서 호스트 되는이 관계형 DBaaS (database as a service)는 PaaS (Platform as a Service)의 업계 범주에 해당 합니다. Azure의 관리 되는 인스턴스를 사용 하는 경우에는 기본 제공 기능을 사용할 수 있으며,이 기능을 사용 하 여 기본 제공 되는 기능을 사용할 수 있습니다. Aadb를 서비스로 사용 하는 경우 중단 없이 기능을 강화 하거나 확장 하는 옵션을 사용 하 여 종 량 제입니다. 또한 독립 실행형 Aadb 서버와 달리 Azure Database for MariaDB에는 기본 제공 고가용성, 인텔리전스 및 관리와 같은 추가 기능이 있습니다. <br/><br/>
**AZURE VM의 Mariadb** 는 업계 범주 IaaS (Infrastructure As a Service)에 포함 되며, azure 클라우드의 완전히 관리 되는 가상 머신 내에서 aadb 서버를 실행할 수 있습니다. 모든 최신 버전 및 버전의 모든 버전을 IaaS 가상 컴퓨터에 설치할 수 있습니다. Azure Database for MariaDB와의 가장 큰 차이점은 Azure Vm의 MariaDB에서 데이터베이스 엔진에 대 한 제어를 허용 한다는 것입니다. 그러나이 컨트롤은 가상 컴퓨터를 관리 하 고 데이터베이스 서버 유지 관리/패치, 복구 및 고가용성 디자인 등의 여러 DBA 작업을 관리 하는 데 필요한 추가 책임의 비용으로 제공 됩니다.

이러한 옵션들의 주요 차이점은 다음 표에 나와 있습니다.

|            | **Azure Database for MariaDB** | **Azure Vm의 MariaDB**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | 99.99% 가용성의 SLA를 제공 합니다.| 동일한 가용성 집합에서 두 개 이상의 인스턴스를 사용 하 여 최대 99.95%의 가용성을 제공 합니다. <br/>Premium storage를 사용 하는 99.9% 단일 인스턴스 VM <br/> 2 개 이상의 가용성 집합에 2 개 이상의 인스턴스가 있는 가용성 영역을 사용 하는 99.99%<br/> 참고- [가상 컴퓨터 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **OS 패치**        | 자동  | 고객이 관리 |
|**MariaDB 패치**     | 자동  | 고객이 관리 |
| **고가용성** | HA(고가용성) 모델은 노드 수준의 중단이 발생할 때 기본 제공되는 장애 조치(failover) 메커니즘을 기반으로 합니다. 이 경우 서비스는 자동으로 인스턴스를 만들고이 새 인스턴스에 저장소를 연결 합니다. | 고객은 고가용성을 설계, 구현, 테스트 및 유지 관리 합니다. 여기에는 사용 중인 버전에 따라 Always On (장애 조치 (Failover) 클러스터링 또는 그룹 복제), 로그 전달 및 트랜잭션 복제가 포함 될 수 있습니다.|
| **영역 중복성** | 현재 지원 되지 않습니다. | Azure Vm은 서로 다른 가용성 영역에서 실행 되도록 설정할 수 있습니다. 온-프레미스 솔루션의 경우 고객은 자체 보조 데이터 센터를 만들고 관리 하 고 유지 관리 해야 합니다.|
| **하이브리드 시나리오** | [입력 데이터 복제](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication) 를 사용 하 여 외부의 데이터를 Azure Database for MariaDB 서비스에 동기화 할 수 있습니다. 외부 서버는 온-프레미스 가상 머신 또는 다른 클라우드 공급 기업이 호스트하는 데이터베이스 서비스에 있을 수 있습니다.  <br/> <br/> [복제본 읽기](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) 기능을 사용 하면 동일한 Azure 지역 내에서 또는 여러 지역에 있는 Azure Database for MariaDB 서버 (마스터)에서 최대 5 개의 읽기 전용 서버 (복제본)로 데이터를 복제할 수 있습니다. 읽기 전용 복제본은 binlog 복제 기술을 사용 하 여 비동기적으로 업데이트 됩니다.   <br/> <br/> 참고-지역 간 읽기 복제는 현재 공개 미리 보기로 제공 됩니다.| 고객이 관리 <br/>
| **Backup 및 복원** | 에서 자동으로 [서버 백업을](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) 만들어 사용자가 구성한 로컬 중복 또는 지역 중복 저장소에 저장 합니다. 이 서비스는 전체, 차등 및 트랜잭션 로그 백업을 수행 합니다. | 고객이 관리 |
| **데이터베이스 작업 모니터링** | 고객이 데이터베이스 작업에 대해 [경고를 설정](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) 하 고 임계값에 도달 하는 데 사용할 수 있습니다. | 고객이 관리 |
| **고급 스레드 보호** | 비정상적인 활동을 감지 하는 [고급 위협 방지](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal) 를 제공 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다. <br/> <br/> 참고-고급 스레드 보호는 현재 공개 미리 보기로 제공 됩니다.| 고객은 스스로를 위해 빌드해야 합니다.
| **백업 (재해 복구)** | 는 [로컬 중복 또는 지역 중복 저장소](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)에서 구성 된 사용자에 게 자동화 된 백업을 저장 합니다. 백업을 사용 하 여 특정 시점으로 서버를 복원할 수도 있습니다. 보존 기간은 7-35 일에서 설정할 수 있습니다. Azure Portal를 사용 하 여 복원을 수행할 수 있습니다. <br/> | 예약, 테스트, 보관, 저장 및 보존을 포함 하 되, 포함 하 되이에 제한 되지 않는 고객에 의해 완전히 관리 됩니다. 추가 옵션은 Azure Recovery Services 자격 증명 모음을 사용 하 여 Vm (미리 보기)에서 Azure Vm 및 데이터베이스를 백업 하는 것입니다. |
| **성능 권장 사항** | 작업을 최적화 하는 데 도움이 되는 사용량 원격 분석을 기반으로 고객에 게 사전 [성능 권장 사항을](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) 제공 합니다. <br/> <br/> 참고-성능 권장 사항은 현재 공개 미리 보기로 제공 됩니다. | 고객이 관리 |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS 또는 IaaS 선택을 위한 비즈니스 동기

사용자의 결정에 영향을 줄 수 있는 몇 가지 요인이 있습니다.

### <a name="cost"></a>비용

현금에 대 한 시작 strapped, 높은 예산 제약 조건에서 작동 하는 설정 된 회사의 팀이 든 상관 없이, 데이터베이스 호스팅을 위한 최상의 솔루션을 결정할 때 제한 된 자금을 주로 고려해 야 합니다. 이 섹션에서는 azure Vm의 Azure Database for MariaDB 및 MariaDB와 관련 하 여 Azure의 청구 및 라이선스 기본 사항을 설명 합니다.

#### <a name="billing"></a>대금 청구

현재 Azure Database for MariaDB는 리소스에 대 한 가격이 다른 여러 계층에서 서비스로 사용할 수 있으며, 모든 비용은 고정 요금으로 시간당 청구 됩니다. 현재 지원 되는 서비스 계층, 계산 크기 및 저장소 양에 대 한 최신 정보는 [Vcore 기반 구매 모델](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers)을 참조 하세요. 애플리케이션의 다양한 처리량 요구에 맞게 서비스 계층과 컴퓨팅 크기를 동적으로 조정할 수 있습니다. 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 나가는 인터넷 트래픽에 대 한 요금이 청구 됩니다.

Azure Database for MariaDB를 사용 하면 Microsoft에서 자동으로 데이터베이스 소프트웨어를 구성, 패치 및 업그레이드 하므로 관리 비용이 줄어듭니다. 또한 [기본 제공 백업](https://docs.microsoft.com/azure/MariaDB/concepts-backup) 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다. Azure Vm에서의 Aadb를 사용 하 여 모든 MariaDB 버전을 선택 하 고 실행할 수 있습니다. 사용 하는 종 량 제 b 버전에 관계 없이 프로 비전 된 VM에 대해 비용을 지불 하 고, MariaDB에 사용 되는 특정 라이선스 유형에 대 한 비용을 지불 합니다.

Azure Database for MariaDB은 모든 종류의 노드 수준 중단에 대 한 고가용성을 제공 하 고 서비스에 대 한 99.99% SLA를 유지 합니다. 그러나 Vm 내 데이터베이스 HA (고가용성)의 경우 고객은 mariadb [복제](https://mariadb.com/kb/en/library/setting-up-replication/)와 같은 mariadb 데이터베이스에서 사용할 수 있는 고가용성 옵션을 수행 해야 합니다. 지원 되는 고가용성 옵션을 사용 하면 추가 SLA가 제공 되지 않지만, 추가 비용 및 관리 오버 헤드로 인해 99.99%의 데이터베이스 가용성 > 달성할 수 있습니다.

가격에 대한 자세한 내용은 다음 리소스를 참조하세요.
* [Azure Database for MariaDB 가격 책정](https://azure.microsoft.com/pricing/details/MariaDB/)
* [가상 컴퓨터 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>관리

대부분의 비즈니스에서 클라우드 서비스로 전환 하는 것은 관리의 복잡성을 오프 로딩 하는 것에 대 한 것입니다. IaaS 및 PaaS를 사용 하 여 Microsoft는 기본 인프라를 관리 하 고 모든 데이터를 자동으로 복제 하 여 재해 복구를 제공 하 고, 데이터베이스 소프트웨어를 구성 및 업그레이드 하 고, 부하 분산을 관리 하 고, 서버 오류입니다.

* **Azure Database for MariaDB를 사용**하 여 계속 해 서 데이터베이스를 관리할 수 있지만 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 계속 관리할 수 있는 항목의 예는 데이터베이스 및 로그인, 인덱스 및 쿼리 튜닝 및 감사 및 보안을 들 수 있습니다. 또한 다른 데이터 센터에 대 한 고가용성을 구성 하려면 구성 또는 관리를 최소화 해야 합니다.<br/><br/>
* **Azure vm에서 MariaDB를 사용**하 여 운영 체제 및 mariadb 서버 인스턴스 구성을 완전히 제어할 수 있습니다. VM을 사용 하 여 운영 체제 및 데이터베이스 소프트웨어를 업데이트/업그레이드할 시기 및 바이러스 백신 응용 프로그램과 같은 추가 소프트웨어를 설치할 시기를 결정 합니다. 자동화된 일부 기능이 제공되어 패치, 백업 및 고가용성을 크게 간소화합니다. 또한 VM 크기, 디스크 수 및 해당 스토리지 구성도 사용자가 제어할 수 있습니다. 자세한 내용은 Azure의 가상 컴퓨터 및 클라우드 서비스 크기를 참조 하세요.

### <a name="time-to-move-to-azure-br"></a>Azure로 이동 하는 시간 <br/>
* **Azure Database for MariaDB** 는 개발자의 생산성과 새 솔루션에 대 한 빠른 출시 시간에 매우 중요 한 경우 클라우드로 설계 된 응용 프로그램에 적합 한 솔루션입니다. 프로그래밍 방식의 DBA와 유사한 기능을 사용 하 여 서비스는 기본 운영 체제 및 데이터베이스를 관리할 필요가 없기 때문에 클라우드 설계자 및 개발자에 게 적합 합니다.<br/><br/>
* **Azure vm의 Mariadb** 는 mariadb 데이터베이스가 필요한 기존 응용 프로그램 또는 새로운 응용 프로그램에 적합 하며, Windows/Linux에서 mariadb 데이터베이스의 기능에 액세스 하 고, 새로운 온-프레미스 하드웨어를 얻는 시간과 비용을 방지 하려고 합니다. 이 옵션은 기존 온-프레미스 응용 프로그램 및 데이터베이스를 있는 그대로 Azure로 마이그레이션하는 데에도 적합 합니다. Azure Database for MariaDB 인스턴스가 적합 하지 않은 경우에 적합 합니다. 프레젠테이션, 응용 프로그램 및 데이터 계층을 변경할 필요가 없기 때문에 기존 솔루션을 다시 설계 하는 데 시간과 예산을 절감할 수 있습니다. 대신 모든 솔루션을 Azure로 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 몇 가지 성능 최적화를 해결 하는 데 집중할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for MariaDB 가격 책정](https://azure.microsoft.com/pricing/details/MariaDB/) 참조
* [첫 번째 서버를 만들어서](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal) 시작합니다.

