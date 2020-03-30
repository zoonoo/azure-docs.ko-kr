---
title: 올바른 배포 유형 선택 - MySQL용 Azure 데이터베이스
description: 이 문서에서는 MySQL용 Azure Database를 서비스(IaaS) 또는 PaaS(서비스로 하는 플랫폼)로 인프라로 배포하기 전에 고려해야 할 요소에 대해 설명합니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255543"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Azure에서 올바른 MySQL 서버 옵션을 선택합니다.

Azure를 사용하면 MySQL 서버 워크로드가 IaaS(서비스) 또는 서비스로 호스팅된 플랫폼(PaaS)으로 호스팅된 가상 시스템 인프라에서 실행할 수 있습니다. PaaS에는 여러 배포 옵션이 있으며 각 배포 옵션 내에 서비스 계층이 있습니다. IaaS와 PaaS 중에서 선택할 때 데이터베이스를 관리하고, 패치를 적용하고, 백업을 할지 또는 이러한 작업을 Azure에 위임할지 결정해야 합니다.

결정을 내릴 때 다음 두 가지 옵션을 고려하십시오.

- **MySQL에 대 한 Azure 데이터베이스**. 이 옵션은 MySQL 커뮤니티 에디션의 안정적인 버전을 기반으로 완전히 관리되는 MySQL 데이터베이스 엔진입니다. Azure 클라우드 플랫폼에서 호스팅되는 서비스(DBaaS)로서의 관계형 데이터베이스는 PaaS의 업계 범주에 속합니다.

  Azure에서 MySQL의 관리되는 인스턴스를 사용하면 MySQL Server가 온-프레미스 또는 Azure VM에 있을 때 광범위한 구성이 필요한 기본 제공 기능을 사용할 수 있습니다.

  MySQL을 서비스로 사용하는 경우 중단 없이 더 큰 제어를 위해 확장하거나 확장할 수 있는 옵션을 사용할 때 비용을 지불합니다. 독립 실행형 MySQL 서버와 달리 MySQL용 Azure 데이터베이스에는 기본 제공 고가용성, 인텔리전스 및 관리와 같은 추가 기능이 있습니다.

- **Azure VM에서 MySQL**. 이 옵션은 IaaS의 산업 범주에 속합니다. 이 서비스를 사용하면 Azure 클라우드 플랫폼에서 완전히 관리되는 가상 시스템 내에서 MySQL Server를 실행할 수 있습니다. MySQL의 모든 최신 버전과 버전은 IaaS 가상 머신에 설치할 수 있습니다.

  MySQL용 Azure 데이터베이스와 가장 큰 차이점은 Azure VM의 MySQL은 데이터베이스 엔진에 대한 제어를 제공합니다. 그러나 이 제어는 VM 및 많은 DBA(데이터베이스 관리) 작업을 관리하는 책임을 부담해야 합니다. 이러한 작업에는 데이터베이스 서버 유지 관리 및 패치, 데이터베이스 복구 및 고가용성 디자인이 포함됩니다.

이러한 옵션들의 주요 차이점은 다음 표에 나와 있습니다.

