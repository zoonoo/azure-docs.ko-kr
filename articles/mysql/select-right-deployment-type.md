---
title: 올바른 배포 유형 선택-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL을 IaaS (infrastructure as a service) 또는 PaaS (platform as a service)로 배포 하기 전에 고려해 야 하는 요인을 설명 합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 000de084cf9375347704cc4d3905ca36bdd77ff8
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926192"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Azure에서 적절 한 MySQL Server 옵션을 선택 합니다.

Azure를 사용 하면 호스트 된 가상 머신 IaaS (infrastructure as a service) 또는 호스트 된 PaaS (platform as a service)로 MySQL 서버 작업을 실행할 수 있습니다. PaaS는 여러 배포 옵션을 포함 하며 각 배포 옵션 내에 서비스 계층이 있습니다. IaaS와 PaaS 중에 선택할 때 직접 데이터베이스를 관리하고 패치를 적용하고 백업을 수행할 것인지 아니면 이러한 작업을 Azure에 위임할 것인지 결정해야 합니다.

결정을 내릴 때는 다음 두 가지 옵션을 고려해 야 합니다.

- **Azure Database for MySQL** . 이 옵션은 안정적인 버전의 MySQL community edition을 기반으로 하는 완전히 관리 되는 MySQL 데이터베이스 엔진입니다. Azure 클라우드 플랫폼에서 호스트 되는이 관계형 Database as a Service (DBaaS)는 PaaS의 산업 범주에 해당 합니다.

  Azure에서 관리 되는 MySQL 인스턴스를 사용 하 여 자동화 된 패치, 고가용성, 자동화 된 백업, 탄력적 확장, 엔터프라이즈급 보안, 규정 준수 및 거 버 넌 스, 모니터링 및 경고의 기본 제공 기능을 사용 하 여 MySQL 서버가 온-프레미스 또는 Azure VM 인 경우 광범위 한 구성이 필요한 시각화 수 있습니다. MySQL을 서비스로 사용 하는 경우 중단 없이 더 많은 제어를 위해 강화 하거나 규모를 확장 하는 옵션과 함께 종 량 제를 사용 합니다.
  
  MySQL community edition에서 제공 하는 [Azure Database for MySQL](overview.md)은 다음 두 가지 배포 모드에서 사용할 수 있습니다.

  - [단일 서버](single-server-overview.md) 는 완전히 관리 되는 데이터베이스 서비스로, 데이터베이스를 사용자 지정 하기 위한 최소한의 요구 사항이 있습니다. 단일 서버 플랫폼은 최소한의 사용자 구성과 제어를 통해 패치, 백업, 고가용성, 보안 등 대부분의 데이터베이스 관리 기능을 처리하도록 설계되었습니다. 아키텍처는 단일 가용성 영역에서 99.99%의 가용성을 제공하도록 최적화되었습니다. 단일 서버는 패치 일정 및 사용자 지정 MySQL 구성 설정에 대한 세부적인 제어 없이 자동화된 패치를 처리하도록 설계된 클라우드 네이티브 애플리케이션에 가장 적합합니다.

  - [유연한 서버 (미리 보기)](flexible-server/overview.md) 는 데이터베이스 관리 기능 및 구성 설정에 대 한 보다 세부적인 제어 및 유연성을 제공 하도록 설계 된 완전히 관리 되는 데이터베이스 서비스입니다. 일반적으로 서비스는 사용자 요구 사항에 따라 단일 서버 배포에 비해 더 많은 유연성과 서버 구성 사용자 지정을 제공 합니다. 유연한 서버 아키텍처를 통해 사용자는 단일 가용성 영역 및 여러 가용성 영역에서 고가용성을 선택할 수 있습니다. 또한 유연한 서버는 전체 계산 용량이 지속적으로 필요 하지 않은 워크 로드에 이상적 이며, 서버를 시작/중지 하 고 안정화 된 Sku를 사용할 수 있는 향상 된 비용 최적화 제어 기능을 제공 합니다.

  유연한 서버는 다음에 가장 적합 합니다.

  - MySQL 엔진을 더 잘 제어 하 고 사용자 지정 해야 하는 응용 프로그램 개발
  - 영역 중복 고가용성
  - 관리되는 유지 관리 기간

