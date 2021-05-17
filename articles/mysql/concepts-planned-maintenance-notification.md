---
title: 계획된 유지 관리 알림 - Azure Database for MySQL - 단일 서버
description: 이 문서에서는 Azure Database for MySQL - 단일 서버의 계획된 유지 관리 알림 기능에 대해 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: c6d1bfbf1592da3a5e632eb875221225630aed3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108675"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL의 계획된 유지 관리 알림 - 단일 서버

Azure Database for MySQL에서 계획된 유지 관리 이벤트를 준비하는 방법을 알아봅니다.

## <a name="what-is-a-planned-maintenance"></a>계획된 유지 관리는 무엇인가요?

Azure Database for MySQL 서비스는 기본 하드웨어, OS 및 데이터베이스 엔진의 자동화된 패치를 수행합니다. 패치에는 새로운 서비스 기능, 보안 및 소프트웨어 업데이트가 포함되어 있습니다. MySQL 엔진의 경우 부 버전 업그레이드는 자동으로 적용되며 패치 주기에 포함됩니다. 패치를 적용하는 데 필요한 사용자 작업 또는 구성 설정은 없습니다. 패치는 광범위하게 테스트되고 안전한 배포 방법을 사용하여 출시됩니다.

계획된 유지 관리는 서비스 업데이트가 지정된 Azure 지역의 서버에 배포되는 유지 관리 기간입니다. 계획된 유지 관리 중에 서버를 호스팅하는 Azure 지역에 서비스 업데이트가 배포되면 고객에게 알리기 위해 알림 이벤트가 만들어집니다. 계획된 두 유지 관리 사이의 최소 기간은 30일입니다. 다음 유지 관리 기간 72시간에 대한 알림을 미리 받습니다.

## <a name="planned-maintenance---duration-and-customer-impact"></a>계획된 유지 관리 - 기간 및 고객에게 미치는 영향

지정된 Azure 지역에 대한 계획된 유지 관리는 일반적으로 15시간 동안 실행될 것으로 예상됩니다. 또한 이 기간에는 필요한 경우 롤백 계획을 실행하는 데 필요한 버퍼 시간도 포함됩니다. 계획된 유지 관리 중에는 데이터베이스 서버 다시 시작 또는 장애 조치(failover)로 인해 최종 사용자가 데이터베이스 서버를 잠깐 사용하지 못할 수 있습니다. Azure Database for MySQL 서버는 컨테이너에서 실행되므로 일반적으로 데이터베이스 서버가 빠르게 다시 시작됩니다(일반적으로 60~120초 내에 다시 시작이 완료될 것으로 예상). 각 서버 다시 시작을 포함하여 전체 계획된 유지 관리 이벤트는 엔지니어링 팀에서 신중하게 모니터링합니다. 서버 장애 조치 시간은 데이터베이스 복구 시간에 따라 달라지므로 장애 조치 시 서버에서 트랜잭션 작업이 많은 경우 데이터베이스가 온라인 상태가 되는 데 더 오래 걸릴 수 있습니다. 다시 시작 시간이 길어지지 않도록 하려면 계획된 유지 관리 이벤트 중에는 장기 실행 트랜잭션(대량 로드)을 피하는 것이 좋습니다.

요약하자면 계획된 유지 관리 이벤트는 15시간 동안 실행되지만 개별 서버 영향은 서버의 트랜잭션 작업에 따라 일반적으로 60초간 지속됩니다. 알림은 계획된 유지 관리가 시작되기 72시간 전에 전송되고 지정된 지역에 대해 유지 관리가 진행 중일 때 또 다른 알림이 전송됩니다.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>계획된 유지 관리에 관한 알림은 어떻게 받나요?

계획된 유지 관리 알림 기능을 활용하여 예정된 유지 관리 이벤트에 대한 경고를 받을 수 있습니다. 이벤트 72시간 전에 예정된 유지 관리에 대한 알림을 받고 지정된 지역에 대한 유지 관리가 진행되는 동안 또 다른 알림을 받게 됩니다.

### <a name="planned-maintenance-notification"></a>계획된 유지 관리 알림

> [!IMPORTANT]
> 계획된 유지 관리 알림은 현재 미국 중서부를 **제외한** 모든 지역에서 미리 보기로 제공됩니다.

**계획된 유지 관리 알림** 을 사용하면 Azure Database for MySQL에 대해 예정된 유지 관리 이벤트에 대한 경고를 받을 수 있습니다. 해당 알림은 [Service Health](../service-health/overview.md)의 계획된 유지 관리와 통합되며 알림을 통해 구독에 대해 예약된 모든 유지 관리를 한 곳에서 볼 수 있습니다. 리소스마다 담당하는 연락처가 다를 수 있으므로 다양한 리소스 그룹의 적절한 대상 그룹으로 알림을 스케일링하는 데도 도움이 됩니다. 이벤트 72시간 전에 예정된 유지 관리에 대한 알림을 받게 됩니다.

