---
title: 복제본 읽기 - MySQL용 Azure 데이터베이스입니다.
description: 'MySQL용 Azure 데이터베이스에서 읽기 복제본: 지역 선택, 복제본 만들기, 복제본 에 연결, 복제본 모니터링 및 복제 중지에 대해 알아봅니다.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 47f686f810f62fe03a9b0217677c436f3b91782b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767877"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Azure Database for MySQL의 읽기 복제본

읽기 복제본 기능을 사용하면 Azure Database for MySQL 서버에서 읽기 전용 서버로 데이터를 복제할 수 있습니다. 마스터 서버에서 최대 5개의 복제본으로 복제할 수 있습니다. 읽복제본은 MySQL 엔진의 네이티브 이진 로그(binlog) 파일의 위치 기반 복제 기술을 사용하여 비동기식으로 업데이트됩니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다.

복제본은 MySQL 서버에 대한 일반 Azure 데이터베이스와 유사한 새 서버입니다. 읽기 복제본의 경우, vCore 및 스토리지에 프로비저닝된 컴퓨팅에 대한 비용이 GB/월 단위로 청구됩니다.

MySQL 복제 기능 및 문제에 대한 자세한 내용은 [MySQL 복제 설명서를](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)참조하십시오.

## <a name="when-to-use-a-read-replica"></a>읽기 복제본을 사용하는 경우

읽기 복제본 기능은 읽기 작업이 많은 워크로드의 성능과 규모를 향상시키는 데 도움이 됩니다. 읽기 워크로드를 복제본과 격리할 수 있고 쓰기 워크로드를 마스터로 보낼 수 있습니다.

일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.

복제본은 읽기 전용이기 때문에 마스터에 대한 쓰기 용량 부담을 직접적으로 줄이지는 못합니다. 이 기능은 쓰기 작업이 많은 워크로드를 대상으로 하지 않습니다.

읽기 복제본 기능은 MySQL 비동기 복제를 사용합니다. 이 기능은 동기식 복제 시나리오를 위한 것이 아닙니다. 마스터와 복제본 간에는 측정 가능한 지연이 발생합니다. 복제본의 데이터는 결과적으로 마스터의 데이터와 일치하게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다.

## <a name="cross-region-replication"></a>지역 간 복제
마스터 서버와 다른 지역에서 읽기 복제본을 만들 수 있습니다. 지역 간 복제는 재해 복구 계획 또는 사용자에게 데이터를 더 가까이 가져오는 등의 시나리오에 유용할 수 있습니다.

[MySQL 영역에 대한](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)모든 Azure 데이터베이스에 마스터 서버를 가질 수 있습니다.  마스터 서버는 쌍을 이루는 리전 또는 범용 복제본 지역에 복제본을 가질 수 있습니다. 아래 그림은 마스터 지역에 따라 사용할 수 있는 복제본 영역을 보여 줍니다.

