---
title: 계획 된 유지 관리 알림-Azure Database for MySQL-단일 서버
description: 이 문서에서는 Azure Database for MySQL 단일 서버에서 계획 된 유지 관리 알림 기능에 대해 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: ff197f8add65782a594d64661ffecdaced4598c2
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919627"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL의 계획 된 유지 관리 알림-단일 서버

Azure Database for MySQL에서 계획 된 유지 관리 이벤트를 준비 하는 방법을 알아봅니다.

## <a name="what-is-a-planned-maintenance"></a>계획 된 유지 관리는 무엇 인가요?

Azure Database for MySQL 서비스는 기본 하드웨어, OS 및 데이터베이스 엔진의 자동화 된 패치를 수행 합니다. 패치에는 새로운 서비스 기능, 보안 및 소프트웨어 업데이트가 포함 되어 있습니다. MySQL 엔진의 경우 부 버전 업그레이드는 자동으로 적용되며 패치 주기에 포함됩니다. 패치를 적용하는 데 필요한 사용자 작업 또는 구성 설정은 없습니다. 패치는 광범위 하 게 테스트 되 고 안전한 배포 방법을 사용 하 여 롤아웃 됩니다.

계획 된 유지 관리는 이러한 서비스 업데이트가 지정 된 Azure 지역의 서버에 배포 되는 경우 유지 관리 기간입니다. 계획된 유지 관리 중에 서버를 호스팅하는 Azure 지역에 서비스 업데이트가 배포되면 고객에게 알리기 위해 알림 이벤트가 만들어집니다. 계획 된 두 유지 관리 사이의 최소 기간은 30 일입니다. 다음 유지 관리 기간 72시간에 대한 알림을 미리 받습니다.

## <a name="planned-maintenance---duration-and-customer-impact"></a>계획 된 유지 관리-기간 및 고객에 게 미치는 영향

지정 된 Azure 지역에 대 한 계획 된 유지 관리는 일반적으로 15 시간 동안 실행 될 것으로 예상 됩니다. 또한이 창에는 필요한 경우 롤백 계획을 실행 하는 데 필요한 버퍼 시간도 포함 됩니다. 계획 된 유지 관리 중에는 데이터베이스 서버를 다시 시작 하거나 장애 조치 (failover) 할 수 있으며,이로 인해 최종 사용자가 데이터베이스 서버를 잠깐 사용할 수 없게 될 수 있습니다. Azure Database for MySQL 서버는 컨테이너에서 실행 되므로 데이터베이스 서버를 다시 시작 하는 것은 일반적으로 빠르게 60-120 초 내에 완료 될 것으로 예상 됩니다. 각 서버 다시 시작을 포함 하 여 계획 된 전체 유지 관리 이벤트는 엔지니어링 팀에서 신중 하 게 모니터링 합니다. 서버 장애 조치 (failover) 시간은 데이터베이스 복구 시간에 따라 달라 지 며 장애 조치 (failover) 시 서버에서 트랜잭션 작업이 많은 경우 데이터베이스가 더 오래 온라인 상태가 될 수 있습니다. 다시 시작 시간을 방지 하려면 계획 된 유지 관리 이벤트 동안 장기 실행 트랜잭션 (대량 로드)을 방지 하는 것이 좋습니다.

요약 하자면 계획 된 유지 관리 이벤트는 15 시간 동안 실행 되지만 일반적으로 개별 서버 영향은 서버에서 트랜잭션 활동에 따라 60 초 동안 지속 됩니다. 지정 된 지역에 대해 계획 된 유지 관리를 시작 하 고 다른 하나는 유지 관리를 시작 하기 전에 72의 일정 시간에 알림이 전송 됩니다.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>계획 된 유지 관리에 대 한 통지를 받으려면 어떻게 해야 하나요?

계획 된 유지 관리 알림 기능을 활용 하 여 예정 된 예정 된 유지 관리 이벤트에 대 한 경고를 받을 수 있습니다. 이벤트 이전에 예정 된 유지 관리 72 시간에 대 한 알림과 지정 된 지역에 대 한 유지 관리 작업이 진행 되는 동안 해당 시간에 대 한 알림을 받게 됩니다.

### <a name="planned-maintenance-notification"></a>계획 된 유지 관리 알림

> [!IMPORTANT]
> 계획 된 유지 관리 알림은 현재 미국 서 부를 **제외한** 모든 지역에서 미리 보기로 제공 됩니다.

**계획 된 유지 관리 알림을** 통해 예정 된 예정 된 유지 관리 이벤트에 대 한 경고를 Azure Database for MySQL 받을 수 있습니다. 이러한 알림은 [Service Health의](../service-health/overview.md) 계획 된 유지 관리와 통합 되어 있으므로 구독에 대해 예약 된 유지 관리 작업을 한 곳에서 모두 볼 수 있습니다. 또한 다양 한 리소스를 담당 하는 여러 연락처가 있을 수 있으므로 다양 한 리소스 그룹에 대 한 올바른 대상으로 알림을 확장 하는 데 도움이 됩니다. 이벤트 전에 예정 된 유지 관리 72 시간에 대 한 알림을 받게 됩니다.

