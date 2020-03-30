---
title: 데이터 인 복제 - MySQL용 Azure 데이터베이스
description: 데이터 입력 복제를 사용하여 외부 서버에서 MySQL 서비스에 대한 Azure 데이터베이스로 동기화하는 방법에 대해 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533235"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Azure Database for MySQL에 데이터를 복제합니다.

입력 데이터 복제를 사용하면 외부 MySQL 서버에서 Azure Database for MySQL 서비스로 데이터를 동기화할 수 있습니다. 외부 서버는 온-프레미스 가상 머신 또는 다른 클라우드 공급 기업이 호스트하는 데이터베이스 서비스에 있을 수 있습니다. 내부 데이터 복제는 MySQL에 네이티브인 이진 로그(binlog) 파일 위치 기반 복제를 기반으로 합니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다. 

## <a name="when-to-use-data-in-replication"></a>데이터 내부 복제를 사용하는 경우
데이터 내부 복제 사용을 고려할 주요 시나리오는 다음과 같습니다.

- **하이브리드 데이터 동기화:** 데이터 내부 복제를 사용하면 온-프레미스 서버와 Azure Databases for MySQL 간에 데이터를 동기화할 수 있습니다. 이 동기화는 하이브리드 애플리케이션을 만드는 데 유용합니다. 이 메서드는 기존 로컬 데이터베이스 서버가 있지만 최종 사용자에게 더 가까운 지역으로 데이터를 이동하려는 경우 매력적입니다.
- **다중 클라우드 동기화:** 복잡한 클라우드 솔루션의 경우 데이터 내부 복제를 사용하여 해당 클라우드에 호스팅된 데이터베이스 서비스 및 가상 머신을 포함하여 Azure Database for MySQL과 다른 클라우드 공급자 간에 데이터를 동기화합니다.
 
마이그레이션 시나리오의 경우 [Azure 데이터베이스 마이그레이션 서비스(DMS)를](https://azure.microsoft.com/services/database-migration/)사용합니다.

## <a name="limitations-and-considerations"></a>제한 사항 및 고려 사항

### <a name="data-not-replicated"></a>데이터가 복제되지 않음
마스터 서버의 [*mysql 시스템 데이터베이스*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)는 복제되지 않습니다. 마스터 서버에서 계정 및 사용 권한에 대한 변경 내용은 복제되지 않습니다. 마스터 서버에서 계정을 만들고 이 계정으로 복제 서버에 액세스해야 하는 경우 복제 서버 쪽에서 동일한 계정을 수동으로 만듭니다. 시스템 데이터베이스에 포함된 테이블을 이해하려면 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)를 참조합니다.

### <a name="requirements"></a>요구 사항
- 마스터 서버 버전은 MySQL 버전 5.6 이상이어야 합니다. 
- 마스터 서버 및 복제 서버 버전은 동일해야 합니다. 예를 들어 둘 다 MySQL 버전 5.6이거나 둘 다 MySQL 버전 5.7이어야 합니다.
- 각 표에는 기본 키가 있어야 합니다.
- 마스터 서버는 MySQL InnoDB 엔진을 사용해야 합니다.
- 사용자는 이진 로깅을 구성하고 마스터 서버에서 새 사용자를 만들 수 있는 권한이 있어야 합니다.
- 마스터 서버에 SSL이 활성화되어 있는 경우 도메인에 대해 제공된 SSL `mysql.az_replication_change_master` CA 인증서가 저장 프로시저에 포함되어 있는지 확인합니다. 다음 [예제](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) 및 매개 `master_ssl_ca` 변수를 참조하십시오.
- 마스터 서버의 IP 주소가 Azure Database for MySQL 복제본 서버의 방화벽 규칙에 추가되었는지 확인합니다. [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) 또는 [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli)를 사용하여 방화벽 규칙을 업데이트합니다.
- 마스터 서버를 호스트하는 컴퓨터에서 포트 3306에 대한 인바운드 및 아웃바운드 트래픽을 둘 다 허용하는지 확인합니다.
- 마스터 서버에 **공용 IP 주소가**있는지, DNS에 공개적으로 액세스할 수 있는지, 또는 정규화된 도메인 이름(FQDN)이 있는지 확인합니다.

### <a name="other"></a>기타
- 입력 데이터 복제는 범용 및 메모리 최적화 가격 책정 계층에서만 지원됩니다.
- GTID(전역 트랜잭션 식별자)는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [데이터 인 복제를 설정하는](howto-data-in-replication.md) 방법 알아보기
- [일기 복제본을 사용하여 Azure에서 복제](concepts-read-replicas.md)에 대해 알아보기
- [DMS를 사용하여 가동 중지 시간을 최소화하면서 데이터를 마이그레이션하는](howto-migrate-online.md) 방법에 대해 알아봅니다.