- **Azure vm의 MySQL** 이 옵션은 IaaS의 업계 범주에 해당 합니다. 이 서비스를 사용 하면 Azure 클라우드 플랫폼에서 관리 되는 가상 머신 내에서 MySQL Server를 실행할 수 있습니다. 모든 최신 버전 및 MySQL 버전은 가상 컴퓨터에 설치할 수 있습니다.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Azure에서 MySQL 배포 옵션 비교

이러한 옵션들의 주요 차이점은 다음 표에 나와 있습니다.

| attribute          | Azure Database for MySQL<br/>단일 서버 |Azure Database for MySQL<br/>유연한 서버  |Azure Vm의 MySQL                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| MySQL 버전 지원 | 5.6, 5.7 & 8.0| 5.7 | 모든 버전|
| 컴퓨팅 크기 조정 | 지원 됨 (및에서 기본 계층으로 크기 조정은 지원 되지 않음)| 지원됨 | 지원됨|
| 스토리지 크기 | 5 GiB ~ 16 TiB| 5 GiB ~ 16 TiB | 32 GiB ~ 32767 GiB|
| 온라인 저장소 크기 조정 | 지원됨| 지원됨| 지원되지 않음|
| 자동 저장소 크기 조정 | 지원됨| 미리 보기에서 지원 되지 않음| 지원 안 함|
| 네트워크 연결 | -서버 방화벽을 사용 하는 공용 끝점.<br/> -개인 링크를 지 원하는 개인 액세스|-서버 방화벽을 사용 하는 공용 끝점.<br/> -Virtual Network 통합을 사용한 개인 액세스| -서버 방화벽을 사용 하는 공용 끝점.<br/> -개인 링크를 지 원하는 개인 액세스|
| SLA(서비스 수준 계약) | 99.99% 가용성 SLA |미리 보기에 SLA 없음| 가용성 영역 사용 하는 99.99%|
| 운영 체제 패치| 자동  | 사용자 지정 유지 관리 기간 제어를 사용 하 여 자동 | 최종 사용자가 관리 |
| MySQL 패치     | 자동  | 사용자 지정 유지 관리 기간 제어를 사용 하 여 자동 | 최종 사용자가 관리 |
| 고가용성 | 단일 가용성 영역 내의 기본 제공 HA| 가용성 영역 내 및 가용성 영역 간 기본 제공 HA | 클러스터링, 복제 등을 사용 하 여 사용자 지정 관리|
| 영역 중복 | 지원되지 않음 | 지원됨 | 지원됨|
| 하이브리드 시나리오 | [입력 데이터 복제](./concepts-data-in-replication.md) 지원| 미리 보기에서 사용할 수 없음 | 최종 사용자가 관리 |
| 읽기 복제본 | 지원 됨 (최대 5 개의 복제본)| 지원 됨 (최대 10 개의 복제본)| 최종 사용자가 관리 |
| Backup | 7-35 일 보존으로 자동화 됨 | 1-35 일 보존으로 자동화 됨 | 최종 사용자가 관리 |
| 데이터베이스 작업 모니터링 | 지원됨 | 지원됨 | 최종 사용자가 관리 |
| 재해 복구 | 지역 중복 백업 저장소 및 지역 간 읽기 복제본으로 지원 됨 | 미리 보기에서 지원 되지 않음| 복제 기술로 사용자 지정 관리 |
| Query Performance Insight | 지원됨 | 미리 보기에서 사용할 수 없음| 최종 사용자가 관리 |
| 예약 인스턴스 가격 책정 | 지원됨 | 미리 보기에서 사용할 수 없음 | 지원됨 |
| Azure AD 인증 | 지원됨 | 미리 보기에서 사용할 수 없음 | 지원되지 않음|
| 휴지 상태의 데이터 암호화 | 고객 관리 키로 지원 됨 | 서비스 관리 키로 지원 됨 | 지원되지 않음|
| SSL/TLS | TLS v 1.2, 1.1 및 1.0에 대 한 지원으로 기본적으로 사용 하도록 설정 됨 | TLS v 1.2를 사용 하 여 적용 | TLS v 1.2, 1.1 및 1.0에서 지원 됨 |
| 제 관리 | Azure CLI, PowerShell, REST 및 Azure Resource Manager에서 지원 | Azure CLI, PowerShell, REST 및 Azure Resource Manager에서 지원  | Azure CLI, PowerShell, REST 및 Azure Resource Manager를 사용 하는 Vm에 대해 지원 됨 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS 또는 IaaS 선택을 위한 비즈니스 동기