[![복제본 영역 읽기](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>범용 복제 영역
마스터 서버의 위치에 관계없이 다음 리전에서 읽기 복제본을 만들 수 있습니다. 지원되는 범용 복제본 영역은 다음과 같습니다.

오스트레일리아 동부, 오스트레일리아 남동부, 미국 중부, 동아시아, 미국 동부, 미국 동부, 동북2, 일본 동부, 일본 서부, 한국 중부, 한국 중부 미국, 북중부, 북유럽, 미국 중남부, 동남아시아, 영국 남부, 영국 서부, 서유럽, 서부 미국.

*미국 서부 2는 교차 지역 복제 위치로 일시적으로 사용할 수 없습니다.

### <a name="paired-regions"></a>쌍을 이루는 지역
범용 복제본 영역 외에도 마스터 서버의 Azure 쌍을 이루는 리전에서 읽기 복제본을 만들 수 있습니다. 리전 쌍을 모르는 경우 [Azure 쌍을 이루는 지역 문서에서](../best-practices-availability-paired-regions.md)자세히 알아볼 수 있습니다.

재해 복구 계획에 지역 간 복제본을 사용하는 경우 다른 지역 중 하나가 아닌 페어링된 리전에서 복제본을 만드는 것이 좋습니다. 쌍을 이루는 지역은 동시 업데이트를 피하고 물리적 격리 및 데이터 상지의 우선 순위를 지정합니다.  

그러나 다음과 같은 제한 사항이 있습니다. 

* 지역별 가용성: MySQL용 Azure 데이터베이스는 미국 서부 2, 프랑스 중부, 아랍에미리트 북부 및 독일 중부에서 사용할 수 있습니다. 그러나 쌍을 이루는 영역은 사용할 수 없습니다.
    
* 단방향 쌍: 일부 Azure 영역은 한 방향으로만 페어링됩니다. 이러한 지역에는 서인도, 브라질 남부 및 미국 정부 버지니아가 포함됩니다. 
   즉, 서인도의 마스터 서버는 인도 남부에서 복제본을 만들 수 있습니다. 그러나 인도 남부의 마스터 서버는 서인도에서 복제본을 만들 수 없습니다. 이는 서인도의 보조 지역이 인도 남부이지만 남인도의 보조 지역은 서인도가 아니기 때문입니다.


## <a name="create-a-replica"></a>복제본 만들기

마스터 서버에 기존 복제 서버가 없는 경우 마스터가 먼저 다시 시작하여 복제를 준비합니다.

복제본 워크플로 만들기를 시작하면 MySQL 서버에 대한 빈 Azure 데이터베이스가 만들어집니다. 새 서버는 마스터 서버에 있는 데이터로 채워집니다. 생성 시간은 마스터의 데이터 양과 지난 주 전체 백업 이후의 시간에 따라 달라집니다. 시간은 몇 분에서 몇 시간까지 걸릴 수 있습니다.

모든 복제본은 저장소 자동 성장에 사용할 수 [있습니다.](concepts-pricing-tiers.md#storage-auto-grow) 자동 증가 기능을 사용하면 복제본이 복제된 데이터를 계속 사용할 수 있으며 저장소 부족 오류로 인한 복제 중단을 방지할 수 있습니다.

[Azure Portal에서 읽기 복제본을 만드는 방법](howto-read-replicas-portal.md)을 알아봅니다.

## <a name="connect-to-a-replica"></a>복제본에 연결

생성 시 복제본은 마스터 서버의 방화벽 규칙을 상속합니다. 이후에 이러한 규칙은 마스터 서버와 독립적입니다.

복제본은 마스터 서버에서 해당 관리자 계정을 상속합니다. 마스터 서버의 모든 사용자 계정은 읽기 복제본으로 복제됩니다. 마스터 서버에서 사용 가능한 사용자 계정을 사용해야만 읽기 복제본에 연결할 수 있습니다.

MySQL 서버의 일반 Azure 데이터베이스에서와 마찬가지로 호스트 이름과 유효한 사용자 계정을 사용하여 복제본에 연결할 수 있습니다. 관리자 사용자 이름 **myadmin을**사용 하 여 **myreplica** 라는 서버의 경우 mysql CLI를 사용 하 여 복제본에 연결할 수 있습니다.

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

프롬프트가 표시되면 사용자 계정의 암호를 입력합니다.

## <a name="monitor-replication"></a>복제 모니터링

MySQL용 Azure 데이터베이스는 Azure **모니터에서 초 단위로 복제 지연을** 제공합니다. 이 메트릭은 복제본에만 사용할 수 있습니다.

이 메트릭은 MySQL `seconds_behind_master` `SHOW SLAVE STATUS` 명령에서 사용할 수 있는 메트릭을 사용하여 계산됩니다.

복제 지연이 워크로드에 허용되지 않는 값에 도달할 때 이를 알려주도록 경고를 설정합니다.

## <a name="stop-replication"></a>복제 중지

마스터 및 복제본 간의 복제를 중지할 수 있습니다. 마스터 서버와 읽기 복제본 사이에서 복제가 중지된 후에 복제본은 독립 실행형 서버가 됩니다. 독립 실행형 서버의 데이터는 복제 중지 명령이 시작될 때 복제본에서 사용할 수 있던 데이터입니다. 독립 실행형 서버는 마스터 서버를 따라잡지 않습니다.

복제본에 대한 복제를 중지하도록 선택하면 이전 마스터 및 다른 복제본에 대한 모든 링크가 손실됩니다. 마스터와 해당 복제본 사이에는 자동화된 장애 조치(failover)가 없습니다.

> [!IMPORTANT]
> 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.
> 읽기 복제본에서 복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인하십시오.

[복제본에 대한 복제를 중지](howto-read-replicas-portal.md)하는 방법을 알아봅니다.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

### <a name="pricing-tiers"></a>가격 책정 계층

읽기 복제본은 범용 및 메모리 최적화 가격 책정 계층에서만 사용할 수 있습니다.

### <a name="master-server-restart"></a>마스터 서버 다시 시작

기존 복제본이 없는 마스터에 대한 복제본을 만들 때 마스터가 먼저 다시 시작하여 복제를 준비합니다. 이 점을 고려하여 사용량이 많은 기간 동안 이러한 작업을 수행합니다.

### <a name="new-replicas"></a>새 복제본

읽기 복제본은 MySQL 서버에 대한 새 Azure 데이터베이스로 만들어집니다. 기존 서버를 복제본으로 만들 수 없습니다. 다른 읽기 복제본의 복제본은 만들 수 없습니다.

### <a name="replica-configuration"></a>복제본 구성

복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본을 만든 후에는 마스터 서버와는 별도로 컴퓨팅 생성, vCore, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 구성을 새 값으로 업데이트하기 전에 복제본의 구성을 같거나 더 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

방화벽 규칙 및 매개 변수 설정은 복제본을 만들 때 마스터 서버에서 복제본으로 상속됩니다. 이후에 복제본의 규칙은 독립적입니다.

### <a name="stopped-replicas"></a>중지된 복제본

마스터 서버와 읽기 복제본 간의 복제를 중지하면 중지된 복제본은 읽기와 쓰기를 모두 허용하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

### <a name="deleted-master-and-standalone-servers"></a>삭제된 마스터 및 독립 실행형 서버

마스터 서버가 삭제되면 모든 읽기 복제본에 대한 복제가 중지됩니다. 이러한 복제본은 자동으로 독립 실행형 서버가 되며 읽기와 쓰기를 모두 허용할 수 있습니다. 마스터 서버 자체는 삭제됩니다.

### <a name="user-accounts"></a>사용자 계정

마스터 서버의 사용자는 읽기 복제본으로 복제됩니다. 마스터 서버에서 사용 가능한 사용자 계정을 사용하여 읽기 복제본에만 연결할 수 있습니다.

### <a name="server-parameters"></a>서버 매개 변수

데이터가 동기화되지 않고 데이터가 손실 또는 손상될 가능성으로부터 데이터를 보호하기 위해 읽기 복제본을 사용하는 경우 일부 서버 매개 변수는 업데이트할 수 없도록 잠깁니다.

다음 서버 매개 변수는 마스터 서버와 복제본 서버 모두에서 잠겨 있습니다.
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

매개 [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) 변수는 복제본 서버에 잠겨 있습니다. 

### <a name="other"></a>기타

- GTID(전역 트랜잭션 식별자)는 지원되지 않습니다.
- 복제본의 복제본 만들기는 지원되지 않습니다.
- 메모리 내 테이블로 인해 복제본이 동기화되지 않을 수 있습니다. 이것은 MySQL 복제 기술의 제한 사항입니다. 자세한 내용은 [MySQL 참조 문서](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)를 참조하세요.
- 마스터 서버 테이블에 기본 키가 있는지 확인합니다. 기본 키가 없는 경우 마스터 및 복제본 간의 복제 대기 시간이 발생할 수 있습니다.
- [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)에서 MySQL 복제 제한 사항의 전체 목록을 검토하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 읽기 복제본 생성 및 관리](howto-read-replicas-portal.md) 방법 알아보기
- [Azure CLI 및 REST API를 사용하여 읽기 복제본을 만들고 관리하는](howto-read-replicas-cli.md) 방법에 대해 알아봅니다.
