---
title: Azure Database for MariaDB의 복제본 읽기
description: Azure Database for MariaDB에서 읽기 복제본에 대해 알아봅니다. 영역 선택, 복제본 만들기, 복제본에 연결, 복제 모니터링 및 복제 중지에 대해 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 29725c302887448689f4aafd86f1f834d81c23ed
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973588"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 복제본 읽기

복제본 읽기 기능을 사용 하면 Azure Database for MariaDB 서버에서 읽기 전용 서버로 데이터를 복제할 수 있습니다. 최대 5개의 복제본으로 마스터 서버에서 복제할 수 있습니다. 복제본은 GTID (전역 트랜잭션 ID)를 사용 하 여 MariaDB 엔진의 이진 로그 파일 위치 기반 복제 기술을 사용 하 여 비동기적으로 업데이트 됩니다. binlog 복제에 대해 자세히 알아보려면 [binlog 복제 개요](https://mariadb.com/kb/en/library/replication-overview/)를 참조하세요.

복제본은 일반 Azure Database for MariaDB 서버와 유사 하 게 관리 되는 새 서버입니다. 읽기 복제본의 경우, vCore 및 스토리지에 프로비저닝된 컴퓨팅에 대한 비용이 GB/월 단위로 청구됩니다.

GTID 복제에 대 한 자세한 내용은 [Mariadb 복제 설명서](https://mariadb.com/kb/en/library/gtid/)를 참조 하세요.

## <a name="when-to-use-a-read-replica"></a>읽기 복제본을 사용하는 경우

읽기 복제본 기능은 읽기 작업이 많은 워크로드의 성능과 규모를 향상시키는 데 도움이 됩니다. 읽기 워크로드를 복제본과 격리할 수 있고 쓰기 워크로드를 마스터로 보낼 수 있습니다.

일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.

복제본은 읽기 전용이기 때문에 마스터에 대한 쓰기 용량 부담을 직접적으로 줄이지는 못합니다. 이 기능은 쓰기 작업이 많은 워크로드를 대상으로 하지 않습니다.

복제본 읽기 기능은 비동기 복제를 사용 합니다. 이 기능은 동기식 복제 시나리오를 위한 것이 아닙니다. 마스터와 복제본 간에는 측정 가능한 지연이 발생합니다. 복제본의 데이터는 결과적으로 마스터의 데이터와 일치하게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다.


## <a name="cross-region-replication"></a>지역 간 복제
마스터 서버에서 다른 지역에 읽기 복제본을 만들 수 있습니다. 지역 간 복제는 재해 복구 계획 또는 사용자에 게 더 가까운 데이터 가져오기 등의 시나리오에 유용할 수 있습니다.

> [!NOTE]
> 지역 간 복제는 미리 보기 상태입니다.

[Azure Database for MariaDB 지역](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)에 마스터 서버를 둘 수 있습니다.  마스터 서버는 쌍을 이루는 지역 또는 유니버설 복제본 지역에 복제본이 있을 수 있습니다. 아래 그림은 마스터 영역에 따라 사용할 수 있는 복제본 영역을 보여 줍니다.

[![ 복제본 영역 읽기](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>유니버설 복제본 영역
마스터 서버가 있는 위치에 관계 없이 다음 지역에서 읽기 복제본을 만들 수 있습니다. 지원 되는 범용 복제본 영역에는 다음이 포함 됩니다.

오스트레일리아 동부, 오스트레일리아 남동쪽, 미국 중부, 동아시아, 미국 동부, 미국 동부 2, 일본 동부, 일본 서 부, 대한민국 중부, 한국 남부, 미국 중 북부, 북부 유럽, 미국 동부, 동남 아시아, 영국 남부, 영국 서부, 유럽 서부, 미국 서 부, 미국 서 부 2.


### <a name="paired-regions"></a>쌍을 이루는 지역
유니버설 복제본 영역 외에도 마스터 서버의 Azure 쌍을 이루는 지역에서 읽기 복제본을 만들 수 있습니다. 지역 쌍을 모를 경우 [Azure 쌍을 이루는 지역 문서](../best-practices-availability-paired-regions.md)에서 자세히 알아볼 수 있습니다.

재해 복구 계획에 지역 간 복제본을 사용 하는 경우 다른 지역 중 하나가 아닌 쌍을 이루는 지역에 복제본을 만드는 것이 좋습니다. 쌍을 이루는 지역은 동시 업데이트를 방지 하 고 물리적 격리 및 데이터 상주의 우선 순위를 지정 합니다.  

그러나 고려해 야 할 제한 사항이 있습니다. 

* 사용 가능한 지역: Azure Database for MariaDB는 미국 서 부 2, 프랑스 중부, 아랍에미리트 북부 및 독일 중부에서 사용할 수 있습니다. 그러나 쌍을 이루는 지역에는 사용할 수 없습니다.
    
* 단방향 쌍: 일부 Azure 지역은 한 방향 으로만 쌍으로 연결 됩니다. 이러한 지역에는 인도 서 부, 브라질 남부 및 US Gov 버지니아 포함 됩니다. 
   즉, 인도 서 부의 마스터 서버는 남부 인도에서 복제본을 만들 수 있습니다. 그러나 인도 남부의 마스터 서버는 인도 서 부에서 복제본을 만들 수 없습니다. 이는 인도 서 부의 보조 지역이 남부 인도 이지만 남부 인도의 보조 지역은 인도 서 부가 아니기 때문입니다.


## <a name="create-a-replica"></a>복제본 만들기

마스터 서버에 기존 복제본 서버가 없는 경우 마스터 복제를 준비하기 위해 마스터가 먼저 다시 시작됩니다.

복제본 만들기 워크플로를 시작 하면 빈 Azure Database for MariaDB 서버가 만들어집니다. 새 서버는 마스터 서버에 있는 데이터로 채워집니다. 생성 시간은 마스터의 데이터 양과 지난 주 전체 백업 이후의 시간에 따라 달라집니다. 시간은 몇 분에서 몇 시간까지 걸릴 수 있습니다.

> [!NOTE]
> 서버에 스토리지 경고가 설정되어 있지 않은 경우 스토리지 경고를 설정하는 것이 좋습니다. 이 경고는 서버가 스토리지 용량 한도에 도달하면 이를 알려주며 복제에 영향을 미칩니다.

[Azure Portal에서 읽기 복제본을 만드는 방법](howto-read-replicas-portal.md)을 알아봅니다.

## <a name="connect-to-a-replica"></a>복제본에 연결

복제본을 만들면 마스터 서버의 방화벽 규칙 또는 VNet 서비스 엔드포인트를 상속하지 않습니다. 이러한 규칙은 복제본에 대해 별도로 설정해야 합니다.

복제본은 마스터 서버에서 해당 관리자 계정을 상속합니다. 마스터 서버의 모든 사용자 계정은 읽기 복제본으로 복제됩니다. 마스터 서버에서 사용 가능한 사용자 계정을 사용해야만 읽기 복제본에 연결할 수 있습니다.

일반 Azure Database for MariaDB 서버에서와 같이 호스트 이름 및 유효한 사용자 계정을 사용 하 여 복제본에 연결할 수 있습니다. 관리자 사용자 이름 **myadmin**을 사용한 **myreplica**라는 이름의 서버의 경우, mysql CLI를 사용하여 복제본에 연결할 수 있습니다.

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

프롬프트가 표시되면 사용자 계정의 암호를 입력합니다.

## <a name="monitor-replication"></a>복제 모니터링

Azure Database for MariaDB은 Azure Monitor에서 **복제 지연 시간 (초)** 을 제공 합니다. 이 메트릭은 복제본에만 사용할 수 있습니다.

이 메트릭은 MariaDB의 `SHOW SLAVE STATUS` 명령에서 사용할 수 있는 `seconds_behind_master` 메트릭을 사용 하 여 계산 됩니다.

복제 지연 시간이 작업에 적합 하지 않은 값에 도달 하는 경우 사용자에 게 알리는 경고를 설정 합니다.

## <a name="stop-replication"></a>복제 중지

마스터 및 복제본 간의 복제를 중지할 수 있습니다. 마스터 서버와 읽기 복제본 사이에서 복제가 중지된 후에 복제본은 독립 실행형 서버가 됩니다. 독립 실행형 서버의 데이터는 복제 중지 명령이 시작될 때 복제본에서 사용할 수 있던 데이터입니다. 독립 실행형 서버는 마스터 서버를 따라잡지 않습니다.

복제본에 복제를 중지하려는 경우 이전 마스터 및 다른 복제본에 대한 모든 링크가 손실됩니다. 마스터 및 해당 복제본 간의 자동 장애 조치는 없습니다.

> [!IMPORTANT]
> 독립 실행형 서버는 다시 복제본으로 만들 수 없습니다.
> 읽기 복제본에서 복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인하십시오.

[복제본에 대한 복제를 중지](howto-read-replicas-portal.md)하는 방법을 알아봅니다.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

### <a name="pricing-tiers"></a>가격 책정 계층

읽기 복제본은 범용 및 메모리 최적화 가격 책정 계층에서만 사용할 수 있습니다.

### <a name="master-server-restart"></a>마스터 서버 다시 시작

기존 복제본이 없는 마스터에 대한 복제본을 만들 때 마스터 복제를 준비하기 위해 마스터가 먼저 다시 시작됩니다. 이를 고려 하 여 사용량이 많지 않은 기간 동안 이러한 작업을 수행 합니다.

### <a name="new-replicas"></a>새 복제본

읽기 복제본은 새 Azure Database for MariaDB 서버로 생성 됩니다. 기존 서버를 복제본으로 만들 수 없습니다. 다른 읽기 복제본의 복제본은 만들 수 없습니다.

### <a name="replica-configuration"></a>복제본 구성

복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본을 만든 후에는 계산 세대, vCores, 저장소, 백업 보존 기간 및 MariaDB 엔진 버전 등의 몇 가지 설정을 마스터 서버와 독립적으로 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 구성을 새 값으로 업데이트하기 전에 복제본의 구성을 같거나 더 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

### <a name="stopped-replicas"></a>중지된 복제본

마스터 서버와 읽기 복제본 간의 복제를 중지한 경우 중지된 복제본은 읽기 및 쓰기를 허용하는 독립 실행형 서버가 됩니다. 독립 실행형 서버는 다시 복제본으로 만들 수 없습니다.

### <a name="deleted-master-and-standalone-servers"></a>삭제된 마스터 및 독립 실행형 서버

마스터 서버가 삭제되면 모든 읽기 복제본에 대한 복제가 중지됩니다. 이러한 복제본은 독립형 서버가 됩니다. 마스터 서버 자체는 삭제됩니다.

### <a name="user-accounts"></a>사용자 계정

마스터 서버의 사용자는 읽기 복제본으로 복제됩니다. 마스터 서버에서 사용 가능한 사용자 계정을 사용하여 읽기 복제본에만 연결할 수 있습니다.

### <a name="server-parameters"></a>서버 매개 변수

데이터 동기화가 되지 않는 것을 방지하고 잠재적 데이터 손실이나 손상을 방지하기 위해, 일부 서버 매개 변수는 복제본을 읽을 때 업데이트로부터 잠겨 있습니다.

다음 서버 매개 변수가 마스터와 복제본 서버에서 잠깁니다.
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[ `event_scheduler` ](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) 매개 변수는 복제본 서버에서 잠겨 있습니다.

### <a name="other"></a>기타

- 복제본의 복제본 만들기는 지원되지 않습니다.
- 메모리 내 테이블이 있으면 복제본이 동기화되지 않을 수 있기 때문입니다. 이는 MariaDB 복제 기술의 제한 사항입니다.
- 마스터 서버 테이블에 기본 키가 있는지 확인합니다. 기본 키가 없는 경우 마스터 및 복제본 간의 복제 대기 시간이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 읽기 복제본 생성 및 관리](howto-read-replicas-portal.md) 방법 알아보기
- [Azure CLI 및 REST API를 사용 하 여 읽기 복제본을 만들고 관리](howto-read-replicas-cli.md) 하는 방법을 알아봅니다.