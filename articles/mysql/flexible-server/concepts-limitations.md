---
title: 제한 Azure Database for MySQL-유연한 서버
description: 이 문서에서는 연결 수 및 저장소 엔진 옵션과 같은 Azure Database for MySQL 유연한 서버의 제한 사항을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 6fff7f22e7d265eb9b15bcec8604eeab692ac1c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650292"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 유연한 서버 (미리 보기)의 제한 사항

> [!IMPORTANT] 
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure Database for MySQL 유연한 서버 서비스의 제한 사항을 설명 합니다. MySQL 데이터베이스 엔진의 [일반적인 제한](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) 사항도 적용 됩니다. 리소스 (계산, 메모리, 저장소) 계층에 대해 알아보려면 [계산 및 저장소](concepts-compute-storage.md) 문서를 참조 하세요.

## <a name="server-parameters"></a>서버 매개 변수

> [!NOTE]
> 및와 같은 서버 매개 변수에 대 한 최소/최대 값을 찾으려는 경우 `max_connections` `innodb_buffer_pool_size` 이 정보가 서버 매개 변수 개념으로 이동 되었습니다. <!-- **[server parameters](./concepts-server-parameters.md)** --> 문서를 참조하세요.

Azure Database for MySQL 서버 매개 변수 값의 튜닝을 지원 합니다. 일부 매개 변수의 min 및 max 값 (예: `max_connections`, `join_buffer_size` , `query_cache_size` )는 서버의 계산 계층 및 계산 크기에 따라 결정 됩니다. 서버 매개 변수 개념 참조 <!-- [server parameters](./concepts-server-parameters.md)--> 이러한 제한에 대 한 자세한 내용은을 (를).

"Validate_password" 및 "caching_sha2_password"와 같은 암호 플러그 인은 서비스에서 지원 되지 않습니다.

## <a name="storage-engines"></a>저장소 엔진

MySQL은 많은 저장소 엔진을 지원 합니다. Azure Database for MySQL 유연한 서버에서 지원 되 고 지원 되지 않는 저장소 엔진은 다음과 같습니다.

### <a name="supported"></a>지원됨
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>지원되지 않음
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>데이터 조작 지원 & 권한

많은 서버 매개 변수 및 설정이 실수로 서버 성능을 저하 시키거나 MySQL 서버의 ACID 속성을 무시할 수 있습니다. 제품 수준에서 서비스 무결성 및 SLA를 유지 하기 위해이 서비스는 여러 역할을 노출 하지 않습니다. 

MySQL 서비스는 기본 파일 시스템에 대 한 직접 액세스를 허용 하지 않습니다. 일부 데이터 조작 명령은 지원 되지 않습니다. 

### <a name="unsupported"></a>지원되지 않음

지원 되지 않는 기능은 다음과 같습니다.
- DBA 역할: 제한 됨 또는 새 서버를 만드는 동안 만들어진 관리자 사용자를 사용 하 여 대부분의 DDL 및 DML 문을 수행할 수 있습니다. 
- SUPER 권한: 마찬가지로 [슈퍼 권한도](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) 제한 됩니다.
- DEFINER: 생성하려면 SUPER 권한이 필요하며, 제한됩니다. 백업을 사용하여 데이터를 가져올 경우 mysqldump를 수행할 때 수동으로 또는 `--skip-definer` 명령을 사용하여 `CREATE DEFINER` 명령을 제거하세요.
- 시스템 데이터베이스: [mysql 시스템 데이터베이스](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) 는 읽기 전용 이며 다양 한 PaaS 기능을 지 원하는 데 사용 됩니다. 시스템 데이터베이스를 변경할 수 없습니다 `mysql` .
- `SELECT ... INTO OUTFILE`: 서비스에서 지원 되지 않습니다.

### <a name="supported"></a>지원됨
- `LOAD DATA INFILE`은 지원되지만 `[LOCAL]` 매개 변수를 지정하고 UNC 경로(SMB를 통해 탑재된 Azure Storage)로 전달해야 합니다.

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="zone-redundant-ha"></a>영역 중복 HA
- 이 구성은 서버를 만드는 동안에만 설정할 수 있습니다.
- 삼 간에 안정화 되는 계산 계층에서는 지원 되지 않습니다.

### <a name="networking"></a>네트워킹
- 서버를 만든 후에는 연결 방법을 변경할 수 없습니다. 서버를 *개인 액세스 (VNet 통합)* 를 사용 하 여 만든 경우에는 만든 후 *공용 액세스 (허용 된 IP 주소)* 로 변경할 수 없으며 그 반대의 경우도 마찬가지입니다.
- TLS/SSL은 기본적으로 사용 하도록 설정 되어 있으며 사용 하지 않도록 설정할 수 없습니다.
- 서버에서 지원 되는 최소 TLS 버전은 TLS 1.2입니다. 자세한 내용은 [TLS/SSL을 사용 하 여 연결](./how-to-connect-tls-ssl.md) 을 참조 하세요.

### <a name="stopstart-operation"></a>작업 중지/시작
- 영역 중복 HA 구성 (주 및 대기 모두)에서는 지원 되지 않습니다.
- 읽기 복제본 구성 (원본 및 복제본 모두)에서 지원 되지 않습니다.

### <a name="scale-operations"></a>크기 조정 작업
- 프로 비전 된 서버 저장소를 줄이는 것은 지원 되지 않습니다.

### <a name="read-replicas"></a>읽기 복제본
- 영역 중복 HA 구성 (주 및 대기 모두)에서는 지원 되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간의 자동화 된 마이그레이션은 지원 되지 않습니다. 주 버전을 업그레이드 하려면 덤프를 사용 하 여 새 엔진 버전으로 만든 서버에 [복원](../concepts-migrate-dump-restore.md) 합니다.

### <a name="restoring-a-server"></a>서버 복원
- 지정 시간 복원을 사용 하 여 새 서버는 기반으로 하는 원본 서버와 동일한 계산 및 저장소 구성을 사용 하 여 만들어집니다. 서버를 만든 후 새로 복원 된 서버의 계산을 축소할 수 있습니다.
- 삭제 된 서버 복원은 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [계산 및 저장소 옵션에 사용할 수 있는 기능](concepts-compute-storage.md) 이해
- [지원 되는 MySQL 버전](concepts-supported-versions.md) 에 대 한 자세한 정보
- [Azure Portal를 사용 하 여 서버를 백업 및 복원 하는 방법](how-to-restore-server-portal.md) 검토