MySQL 데이터베이스를 호스트 하기 위해 PaaS 또는 IaaS를 선택 하기 위해 결정에 영향을 줄 수 있는 몇 가지 요인이 있습니다.

### <a name="cost"></a>비용

비용 절감은 종종 데이터베이스 호스팅을 위한 최상의 솔루션을 결정 하는 주요 고려 사항입니다. 현금이 부족한 스타트업이거나 탄탄한 회사에 속해 있지만 예산이 넉넉하지 않은 팀인 경우 특히 그렇습니다. 이 섹션에서는 azure Vm의 Azure Database for MySQL 및 MySQL에 적용 되는 Azure의 청구 및 라이선스 기본 사항에 대해 설명 합니다.

#### <a name="billing"></a>결제

Azure Database for MySQL는 현재 리소스에 대해 다른 가격을 사용 하는 여러 계층에서 서비스로 사용할 수 있습니다. 모든 리소스 요금은 고정 요율로 시간당 청구됩니다. 현재 지원 되는 서비스 계층, 계산 크기 및 저장소 양에 대 한 최신 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/mysql/)를 참조 하세요. 응용 프로그램의 다양 한 처리량 요구에 맞게 서비스 계층 및 계산 크기를 동적으로 조정할 수 있습니다. 발신 인터넷 트래픽은 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)이 청구됩니다.

Azure Database for MySQL를 사용 하면 Microsoft에서 자동으로 데이터베이스 소프트웨어를 구성, 패치 및 업그레이드 합니다. 이러한 작업이 자동화되어 있으므로 관리 비용이 줄어듭니다. 또한 Azure Database for MySQL에는 [자동화 된 백업](./concepts-backup.md) 기능이 있습니다. 이러한 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다. 반면 Azure Vm의 MySQL을 사용 하 여 MySQL 버전을 선택 하 고 실행할 수 있습니다. 사용 하는 MySQL 버전에 관계 없이 프로 비전 된 VM에 대해 비용을 지불 하 고, 데이터, 백업, 데이터 및 로그 저장소와 관련 된 저장소 비용 및 사용 된 특정 MySQL 라이선스 유형 (있는 경우)에 대 한 비용을 지불 합니다.

