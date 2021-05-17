---
title: 올바른 배포 유형 선택 - Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB을 IaaS(infrastructure as a Service) 또는 PaaS(Platform as a Service)로 배포하기 전에 고려해야 하는 요인에 대해 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d8885e374142b3d916803fc472ae18351ca6d470
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664521"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Azure에서 적절한 MariaDB 서버 옵션 선택

Azure를 사용하면 MariaDB 서버 워크로드를 호스트된 가상 머신 IaaS(Infrastructure as a Service) 또는 호스트된 PaaS(Platform as a Service)로 실행할 수 있습니다. PaaS에는 여러 배포 옵션이 있고 각 배포 옵션 내에는 여러 서비스 계층이 있습니다. IaaS와 PaaS 중에 선택할 때 직접 데이터베이스를 관리하고 패치를 적용하고 백업을 수행할 것인지 아니면 이러한 작업을 Azure에 위임할 것인지 결정해야 합니다.

결정할 때 다음 두 가지 옵션을 고려하세요.

- **Azure Database for MariaDB:** 이 옵션은 안정적인 버전의 MariaDB Community Edition을 기준으로 하는 완전 관리형 MariaDB 데이터베이스 엔진입니다. PaaS 산업 범주로 분류되며, Azure 클라우드 플랫폼에서 호스트되는 관계형 DBaaS(Database as a Service)입니다.

  Azure에서 관리되는 MariaDB 인스턴스를 사용하여, MariaDB 서버가 온-프레미스 또는 Azure VM에 있을 때 포괄적인 구성이 필요한 기본 제공 기능을 사용할 수 있습니다.

  MariaDB를 서비스로 사용하는 경우 중단 없이 제어를 강화하기 위해 스케일 업 또는 스케일 아웃하는 옵션이 종량제 방식으로 지원됩니다. 독립형 MariaDB 서버와 달리 Azure Database for MariaDB에는 기본 제공 고가용성, 인텔리전스 및 관리와 같은 추가 기능이 있습니다.

- **Azure VM의 MariaDB:** 이 옵션은 IaaS 산업 범주에 속합니다. 이 서비스를 선택하면 Azure 클라우드 플랫폼의 완전 관리형 가상 머신 내에서 MariaDB 서버를 실행할 수 있습니다. MariaDB의 모든 최신 버전과 에디션을 IaaS 가상 머신에 설치할 수 있습니다.

  Azure Database for MariaDB와의 가장 큰 차이점으로 Azure VM의 MariaDB는 데이터베이스 엔진을 제어할 수 있습니다. 그러나 데이터베이스 엔진을 제어할 수 있는 대신 VM 및 여러 DBA(데이터베이스 관리) 작업을 직접 관리해야 합니다. 이러한 작업에는 데이터베이스 서버 유지 관리 및 패치, 데이터베이스 복구, 고가용성 디자인이 포함됩니다.

이러한 옵션들의 주요 차이점은 다음 표에 나와 있습니다.

