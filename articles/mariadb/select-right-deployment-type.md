---
title: 올바른 배포 유형 선택-Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB을 IaaS (infrastructure as a service) 또는 PaaS (platform as a service)로 배포 하기 전에 고려해 야 하는 요인을 설명 합니다.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4c7eb5e4f22cb432a9d17e6eafa653e62e1f9129
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529903"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Azure에서 적절 한 MariaDB 서버 옵션 선택

Azure를 사용 하는 경우에는 호스트 된 가상 머신 IaaS (infrastructure as a service) 또는 호스트 된 PaaS (platform as a service)로 서의 Aadb 서버 워크 로드를 실행할 수 있습니다. PaaS는 여러 배포 옵션을 포함 하며 각 배포 옵션 내에 서비스 계층이 있습니다. IaaS와 PaaS 중에서 선택 하는 경우 데이터베이스를 관리 하 고, 패치를 적용 하 고, 백업을 만들 지, 아니면 이러한 작업을 Azure로 위임할 것인지를 결정 해야 합니다.

결정을 내릴 때는 다음 두 가지 옵션을 고려해 야 합니다.

- **Azure Database for MariaDB:** 이 옵션은 안정적인 버전의 MariaDB community edition을 기반으로 하는 완전히 관리 되는 Fadb 데이터베이스 엔진입니다. Azure 클라우드 플랫폼에서 호스트 되는이 관계형 Database as a Service (DBaaS)는 PaaS의 산업 범주에 해당 합니다.

  Azure의 관리 되는 인스턴스를 사용 하 여, MariaDB 서버가 온-프레미스 또는 Azure VM 일 때 포괄적인 구성이 필요한 기본 제공 기능을 사용할 수 있습니다.

  MariaDB를 서비스로 사용 하는 경우 중단 없이 제어를 강화 하거나 규모를 확장 하는 옵션을 사용 하 여 비용을 지불 합니다. 독립형 Aadb 서버와 달리 Azure Database for MariaDB에는 기본 제공 고가용성, 인텔리전스 및 관리와 같은 추가 기능이 있습니다.

- **Azure vm의 Mariadb:** 이 옵션은 IaaS의 업계 범주에 해당 합니다. 이 서비스를 사용 하면 Azure 클라우드 플랫폼에서 완전히 관리 되는 가상 머신 내에서 MariaDB 서버를 실행할 수 있습니다. 모든 최신 버전 및 버전의 모든 버전을 IaaS 가상 컴퓨터에 설치할 수 있습니다.

  Azure Database for MariaDB와의 가장 큰 차이점으로, Azure Vm의 Aadb는 데이터베이스 엔진에 대 한 제어를 제공 합니다. 그러나이 컨트롤은 Vm 및 많은 DBA (데이터베이스 관리) 태스크를 관리 하는 데 드는 책임을 집니다. 이러한 작업에는 데이터베이스 서버 유지 관리 및 패치, 데이터베이스 복구 및 고가용성 디자인이 포함 됩니다.

이러한 옵션들의 주요 차이점은 다음 표에 나와 있습니다.