모든 이벤트에 대해 **계획 된 유지 관리 알림** 72 시간 통지를 제공 하려고 합니다. 그러나 중요 또는 보안 패치의 경우 알림이 이벤트에 더 가까이 전송 되거나 생략 될 수 있습니다.

Azure Portal에 대 한 계획 된 유지 관리 알림을 확인 하거나 알림을 받도록 경고를 구성할 수 있습니다. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Azure Portal에서 계획 된 유지 관리 알림 확인

1. [Azure Portal](https://portal.azure.com)에서 **Service Health** 를 선택 합니다.
2. **계획 된 유지 관리** 탭 선택
3. 계획 된 유지 관리 알림을 확인 하려는 **구독**, **지역** 및 **서비스** 를 선택 합니다. 
   
### <a name="to-receive-planned-maintenance-notification"></a>계획 된 유지 관리 알림을 받으려면

1. [포털](https://portal.azure.com)에서 **서비스 상태** 를 선택합니다.
2. **경고** 섹션에서 **상태 경고** 를 선택합니다.
3. **+ 서비스 상태 경고 추가** 를 선택 하 고 필드를 입력 합니다.
4. 필수 필드를 입력 합니다. 
5. **이벤트 유형을** 선택 하 고 **계획 된 유지 관리** 를 선택 하거나 **모두를 선택** 합니다.
6. **작업 그룹** 에서 경고를 수신 하는 방법 (전자 메일 가져오기, 논리 앱 트리거 등)을 정의 합니다.  
7. 규칙을 만들 때 규칙 사용이 예로 설정 되어 있는지 확인 합니다.
8. 경고 **규칙 만들기** 를 선택 하 여 경고를 완료 합니다.

**서비스 상태 경고** 를 만드는 방법에 대 한 자세한 단계는 [서비스 알림에서 활동 로그 경고 만들기](../service-health/alerts-activity-log-service-notifications-portal.md)를 참조 하세요.

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>계획 된 유지 관리를 취소 하거나 연기할 수 있나요?

유지 관리는 서버를 안전 하 고 안정적 이며 최신 상태로 유지 하기 위해 필요 합니다. 계획 된 유지 관리 이벤트는 취소 하거나 연기할 수 없습니다. 지정 된 Azure 지역으로 알림이 전송 되 면 해당 지역의 개별 서버에 대해 패치 일정을 변경할 수 없습니다. 한 번에 전체 지역에 대해 패치가 롤아웃 됩니다. Azure Database for MySQL-단일 서버 서비스는 서비스를 세부적으로 제어 하거나 사용자 지정할 필요가 없는 클라우드 네이티브 응용 프로그램용으로 설계 되었습니다. 서버에 대 한 유지 관리를 예약 하는 기능을 원하는 경우 [유연한 서버](./flexible-server/overview.md)를 고려 하는 것이 좋습니다.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>모든 Azure 지역에 동시에 패치가 적용 되나요?

아니요, 모든 Azure 지역은 배포 단위 기간 동안 패치 됩니다. 배포 단위는 일반적으로 지정 된 Azure 지역에서 오전 5 시-오전 8 시 현지 시간에 확장 됩니다. 지리적으로 쌍을 이루는 Azure 지역은 며칠 마다 패치 됩니다. 데이터베이스 서버의 고가용성 및 비즈니스 연속성을 위해 [지역 간 읽기 복제본](./concepts-read-replicas.md#cross-region-replication) 을 활용 하는 것이 좋습니다.

## <a name="retry-logic"></a>재시도 논리

일시적 결함이라고도 하는 일시적 오류는 자체적으로 해결되는 오류입니다. 유지 관리 중에 [일시적인 오류가](./concepts-connectivity.md#transient-errors) 발생할 수 있습니다. 이러한 이벤트의 대부분은 60초 이내에 시스템에서 자동으로 완화됩니다. 일시적인 오류는 [재시도 논리](./concepts-connectivity.md#handling-transient-errors)를 사용 하 여 처리 해야 합니다.


## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL 작업에 대 한 질문이 나 제안 사항은에서 Azure Database for MySQL 팀에 전자 메일을 보냅니다. AskAzureDBforMySQL@service.microsoft.com
- 메트릭에 대 한 경고를 만드는 방법에 대 한 지침은 [경고를 설정 하는 방법을](howto-alert-on-metric.md) 참조 하세요.
- [Azure Database for MySQL 단일 서버에 대 한 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)
- [일시적인 오류를 처리 하 고 Azure Database for MySQL-단일 서버에 효율적으로 연결](concepts-connectivity.md)