| attribute          | Azure Database for MariaDB | Azure VM의 MariaDB    |
|:-------------------|:-----------------------------|:--------------------|
| SLA(서비스 수준 계약)                | 99.99% 가용성의 SLA를 제공합니다.| 동일한 가용성 집합에서 두 개 이상의 인스턴스를 사용하여 최대 99.95%의 가용성을 제공합니다.<br/><br/>Premium Storage를 사용하여 단일 인스턴스 VM에서 99.9% 가용성을 제공합니다.<br/><br/>여러 가용성 집합에 여러 인스턴스가 있는 가용성 영역을 사용하여 99.99% 가용성을 제공합니다.<br/><br/>[가상 머신 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)를 참조하세요. |
| 운영 체제 패치        | 자동  | 고객이 관리 |
| MariaDB 패치     | 자동  | 고객이 관리 |
| 고가용성 | HA(고가용성) 모델은 노드 수준의 중단이 발생할 때 기본 제공되는 장애 조치(failover) 메커니즘을 기준으로 합니다. 이 경우 서비스는 새 인스턴스를 자동으로 만들고 이 인스턴스에 스토리지를 연결합니다. | 고객이 고가용성을 설계, 구현, 테스트 및 유지 관리합니다. 기능에는 상시 장애 조치 클러스터링, 상시 그룹 복제, 로그 전달 또는 트랜잭션 복제가 포함될 수 있습니다.|
| 영역 중복 | 현재 지원되지 않음 | Azure VM은 서로 다른 가용성 영역에서 실행되도록 설정할 수 있습니다. 온-프레미스 솔루션의 경우 고객이 자체적으로 보조 데이터 센터를 만들고 관리하고 유지 관리해야 합니다.|
| 하이브리드 시나리오 | [데이터 내부 복제](concepts-data-in-replication.md)를 사용하면 외부 MariaDB 서버에서 Azure Database for MariaDB 서비스로 데이터를 동기화할 수 있습니다. 외부 서버는 온-프레미스 가상 머신 또는 다른 클라우드 공급 기업이 호스트하는 데이터베이스 서비스에 있을 수 있습니다.<br/><br/> [복제본 읽기](concepts-read-replicas.md) 기능을 사용하면 Azure Database for MariaDB 원본 서버에서 최대 5개의 읽기 전용 복제 서버로 데이터를 복제할 수 있습니다. 복제본은 동일한 Azure 지역 내에 있거나 여러 지역에 걸쳐 있습니다. 읽기 전용 복제본은 binlog 복제 기술을 사용하여 비동기적으로 업데이트됩니다.<br/><br/>지역 간 읽기 복제는 현재 퍼블릭 미리 보기로 제공됩니다.| 고객이 관리
| 백업 및 복원 | 자동으로 [서버 백업](concepts-backup.md#backups)을 만들어 사용자가 로컬로 구성한 중복 스토리지 또는 지역 중복 스토리지에 저장합니다. 이 서비스는 전체, 차등 및 트랜잭션 로그 백업을 수행합니다. | 고객이 관리 |
| 데이터베이스 작업 모니터링 | 데이터베이스 작업에 대한 [경고를 설정](concepts-monitoring.md)하고 임계값에 도달하면 작동하는 기능을 고객에게 제공합니다. | 고객이 관리 |
| Advanced Threat Protection | [Advanced Threat Protection](howto-database-threat-protection-portal.md)을 제공합니다. 이 보호 기능은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 탐지합니다.<br/><br/>Advanced Threat Protection은 현재 퍼블릭 미리 보기로 제공됩니다.| 고객이 직접 이 보호를 구축해야 합니다.
| 재해 복구 | 자동 백업을 사용자가 구성한 [로컬 중복 또는 지역 중복 스토리지](howto-restore-server-portal.md)에 저장합니다. 또한 백업은 서버를 특정 시점으로 복원할 수 있습니다. 보존 기간은 7일에서 35일 사이입니다. 복원은 Azure Portal를 사용하여 수행됩니다. | 고객이 완전히 관리합니다. 책임은 예약, 테스트, 보관, 스토리지 및 보존을 포함하지만 이에 국한 되지 않습니다. 추가 옵션은 Azure Recovery Services 자격 증명 모음을 사용하여 VM에서 Azure VM 및 데이터베이스를 백업하는 것입니다. 이 옵션은 현재 미리 보기로 제공됩니다. |
| 성능 권장 사항 | 시스템에서 생성된 사용 현황 로그 파일을 기준으로 고객에게 [성능 권장 사항](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)을 제공합니다. 권장 사항은 워크로드를 최적화하는 데 도움이 됩니다.<br/><br/>성능 권장 사항은 현재 퍼블릭 미리 보기로 제공됩니다. | 고객이 관리 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS 또는 IaaS 선택을 위한 비즈니스 동기

PaaS 또는 IaaS 중에서 MariaDB 데이터베이스의 호스트를 결정할 때 영향을 줄 수 있는 여러 요인이 있습니다.

### <a name="cost"></a>Cost

데이터베이스 호스팅을 위한 최적의 솔루션을 결정할 때 제한된 자금이 결정적인 역할을 하는 경우가 종종 있습니다. 현금이 부족한 스타트업이거나 탄탄한 회사에 속해 있지만 예산이 넉넉하지 않은 팀인 경우 특히 그렇습니다. 이 섹션에서는 Azure Database for MariaDB 및 Azure VM의 MariaDB에 적용되는 Azure의 청구 및 라이선스 기본 사항에 대해 설명합니다.

#### <a name="billing"></a>결제

Azure Database for MariaDB는 현재 리소스 가격이 다른 여러 계층에서 서비스로 제공됩니다. 모든 리소스 요금은 고정 요율로 시간당 청구됩니다. 현재 지원되는 서비스 계층, 컴퓨팅 크기 및 스토리지 용량에 대한 최신 정보는 [vCore 기반 구매 모델](concepts-pricing-tiers.md)을 참조하세요. 애플리케이션의 다양한 처리량 요구에 맞게 서비스 계층과 컴퓨팅 크기를 동적으로 조정할 수 있습니다. 발신 인터넷 트래픽은 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)이 청구됩니다.

Azure Database for MariaDB를 선택하면 Microsoft에서 자동으로 데이터베이스 소프트웨어를 구성, 패치 및 업그레이드합니다. 이러한 작업이 자동화되어 있으므로 관리 비용이 줄어듭니다. 또한 Azure Database for MariaDB에는 [기본 제공 백업](concepts-backup.md) 기능이 있습니다. 이러한 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다. 반면, Azure VM의 MariaDB를 선택하면 MariaDB 버전을 선택하여 실행할 수 있습니다. 어떤 MariaDB 버전을 사용하든, 프로비저닝된 VM 비용과 사용된 특정 MariaDB 라이선스 유형에 대한 비용을 지불합니다.