|            | Azure Database for MariaDB | Azure Vm의 MariaDB    |
|:-------------------|:-----------------------------|:--------------------|
| SLA (서비스 수준 계약)                | 99.99% 가용성의 SLA를 제공 합니다.| 동일한 가용성 집합에서 두 개 이상의 인스턴스를 사용 하 여 최대 99.95%의 가용성을 제공 합니다.<br/><br/>premium storage를 사용 하는 단일 인스턴스 VM의 99.9% 가용성.<br/><br/>99.99% 여러 가용성 집합에 여러 인스턴스가 있는 가용성 영역를 사용 합니다.<br/><br/>[VIRTUAL MACHINES SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)를 참조 하세요. |
| 운영 체제 패치        | 자동  | 고객이 관리 |
| MariaDB 패치     | 자동  | 고객이 관리 |
| 고가용성 | HA (고가용성) 모델은 노드 수준의 중단이 발생 한 경우에 대 한 기본 제공 장애 조치 (failover) 메커니즘을 기반으로 합니다. 이 경우 서비스는 새 인스턴스를 자동으로 만들고이 인스턴스에 저장소를 연결 합니다. | 고객은 고가용성을 설계, 구현, 테스트 및 유지 관리 합니다. 기능에는 always on 장애 조치 (failover) 클러스터링, always on 그룹 복제, 로그 전달 또는 트랜잭션 복제가 포함 될 수 있습니다.|
| 영역 중복 | 현재 지원되지 않음 | Azure Vm은 서로 다른 가용성 영역에서 실행 되도록 설정할 수 있습니다. 온-프레미스 솔루션의 경우 고객은 자체 보조 데이터 센터를 만들고 관리 하 고 유지 관리 해야 합니다.|
| 하이브리드 시나리오 | [입력 데이터 복제](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)를 사용 하 여 외부의 데이터를 Azure Database for MariaDB 서비스에 동기화 할 수 있습니다. 외부 서버는 온-프레미스 가상 머신 또는 다른 클라우드 공급 기업이 호스트하는 데이터베이스 서비스에 있을 수 있습니다.<br/><br/> [복제본 읽기](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) 기능을 사용 하면 Azure Database for MariaDB 마스터 서버에서 최대 5 개의 읽기 전용 복제 서버로 데이터를 복제할 수 있습니다. 복제본은 동일한 Azure 지역 내에 있거나 여러 지역에 있습니다. 읽기 전용 복제본은 binlog 복제 기술을 사용 하 여 비동기적으로 업데이트 됩니다.<br/><br/>영역 간 읽기 복제는 현재 공개 미리 보기로 제공 됩니다.| 고객이 관리
| 백업 및 복원 | 에서 자동으로 [서버 백업을](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) 만들고 로컬 중복 또는 지역 중복 인 사용자 구성 저장소에 저장 합니다. 이 서비스는 전체, 차등 및 트랜잭션 로그 백업을 수행 합니다. | 고객이 관리 |
| 데이터베이스 작업 모니터링 | 는 데이터베이스 작업에 대해 [경고를 설정](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) 하 고 임계값에 도달 하는 데 사용할 수 있는 기능을 고객에 게 제공 합니다. | 고객이 관리 |
| 고급 위협 보호 | [고급 위협 방지 기능](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal)을 제공 합니다. 이 보호는 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 잠재적으로 유해한 시도를 감지 합니다.<br/><br/>Advanced Threat Protection은 현재 공개 미리 보기로 제공 됩니다.| 고객은 스스로이 보호를 구축 해야 합니다.
| 재해 복구 | 자동 백업을 사용자가 구성한 [로컬 중복 또는 지역 중복 저장소](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)에 저장 합니다. 또한 백업은 서버를 특정 시점으로 복원할 수 있습니다. 보존 기간은 7 일에서 35 일 사이입니다. 복원은 Azure Portal를 사용 하 여 수행 됩니다. | 고객이 완전히 관리 합니다. 책임은 예약, 테스트, 보관, 저장소 및 보존을 포함 하지만이에 국한 되지 않습니다. 추가 옵션은 Azure Recovery Services 자격 증명 모음을 사용 하 여 Vm에서 Azure Vm 및 데이터베이스를 백업 하는 것입니다. 이 옵션은 미리 보기 상태입니다. |
| 성능 권장 사항 | 시스템에서 생성 된 사용 현황 로그 파일을 기반으로 고객에 게 [성능 권장 사항을](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) 제공 합니다. 권장 사항은 워크 로드를 최적화 하는 데 도움이 됩니다.<br/><br/>성능 권장 사항은 현재 공개 미리 보기로 제공 됩니다. | 고객이 관리 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS 또는 IaaS 선택을 위한 비즈니스 동기

사용자의 의사 결정에 영향을 줄 수 있는 몇 가지 요인이 있습니다.

### <a name="cost"></a>비용

제한 된 자금은 종종 데이터베이스 호스팅을 위한 최상의 솔루션을 결정 하는 주요 고려 사항입니다. 이는 비용 제한으로 운영 하는 설정 된 회사에서 현금 또는 팀이 거의 시작 하지 않는 경우에 해당 합니다. 이 섹션에서는 azure Vm의 Azure Database for MariaDB 및 MariaDB에 적용 되는 Azure의 청구 및 라이선스 기본 사항에 대해 설명 합니다.

#### <a name="billing"></a>결제

Azure Database for MariaDB는 현재 리소스에 대해 다른 가격을 사용 하는 여러 계층에서 서비스로 사용할 수 있습니다. 모든 리소스는 고정 요금으로 시간당 청구 됩니다. 현재 지원 되는 서비스 계층, 계산 크기 및 저장소 크기에 대 한 최신 정보는 [Vcore 기반 구매 모델](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers)을 참조 하세요. 응용 프로그램의 다양 한 처리량 요구에 맞게 서비스 계층 및 계산 크기를 동적으로 조정할 수 있습니다. 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 나가는 인터넷 트래픽에 대 한 요금이 청구 됩니다.