|            | Azure Database for MySQL | Azure VM의 MySQL    |
|:-------------------|:-----------------------------|:--------------------|
| 서비스 수준 계약(SLA)                | 99.99%의 가용성SLA 제공| 동일한 가용성 집합에서 두 개 이상의 인스턴스를 사용할 경우 최대 99.95%의 가용성을 사용할 수 있습니다.<br/><br/>프리미엄 스토리지를 사용하는 단일 인스턴스 VM의 가용성은 99.9% 입니다.<br/><br/>99.99%는 여러 가용성 집합에서 여러 인스턴스가 있는 가용성 영역을 사용합니다.<br/><br/>가상 [컴퓨터 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)를 참조하십시오. |
| 운영 체제 패치        | 자동  | 고객에 의해 관리 |
| MySQL 패치     | 자동  | 고객에 의해 관리 |
| 고가용성 | 고가용성(HA) 모델은 노드 수준 중단이 발생하는 경우에 대한 기본 제공 장애 조치 메커니즘을 기반으로 합니다. 이러한 경우 서비스는 자동으로 새 인스턴스를 만들고 이 인스턴스에 저장소를 연결합니다. | 고객은 고가용성을 설계, 구현, 테스트 및 유지 관리합니다. 기능에는 클러스터링, 복제 등이 포함될 수 있습니다.|
| 영역 중복 | 현재 지원되지 않음 | Azure VM은 서로 다른 가용성 영역에서 실행되도록 설정할 수 있습니다. 온-프레미스 솔루션의 경우 고객은 자체 보조 데이터 센터를 생성, 관리 및 유지 관리해야 합니다.|
| 하이브리드 시나리오 | [데이터 입력 복제를](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)사용하면 외부 MySQL 서버의 데이터를 MySQL 서비스에 대한 Azure 데이터베이스로 동기화할 수 있습니다. 외부 서버는 온-프레미스 가상 머신 또는 다른 클라우드 공급 기업이 호스트하는 데이터베이스 서비스에 있을 수 있습니다.<br/><br/> 읽기 [복제기능을](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) 사용하면 MySQL 마스터 서버용 Azure 데이터베이스의 데이터를 최대 5개의 읽기 전용 복제본 서버로 복제할 수 있습니다. 복제본은 동일한 Azure 지역 내에 있거나 여러 지역에 걸쳐 있습니다. 읽기 전용 복제본은 binlog 복제 기술을 사용하여 비동기적으로 업데이트됩니다.| 고객에 의해 관리
| 백업 및 복원 | [서버 백업을](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) 자동으로 만들고 로컬중복 또는 지리적 중복인 사용자 구성 저장소에 저장합니다. 이 서비스는 전체, 차등 및 트랜잭션 로그 백업을 수행합니다. | 고객에 의해 관리 |
| 데이터베이스 작업 모니터링 | 고객에게 데이터베이스 작업에 대한 [경고를 설정하고](https://docs.microsoft.com/azure/mysql/concepts-monitoring) 임계값에 도달할 때 조치를 취하지 할 수 있는 기능을 제공합니다. | 고객에 의해 관리 |
| 고급 위협 보호 | [고급 위협 보호](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal)기능을 제공합니다. 이 보호는 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 감지합니다. | 고객은 이 보호 기능을 스스로 구축해야 합니다.
| 재해 복구 | 사용자 구성 로컬 [중복 또는 지리적 중복 저장소에](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)자동화된 백업을 저장합니다. 백업은 서버를 한 시점으로 복원할 수도 있습니다. 보존 기간은 7일에서 35일까지입니다. 복원은 Azure 포털을 사용하여 수행됩니다. | 고객이 완벽하게 관리합니다. 책임은 예약, 테스트, 보관, 저장 및 보존에 국한되지 않습니다. 추가 옵션은 Azure 복구 서비스 자격 증명 모음을 사용하여 VM에서 Azure VM 및 데이터베이스를 백업하는 것입니다. 이 옵션은 미리 보기 상태입니다. |
| 성능 권장 사항 | 시스템에서 생성된 사용 로그 파일을 기반으로 [성능 권장 사항을](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) 고객에게 제공합니다. 권장 사항은 워크로드를 최적화하는 데 도움이 됩니다. | 고객에 의해 관리 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS 또는 IaaS 를 선택하는 비즈니스 동기

MySQL 데이터베이스를 호스팅할 PaaS 또는 IaaS를 선택하는 결정에 영향을 미칠 수 있는 몇 가지 요인이 있습니다.

### <a name="cost"></a>비용

제한된 자금조달은 종종 데이터베이스를 호스팅하는 데 가장 적합한 솔루션을 결정하는 주요 고려 사항입니다. 이는 현금이 적은 스타트업이든, 예산 제약이 엄격한 상황에서 운영되는 설립된 회사의 팀이든 마찬가지입니다. 이 섹션에서는 Azure VM의 MySQL 및 MySQL용 Azure 데이터베이스에 적용되는 Azure의 청구 및 라이선스 기본 사항에 대해 설명합니다.

#### <a name="billing"></a>결제

MySQL용 Azure 데이터베이스는 현재 리소스에 대해 가격이 다른 여러 계층의 서비스로 사용할 수 있습니다. 모든 리소스는 고정 된 속도로 시간당 청구됩니다. 현재 지원되는 서비스 계층, 계산 크기 및 저장소 양에 대한 최신 정보는 [vCore 기반 구매 모델을](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)참조하십시오. 애플리케이션의 다양한 처리량 요구에 맞게 서비스 계층과 크기를 동적으로 조정할 수 있습니다. 나가는 인터넷 트래픽에 대한 요금이 일반 [데이터 전송 요금으로](https://azure.microsoft.com/pricing/details/data-transfers/)청구됩니다.

MySQL용 Azure 데이터베이스를 사용하면 Microsoft는 데이터베이스 소프트웨어를 자동으로 구성, 패치 및 업그레이드합니다. 이러한 자동화된 작업은 관리 비용을 절감합니다. 또한 MySQL용 Azure 데이터베이스에는 [기본 제공 백업](https://docs.microsoft.com/azure/mysql/concepts-backup) 기능이 있습니다. 이러한 기능을 사용하면 특히 데이터베이스가 많은 경우 상당한 비용을 절감할 수 있습니다. 반대로 Azure VM의 MySQL을 사용하면 MySQL 버전을 선택하고 실행할 수 있습니다. 어떤 MySQL 버전을 사용하든 프로비저닝된 VM과 사용된 특정 MySQL 라이선스 유형에 대한 비용을 지불합니다.

MySQL용 Azure 데이터베이스는 서비스에 대한 99.99% SLA 보장을 유지하면서 모든 종류의 노드 수준 중단에 대해 기본 제공 고가용성을 제공합니다. 그러나 VM 내에서 데이터베이스 고가용성의 경우 고객은 MySQL 데이터베이스에서 사용할 수 있는 [MySQL 복제와](https://dev.mysql.com/doc/refman/8.0/en/replication.html) 같은 고가용성 옵션을 사용해야 합니다. 지원되는 고가용성 옵션을 사용하면 추가 SLA가 제공되지 않습니다. 그러나 추가 비용 및 관리 오버헤드로 99.99% 이상의 데이터베이스 가용성을 달성할 수 있습니다.

가격 책정에 대한 자세한 내용은 다음 문서를 참조하십시오.
* [MySQL 가격 책정을 위한 Azure 데이터베이스](https://azure.microsoft.com/pricing/details/mysql/)
* [가상 컴퓨터 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>관리

많은 기업에서 클라우드 서비스로 전환하기로 결정하는 것은 비용만큼이나 관리의 복잡성을 오프로드하는 것입니다. IaaS와 PaaS, 마이크로 소프트와 함께 :

- 기본 인프라를 관리합니다.
- 모든 데이터를 자동으로 복제하여 재해 복구를 제공합니다.
- 데이터베이스 소프트웨어를 구성하고 업그레이드합니다.
- 부하 분산을 관리합니다.
- 서버 오류가 있는 경우 투명 장애 조치(failover)를 수행합니다.

다음 목록에서는 각 옵션에 대한 관리 고려 사항에 대해 설명합니다.

* MySQL용 Azure 데이터베이스를 사용하면 데이터베이스를 계속 관리할 수 있습니다. 그러나 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 계속 관리할 수 있는 항목의 예는 다음과 같습니다.

  - 데이터베이스
  - 로그인
  - 인덱스 튜닝
  - 쿼리 튜닝
  - 감사
  - 보안

  또한 다른 데이터 센터에 고가용성을 구성하려면 구성이나 관리가 거의 필요하지 않습니다.

* Azure VM에서 MySQL을 사용하면 운영 체제 및 MySQL 서버 인스턴스 구성을 완전히 제어할 수 있습니다. VM을 사용하면 운영 체제 및 데이터베이스 소프트웨어를 업데이트하거나 업그레이드할 시기를 결정합니다. 또한 바이러스 백신 응용 프로그램과 같은 추가 소프트웨어를 설치할 시기도 결정합니다. 패치, 백업 및 고가용성을 크게 간소화하기 위해 일부 자동화된 기능이 제공됩니다. VM의 크기, 디스크 수 및 해당 저장소 구성을 제어할 수 있습니다. 자세한 내용은 [Azure 에 대한 가상 컴퓨터 및 클라우드 서비스 크기를](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)참조하십시오.

### <a name="time-to-move-to-azure"></a>Azure로 이동해야 하는 경우

* MySQL용 Azure Database는 개발자 생산성과 새로운 솔루션에 대한 출시 시간이 매우 중요한 경우 클라우드에서 디자인한 애플리케이션에 적합한 솔루션입니다. DBA와 같은 프로그래밍 방식 기능을 갖춘 이 서비스는 기본 운영 체제 및 데이터베이스를 관리할 필요성을 낮추기 때문에 클라우드 설계자와 개발자에게 적합합니다.

* 새 온-프레미스 하드웨어를 구입하는 데 드는 시간과 비용을 피하려는 경우 Azure VM의 MySQL은 MySQL 데이터베이스 또는 Windows 또는 Linux의 MySQL 기능에 대한 액세스가 필요한 응용 프로그램에 적합한 솔루션입니다. 이 솔루션은 MySQL용 Azure 데이터베이스가 적합하지 않은 경우 기존 온-프레미스 응용 프로그램 및 데이터베이스를 Azure로 그대로 마이그레이션하는 데도 적합합니다.

  프레젠테이션, 응용 프로그램 및 데이터 계층을 변경할 필요가 없으므로 기존 솔루션을 다시 설계하는 데 시간과 예산을 절약할 수 있습니다. 대신 모든 솔루션을 Azure로 마이그레이션하고 Azure 플랫폼에 필요한 일부 성능 최적화를 해결하는 데 집중할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [MySQL 가격 책정에 대한 Azure 데이터베이스를](https://azure.microsoft.com/pricing/details/MySQL/)참조하십시오.
* [첫 번째 서버를 만들어](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)시작하십시오.