Azure Database for MariaDB는 서비스의 99.99% SLA 보장을 유지하면서도 모든 종류의 노드 수준 중단에 대한 고가용성을 제공합니다. 그러나 VM 내에 있는 데이터베이스의 고가용성을 유지하려면 고객은 MariaDB 데이터베이스에서 사용할 수 있는 [MariaDB 복제](https://mariadb.com/kb/en/library/setting-up-replication/) 같은 고가용성 옵션을 사용하는 것이 좋습니다. 지원되는 고가용성 옵션을 사용하면 추가 SLA가 제공되지 않습니다. 하지만 추가 비용과 관리 오버헤드를 대가로 99.99%보다 높은 데이터베이스 가용성을 달성할 수 있습니다.

가격 책정에 대한 자세한 내용은 다음 문서를 참조하세요.
* [Azure Database for MariaDB 가격 책정](https://azure.microsoft.com/pricing/details/MariaDB/)
* [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>관리

많은 기업이 클라우드 서비스로 전환하기로 결정할 때 비용과 더불어 가장 중요하게 생각하는 것은 관리의 복잡성을 줄이는 것입니다. IaaS 및 PaaS를 사용하여 Microsoft는 다음을 수행합니다.

- 기본 인프라를 관리합니다.
- 자동으로 모든 데이터를 복제하여 재해 복구를 제공합니다.
- 데이터베이스 소프트웨어를 구성하고 업그레이드합니다.
- 부하 분산을 관리합니다.
- 서버 오류가 있는 경우 투명한 장애 조치를 수행합니다.

다음 목록에서는 각 옵션에 대한 관리 고려 사항을 설명합니다.

* Azure Database for MariaDB를 선택하면 계속해서 직접 데이터베이스를 관리할 수 있습니다. 하지만 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 계속해서 직접 관리할 수 있는 항목은 다음과 같습니다.

  - 데이터베이스
  - 로그인
  - 인덱스 튜닝
  - 쿼리 튜닝
  - 감사
  - 보안

  또한 다른 데이터 센터에 고가용성을 구성하는 데 필요한 구성 및 관리가 최소화됩니다.

* Azure VM의 를 사용하여 운영 체제와 MariaDB 서버 인스턴스 구성을 완벽히 제어할 수 있습니다. VM과 함께 운영 체제 및 데이터베이스 소프트웨어를 업데이트 또는 업그레이드할 시기를 결정합니다. 또한 바이러스 백신 애플리케이션과 같은 추가 소프트웨어를 설치할 시기를 결정합니다. 자동화된 일부 기능이 제공되므로 패치, 백업 및 고가용성이 크게 간소화됩니다. VM 크기, 디스크 수 및 해당 스토리지 구성을 제어할 수 있습니다. 자세한 내용은 [Azure를 위한 가상 머신 및 클라우드 서비스 크기](../virtual-machines/sizes.md)를 참조하세요.

### <a name="time-to-move-to-azure"></a>Azure로 이동해야 하는 경우

* 새로운 솔루션에 대한 개발자 생산성과 빠른 출시 시간이 중요한 경우 클라우드용으로 설계된 애플리케이션에 적합한 솔루션은 Azure Database for MariaDB입니다. DBA 같은 프로그래밍 방식 기능을 제공하는 이 서비스는 기본 운영 체제 및 데이터베이스 관리 부담을 덜어 주기 때문에 클라우드 설계자와 개발자에게 적합합니다.

* 새 온-프레미스 하드웨어를 확보하는 시간과 비용을 방지하려는 경우 Azure VM의 MariaDB가 Windows 또는 Linux에서 MariaDB 데이터베이스와 MariaDB 기능에 액세스해야 하는 애플리케이션에 적합한 솔루션입니다. 또한 이 솔루션은 Azure Database for MariaDB가 잘못된 경우 기존 온-프레미스 애플리케이션 및 데이터베이스를 Azure에 그대로 마이그레이션하는 데 적합합니다.

  프레젠테이션, 애플리케이션 및 데이터 계층을 변경할 필요가 없으므로 기존 솔루션 재설계에 따른 비용과 예산이 절감됩니다. 대신 모든 솔루션을 Azure에 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 일부 성능 최적화를 수행하는 데 집중할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for MariaDB 가격 책정](https://azure.microsoft.com/pricing/details/MariaDB/)을 참조하세요.
* [첫 번째 서버를 만들어서](quickstart-create-mariadb-server-database-using-azure-portal.md) 시작합니다.