Azure Database for MariaDB를 사용 하면 Microsoft에서 자동으로 데이터베이스 소프트웨어를 구성, 패치 및 업그레이드 합니다. 이러한 자동화 된 작업을 통해 관리 비용이 줄어듭니다. 또한 Azure Database for MariaDB에는 [기본 제공 백업](https://docs.microsoft.com/azure/MariaDB/concepts-backup) 기능이 있습니다. 이러한 기능을 통해 특히 데이터베이스 수가 많은 경우 비용을 크게 절약할 수 있습니다. 반면, Azure Vm의 경우에는 Azure Vm에서 모든 Aadb 버전을 선택 하 여 실행할 수 있습니다. 어떤 종 량 제 b 버전에 있든 상관 없이 프로 비전 된 VM에 대해 비용을 지불 하 고 사용 되는 특정 Aadb 라이선스 유형에 대 한 비용을 지불 합니다.

Azure Database for MariaDB은 서비스에 대 한 99.99% SLA 보장을 유지 하면서 모든 종류의 노드 수준 중단에 대 한 고가용성을 제공 합니다. 그러나 Vm 내에서 데이터베이스 고가용성을 위해 고객은 MariaDB 데이터베이스에서 사용할 수 있는 [mariadb 복제](https://mariadb.com/kb/en/library/setting-up-replication/) 와 같은 고가용성 옵션을 사용 해야 합니다. 지원 되는 고가용성 옵션을 사용 하면 추가 SLA가 제공 되지 않습니다. 그러나 추가 비용 및 관리 오버 헤드로 99.99%의 데이터베이스 가용성을 달성할 수 있습니다.

가격 책정에 대 한 자세한 내용은 다음 문서를 참조 하세요.
* [Azure Database for MariaDB 가격 책정](https://azure.microsoft.com/pricing/details/MariaDB/)
* [가상 컴퓨터 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 요금 계산기](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>관리

대부분의 비즈니스에서 클라우드 서비스로 전환 하는 것은 비용 측면에서 관리의 복잡성을 오프 로드 하는 것에 대 한 것입니다. IaaS 및 PaaS를 사용 하 여 Microsoft:

- 기본 인프라를 관리 합니다.
- 에서는 모든 데이터를 자동으로 복제 하 여 재해 복구를 제공 합니다.
- 데이터베이스 소프트웨어를 구성 하 고 업그레이드 합니다.
- 부하 분산을 관리 합니다.
- 서버 오류가 있는 경우 투명 한 장애 조치 (failover)를 수행 합니다.

다음 목록에서는 각 옵션에 대 한 관리 고려 사항을 설명 합니다.

* Azure Database for MariaDB를 사용 하 여 계속 해 서 데이터베이스를 관리할 수 있습니다. 그러나 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 관리를 계속할 수 있는 항목의 예는 다음과 같습니다.

  - 데이터베이스
  - 로그인
  - 인덱스 튜닝
  - 쿼리 튜닝
  - 감사
  - 보안

  또한 다른 데이터 센터에 대 한 고가용성을 구성 하려면 구성 또는 관리가 최소화 되어야 합니다.

* Azure Vm에서 MariaDB를 사용 하면 운영 체제 및 MariaDB 서버 인스턴스 구성을 완전히 제어할 수 있습니다. VM을 사용 하 여 운영 체제 및 데이터베이스 소프트웨어를 업데이트 하거나 업그레이드할 시기를 결정 합니다. 또한 바이러스 백신 응용 프로그램과 같은 추가 소프트웨어를 설치할 시기를 결정 합니다. 패치, 백업 및 고가용성을 크게 간소화 하기 위해 몇 가지 자동화 된 기능이 제공 됩니다. VM 크기, 디스크 수 및 저장소 구성의 크기를 제어할 수 있습니다. 자세한 내용은 [Azure의 가상 컴퓨터 및 클라우드 서비스 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)를 참조 하세요.

### <a name="time-to-move-to-azure"></a>Azure로 이동해야 하는 경우

* Azure Database for MariaDB는 개발자 생산성과 새 솔루션에 대 한 빠른 출시 시간이 중요 한 경우 클라우드로 설계 된 응용 프로그램에 적합 한 솔루션입니다. DBA와 같은 프로그래밍 기능을 사용 하 여 서비스는 기본 운영 체제 및 데이터베이스를 관리할 필요가 없기 때문에 클라우드 설계자 및 개발자에 게 적합 합니다.

* 새 온-프레미스 하드웨어를 확보 하는 시간과 비용을 방지 하려는 경우 Azure Vm의 Aadb는 MariaDB 데이터베이스를 필요로 하는 응용 프로그램에 적합 한 솔루션 이거나 Windows 또는 Linux의 경우 MariaDB 기능에 액세스 하는 것입니다. 또한이 솔루션은 기존 온-프레미스 응용 프로그램 및 데이터베이스를 Azure에 그대로 마이그레이션하는 데 적합 합니다. Azure Database for MariaDB이 잘못 된 경우에 해당 합니다.

  프레젠테이션, 응용 프로그램 및 데이터 계층을 변경할 필요가 없기 때문에 기존 솔루션을 다시 설계 하는 데 시간과 예산을 절감할 수 있습니다. 대신 모든 솔루션을 Azure로 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 몇 가지 성능 최적화를 해결 하는 데 집중할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for MariaDB 가격 책정](https://azure.microsoft.com/pricing/details/MariaDB/)을 참조 하세요.
* [첫 번째 서버를 만들어](https://docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal)시작 하세요.
