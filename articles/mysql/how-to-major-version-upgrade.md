---
title: Azure Database for MySQL의 주 버전 업그레이드-단일 서버
description: 이 문서에서는 Azure Database for MySQL 단일 서버에 대 한 주 버전을 업그레이드할 수 있는 방법을 설명 합니다.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: ea2dc877c7bc6db387985e7b5cd1153e195ab4f1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509573"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>단일 서버의 주 버전 업그레이드 Azure Database for MySQL

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용 하지 않는 용어 _종속_ 용어에 대 한 참조가 포함 되어 있습니다. 소프트웨어에서 용어를 제거 하는 경우이 문서에서 제거 합니다.
>

> [!IMPORTANT]
> Azure database for MySQL 단일 서버에 대 한 주 버전 업그레이드는 공개 미리 보기 상태입니다.

이 문서에서는 Azure Database for MySQL 단일 서버에서 MySQL 주 버전을 현재 위치로 업그레이드 하는 방법을 설명 합니다.

이 기능을 통해 고객은 데이터 이동 이나 응용 프로그램 연결 문자열 변경 없이 단추를 클릭 하 여 MySQL 5.6 서버의 전체 업그레이드를 MySQL 5.7로 수행할 수 있습니다.

> [!Note]
> * 주 버전 업그레이드는 MySQL 5.6에서 MySQL 5.7로의 주 버전 업그레이드에만 사용할 수 있습니다.
> * 업그레이드 작업을 수행 하는 동안에는 서버를 사용할 수 없습니다. 따라서 계획 된 유지 관리 기간 동안에는 업그레이드를 수행 하는 것이 좋습니다. [읽기 복제본을 사용 하 여 mysql 5.6에서 mysql 5.7로 최소 가동 중지 시간 주 버전 업그레이드를 수행할](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas) 수 있습니다.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Azure Portal를 사용 하 여 MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

Azure Portal를 사용 하 여 Azure Database의 MySQL 5.6 서버에 대 한 주 버전 업그레이드를 수행 하려면 다음 단계를 따르세요.