Microsoft는 모든 이벤트에 대해 72시간 전에 **계획된 유지 관리 알림** 을 제공하고자 최선을 다합니다. 그러나 중요한 패치 또는 보안 패치인 경우 알림이 더 늦게 전송되거나 생략될 수 있습니다.

Azure Portal에서 계획된 유지 관리 알림을 확인하거나 알림을 받도록 경고를 구성할 수 있습니다. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Azure Portal에서 계획된 유지 관리 알림 확인

1. [Azure Portal](https://portal.azure.com)에서 **Service Health** 를 선택합니다.
2. **계획된 유지 관리** 탭을 선택합니다.
3. 계획된 유지 관리 알림을 확인하려는 **구독**, **지역** 및 **서비스** 를 선택합니다. 
   
### <a name="to-receive-planned-maintenance-notification"></a>계획된 유지 관리 알림을 받으려면

1. [포털](https://portal.azure.com)에서 **서비스 상태** 를 선택합니다.
2. **경고** 섹션에서 **상태 경고** 를 선택합니다.
3. **+ 서비스 상태 경고 추가** 를 선택하고 필드를 입력합니다.
4. 필수 필드를 입력합니다. 
5. **이벤트 유형** 을 선택하고 **계획된 유지 관리** 또는 **모두 선택** 을 선택합니다.
6. **작업 그룹** 에서 경고를 받을 방법(메일 받기, 논리 앱 트리거 등)을 정의합니다.  
7. 규칙을 만들면 바로 사용이 예로 설정되어 있는지 확인합니다.
8. **경고 규칙 만들기** 를 선택하여 경고를 완성합니다.

**서비스 상태 경고** 를 만드는 방법에 대한 자세한 단계는 [서비스 알림에 대한 활동 로그 경고 만들기](../service-health/alerts-activity-log-service-notifications-portal.md)를 참조하세요.

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>계획된 유지 관리를 취소하거나 연기할 수 있나요?

유지 관리는 서버를 안전하고 안정적이며 최신 상태로 유지하기 위해 필요합니다. 계획된 유지 관리 이벤트는 취소하거나 연기할 수 없습니다. 알림이 지정된 Azure 지역으로 전송된 다음에는 해당 지역의 개별 서버에 대한 패치 적용 일정을 변경할 수 없습니다. 패치는 전체 지역에 대해 한 번에 출시됩니다. Azure Database for MySQL - 단일 서버 서비스는 서비스를 세부적으로 제어하거나 사용자 지정할 필요가 없는 클라우드 네이티브 애플리케이션용으로 설계되었습니다. 서버에 대한 유지 관리를 예약할 수 있는 기능을 원하는 경우 [유연한 서버](./flexible-server/overview.md)를 사용하는 것이 좋습니다.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>모든 Azure 지역에 패치가 동시에 적용되나요?

아니요, 배포 단위 기간 동안 모든 Azure 지역에 패치가 적용됩니다. 배포 단위 기간은 일반적으로 지정된 Azure 지역의 현지 시간으로 오후 5시부터 다음날 오전 8시까지로 연장됩니다. 지역 쌍으로 연결된 Azure 지역은 다른 날짜에 패치가 적용됩니다. 데이터베이스 서버의 고가용성 및 비즈니스 연속성을 위해 [지역 간 읽기 복제본](./concepts-read-replicas.md#cross-region-replication)을 활용하는 것이 좋습니다.

## <a name="retry-logic"></a>재시도 논리

일시적 결함이라고도 하는 일시적 오류는 자체적으로 해결되는 오류입니다. [일시적 오류](./concepts-connectivity.md#transient-errors)는 유지 관리 중에 발생할 수 있습니다. 이러한 이벤트의 대부분은 60초 이내에 시스템에서 자동으로 완화됩니다. 일시적 오류는 [재시도 논리](./concepts-connectivity.md#handling-transient-errors)를 사용하여 처리해야 합니다.


## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL 작업에 대해 궁금한 점이나 제안하고 싶은 의견이 있으면 Azure Database for MySQL 팀(AskAzureDBforMySQL@service.microsoft.com)으로 메일을 보내주세요.
- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-alert-on-metric.md)을 참조하세요.
- [Azure Database for MySQL - 단일 서버의 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)
- [일시적 오류를 처리하고 Azure Database for MySQL - 단일 서버에 효율적으로 연결](concepts-connectivity.md)