Azure Database for MySQL은 서비스에 대 한 99.99% SLA 보장을 유지 하면서 모든 종류의 노드 수준 중단에 대 한 고가용성을 제공 합니다. 그러나 Vm 내에서 데이터베이스 고가용성을 위해 MySQL 데이터베이스에서 사용할 수 있는 [mysql 복제](https://dev.mysql.com/doc/refman/8.0/en/replication.html) 와 같은 고가용성 옵션을 사용 합니다. 지원되는 고가용성 옵션을 사용하면 추가 SLA가 제공되지 않습니다. 하지만 추가 비용과 관리 오버헤드를 대가로 99.99%보다 높은 데이터베이스 가용성을 달성할 수 있습니다.

가격 책정에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Database for MySQL 가격 책정](https://azure.microsoft.com/pricing/details/mysql/)
- [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>관리

많은 기업이 클라우드 서비스로 전환하기로 결정할 때 비용과 더불어 가장 중요하게 생각하는 것은 관리의 복잡성을 줄이는 것입니다.

IaaS를 선택하면 Microsoft에서 다음 작업을 처리합니다.

- 기본 인프라를 관리합니다.
- 기본 하드웨어 및 OS에 대해 자동화 된 패치를 제공 합니다.
  
PaaS를 선택하면 Microsoft에서 다음 작업을 처리합니다.

- 기본 인프라를 관리합니다.
- 기본 하드웨어, OS 및 데이터베이스 엔진에 대해 자동화 된 패치를 제공 합니다.
- 데이터베이스의 고가용성을 관리합니다.
- 자동으로 백업을 수행하고 모든 데이터를 복제하여 재해 복구를 제공합니다.
- 기본적으로 미사용 데이터 및 사용 중인 데이터를 암호화합니다.
- 서버를 모니터링 하 고 쿼리 성능 정보 및 성능 권장 사항에 대 한 기능을 제공 합니다.

다음 목록에서는 각 옵션에 대 한 관리 고려 사항을 설명 합니다.

- Azure Database for MySQL를 사용 하 여 계속 해 서 데이터베이스를 관리할 수 있습니다. 하지만 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 계속해서 직접 관리할 수 있는 항목은 다음과 같습니다.

  - 데이터베이스
  - 로그인
  - 인덱스 튜닝
  - 쿼리 튜닝
  - 감사
  - 보안

  또한 다른 데이터 센터에 고가용성을 구성하는 데 필요한 구성 및 관리가 최소화됩니다.

- Azure Vm에서 MySQL을 사용 하 여 운영 체제 및 MySQL server 인스턴스 구성을 완전히 제어할 수 있습니다. VM과 함께 운영 체제 및 데이터베이스 소프트웨어를 업데이트 또는 업그레이드할 시기와 적용할 패치를 결정합니다. 또한 바이러스 백신 애플리케이션과 같은 추가 소프트웨어를 설치할 시기를 결정합니다. 자동화된 일부 기능이 제공되므로 패치, 백업 및 고가용성이 크게 간소화됩니다. VM 크기, 디스크 수 및 해당 스토리지 구성을 제어할 수 있습니다. 자세한 내용은 [Azure를 위한 가상 머신 및 클라우드 서비스 크기](../virtual-machines/sizes.md)를 참조하세요.

### <a name="time-to-move-to-azure"></a>Azure로 이동해야 하는 경우

- Azure Database for MySQL는 개발자 생산성과 새 솔루션에 대 한 빠른 출시 시간이 중요 한 경우 클라우드로 설계 된 응용 프로그램에 적합 한 솔루션입니다. DBA 같은 프로그래밍 방식 기능을 제공하는 이 서비스는 기본 운영 체제 및 데이터베이스 관리 부담을 덜어 주기 때문에 클라우드 설계자와 개발자에게 적합합니다.

- 새 온-프레미스 하드웨어를 얻는 시간과 비용을 방지 하려면 Azure Vm의 MySQL이 서비스에서 지원 하지 않거나 기본 OS에 대 한 액세스를 요구 하지 않는 MySQL 엔진의 사용자 지정이 필요한 응용 프로그램에 적합 한 솔루션입니다. 또한이 솔루션은 기존 온-프레미스 응용 프로그램 및 데이터베이스를 Azure에 그대로 마이그레이션하는 데 적합 합니다. Azure Database for MySQL이 잘못 된 경우에 해당 합니다.

프레젠테이션, 응용 프로그램 및 데이터 계층을 변경할 필요가 없기 때문에 기존 솔루션을 다시 설계 하는 데 시간과 예산을 절감할 수 있습니다. 대신 모든 솔루션을 Azure로 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 몇 가지 성능 최적화를 해결 하는 데 집중할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL 가격 책정](https://azure.microsoft.com/pricing/details/MySQL/)을 참조 하세요.
- [첫 번째 서버를 만들어서](./quickstart-create-mysql-server-database-using-azure-portal.md) 시작합니다.