> [!IMPORTANT]
> 프로덕션을 직접 업그레이드 하는 대신 복원 된 서버 복사본에서 업그레이드를 먼저 수행 하는 것이 좋습니다. 지정 [시간 복원을 수행 하는 방법을](howto-restore-server-portal.md#point-in-time-restore)참조 하세요.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6 서버를 선택 합니다.

2. **개요** 페이지의 도구 모음에서 **업그레이드** 단추를 클릭 합니다.

3. **업그레이드** 섹션에서 **확인** 을 선택 하 여 Azure database for MySQL 5.6 서버를 5.7 서버로 업그레이드 합니다.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-개요-업그레이드":::

4. 알림이 성공적으로 업그레이드 되었는지 확인 합니다.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Azure CLI를 사용 하 여 MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

Azure CLI를 사용 하 여 Azure Database의 MySQL 5.6 서버에 대 한 주 버전 업그레이드를 수행 하려면 다음 단계를 따르세요.

> [!IMPORTANT]
> 프로덕션을 직접 업그레이드 하는 대신 복원 된 서버 복사본에서 업그레이드를 먼저 수행 하는 것이 좋습니다. 지정 [시간 복원을 수행 하는 방법을](howto-restore-server-cli.md#server-point-in-time-restore)참조 하세요.

1. [Windows 용 Azure CLI](/cli/azure/install-azure-cli) 를 설치 하거나 [Azure Cloud Shell](../cloud-shell/overview.md) 의 Azure CLI를 사용 하 여 업그레이드 명령을 실행 합니다. 
 
   이 업그레이드를 수행 하려면 버전 2.16.0 이상의 Azure CLI 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. az version을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 az upgrade를 실행합니다.

2. 로그인 한 후 [az mysql server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) 명령을 실행 합니다.

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   명령 프롬프트에 "실행 중" 메시지가 표시 됩니다. 이 메시지가 더 이상 표시 되지 않으면 버전 업그레이드가 완료 된 것입니다.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Azure Portal를 사용 하 여 읽기 복제본에서 MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6 읽기 복제 서버를 선택 합니다.

2. **개요** 페이지의 도구 모음에서 **업그레이드** 단추를 클릭 합니다.

3. **업그레이드** 섹션에서 **확인** 을 선택 하 여 Azure database for MySQL 5.6 읽기 복제본 서버를 5.7 서버로 업그레이드 합니다.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-개요-업그레이드":::

4. 알림이 성공적으로 업그레이드 되었는지 확인 합니다.

5. **개요** 페이지에서 Azure Database for MySQL 읽기 복제본 서버 버전이 5.7 인지 확인 합니다.

6. 이제 주 서버로 이동 하 여 해당 서버에서 [주 버전 업그레이드를 수행](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) 합니다.

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>읽기 복제본을 사용 하 여 MySQL 5.6에서 MySQL 5.7로 최소 가동 중지 시간 주 버전 업그레이드 수행

읽기 복제본을 활용 하 여 MySQL 5.6에서 MySQL 5.7로 최소 가동 중지 주 버전 업그레이드를 수행할 수 있습니다. 서버에 대 한 읽기 복제본을 5.7로 업그레이드 한 후 나중에 응용 프로그램을 장애 조치 하 여 복제본 읽기를 가리키고 새 주 복제본으로 설정 하는 것이 가장 좋습니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6를 선택 합니다.

2. 주 서버에서 [읽기 복제본](https://docs.microsoft.com/azure/mysql/concepts-read-replicas#create-a-replica) 을 만듭니다.

3. [읽기 복제본](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) 을 버전 5.7으로 업그레이드 합니다.

4. 복제본 서버가 버전 5.7에서 실행 되 고 있는지 확인 한 후에는 응용 프로그램이 주 서버에 연결 되지 않도록 합니다.
 
5. 복제 상태를 확인 하 고 모든 데이터가 동기화 되도록 복제본이 모두 주 복제본 인지 확인 하 고 주 복제본에서 수행 되는 새 작업이 없는지 확인 합니다.

   [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html)복제 서버에서 명령을 호출 하 여 복제 상태를 확인 합니다.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   및의 상태가 `Slave_IO_Running` `Slave_SQL_Running` "yes"이 고 값 `Seconds_Behind_Master` 이 "0" 이면 복제가 정상적으로 작동 합니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 "0"이 아니면 복제본이 업데이트를 처리 하 고 있음을 의미 합니다. `Seconds_Behind_Master`"0"이 확인 되 면 복제를 중지 하는 것이 안전 합니다.

6. [복제를 중지](https://docs.microsoft.com/azure/mysql/howto-read-replicas-portal#stop-replication-to-a-replica-server)하 여 읽기 복제본을 주 복제본으로 승격 합니다.

7. 응용 프로그램이 서버 5.7를 실행 하는 새로운 주 (이전 복제본)를 가리키도록 합니다. 각 서버에는 고유한 연결 문자열이 있습니다. 원본 대신 (이전) 복제본을 가리키도록 응용 프로그램을 업데이트 합니다.

> [!Note]
> 이 시나리오는 4, 5 및 6 단계 에서만 가동 중지 시간이 발생 합니다.


## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>업그레이드 해야 하는 프로덕션 환경에 MySQL v 5.6이 있으므로이 업그레이드 기능이 GA 될 예정 인가요?

이 기능의 GA는 MySQL v 5.6 사용 중지 전에 계획 됩니다. 그러나이 기능은 프로덕션 준비가 되었으며 Azure에서 완전히 지원 되므로 사용자 환경에서 자신 있게 실행 해야 합니다. 권장 되는 모범 사례로, 업그레이드 하는 동안 가동 중지 시간을 예측 하 고 프로덕션 환경에서 실행 하기 전에 응용 프로그램 호환성 테스트를 수행할 수 있도록 서버의 복원 된 복사본에서 먼저 실행 하 고 테스트 하는 것이 좋습니다. 자세한 내용은 지정 시간 [복원을 수행](howto-restore-server-portal.md#point-in-time-restore) 하 여 서버의 특정 시점 복사본을 만드는 방법을 참조 하세요. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>이로 인해 서버의 가동 중지 시간이 발생 하는 경우 시간이 오래 걸릴 수 있습니다.

예, 업그레이드 프로세스 중에는 서버를 사용할 수 없으므로 계획 된 유지 관리 기간 동안이 작업을 수행 하는 것이 좋습니다. 예상 되는 가동 중지 시간은 데이터베이스 크기, 프로 비전 된 저장소 크기 (IOPs 프로 비전 됨) 및 데이터베이스의 테이블 수에 따라 달라 집니다. 업그레이드 시간은 서버의 테이블 수에 직접적으로 비례 합니다. 기본 SKU 서버 업그레이드는 표준 저장소 플랫폼에 비해 시간이 더 오래 걸릴 것으로 예상 됩니다. 서버 환경의 가동 중지 시간을 예측 하려면 먼저 복원 된 서버 복사본에서 업그레이드를 수행 하는 것이 좋습니다. [읽기 복제본을 사용 하 여 mysql 5.6에서 mysql 5.7로 최소 가동 중지 시간을 최소화](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas) 하는 것이 좋습니다.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>2021 년 2 월 5 일 이전에 MySQL v 5.6 서버를 업그레이드 하도록 선택 하지 않으면 어떻게 되나요?

계속 하기 전에 MySQL v 5.6 서버를 계속 실행할 수 있습니다. Azure는 서버에서 강제 업그레이드 **를 수행 하지 않습니다** . 그러나 [Azure Database for MySQL 버전 관리 정책](concepts-version-policy.md) 에 설명 된 제한 사항이 적용 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Database for MySQL 버전 관리 정책](concepts-version-policy.md)에 대해 알아봅니다.