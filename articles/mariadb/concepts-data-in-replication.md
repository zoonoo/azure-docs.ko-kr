---
title: Azure Database for MariaDB에 데이터 복제
description: 데이터에서 복제를 사용 하 여 외부 서버에서 Azure Database for MariaDB 서비스로 동기화 하는 방법에 대해 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 826a6db289bf8b938e85d270f91836b3d8790206
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973647"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Azure Database for MariaDB에 데이터 복제

입력 데이터 복제를 사용하면 다른 클라우드 공급자가 호스트하는 데이터베이스 서비스 또는 가상 머신의 온-프레미스에서 실행하는 MariaDB 서버에서 MariaDB 서비스에 대한 Azure 데이터베이스로 데이터를 동기화할 수 있습니다. 입력 데이터 복제는 MariaDB에 네이티브인 이진 로그(binlog) 파일 위치 기반 복제를 기반으로 합니다. binlog 복제에 대해 자세히 알아보려면 [binlog 복제 개요](https://mariadb.com/kb/en/library/replication-overview/)를 참조하세요.

## <a name="when-to-use-data-in-replication"></a>데이터 내부 복제를 사용하는 경우
데이터 내부 복제 사용을 고려할 주요 시나리오는 다음과 같습니다.

- **하이브리드 데이터 동기화:** 입력 데이터 복제를 사용하면 온-프레미스 서버와 Azure Database for MariaDB 간에 데이터를 동기화할 수 있습니다. 이 동기화는 하이브리드 애플리케이션을 만드는 데 유용합니다. 이 메서드는 기존 로컬 데이터베이스 서버가 있지만 최종 사용자에게 더 가까운 지역으로 데이터를 이동하려는 경우 매력적입니다.
- **다중 클라우드 동기화:** 복잡한 클라우드 솔루션의 경우 입력 데이터 복제를 사용하여 해당 클라우드에 호스트된 데이터베이스 서비스 및 가상 머신을 포함하여 Azure Database for MariaDB와 다른 클라우드 공급자 간에 데이터를 동기화합니다.

## <a name="limitations-and-considerations"></a>제한 사항 및 고려 사항

### <a name="data-not-replicated"></a>데이터가 복제되지 않음
마스터 서버의 [*mysql 시스템 데이터베이스*](https://mariadb.com/kb/en/library/the-mysql-database-tables/)는 복제되지 않습니다. 마스터 서버에서 계정 및 사용 권한에 대한 변경 내용은 복제되지 않습니다. 마스터 서버에서 계정을 만들고 이 계정으로 복제 서버에 액세스해야 하는 경우 복제 서버 쪽에서 동일한 계정을 수동으로 만듭니다. 시스템 데이터베이스에 어떤 테이블이 포함되는지 이해하려면 [MariaDB 설명서](https://mariadb.com/kb/en/library/the-mysql-database-tables/)를 참조하세요.

### <a name="requirements"></a>요구 사항
- 마스터 서버 버전은 MariaDB 버전 10.2 이상이어야 합니다.
- 마스터 서버 및 복제 서버 버전은 동일해야 합니다. 예를 들어 둘 다 MariaDB 10.2 버전이어야 합니다.
- 각 표에는 기본 키가 있어야 합니다.
- 마스터 서버는 InnoDB 엔진을 사용해야 합니다.
- 사용자는 이진 로깅을 구성하고 마스터 서버에서 새 사용자를 만들 수 있는 권한이 있어야 합니다.
- 마스터 서버에서 SSL을 사용 하도록 설정한 경우 도메인에 제공 된 SSL CA 인증서가 `mariadb.az_replication_change_master` 저장 프로시저에 포함 되어 있는지 확인 합니다. 다음 [예제](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) 와 `master_ssl_ca` 매개 변수를 참조 하세요.
- 마스터 서버의 IP 주소가 Azure Database for MariaDB 복제본 서버의 방화벽 규칙에 추가되었는지 확인합니다. [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) 또는 [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli)를 사용하여 방화벽 규칙을 업데이트합니다.
- 마스터 서버를 호스트하는 컴퓨터에서 포트 3306에 대한 인바운드 및 아웃바운드 트래픽을 둘 다 허용하는지 확인합니다.
- 마스터 서버에 **공용 IP 주소가**있거나, DNS에 공개적으로 액세스할 수 있거나, FQDN (정규화 된 도메인 이름)이 있는지 확인 합니다.

### <a name="other"></a>기타
- 입력 데이터 복제는 범용 및 메모리 최적화 가격 책정 계층에서만 지원됩니다.

## <a name="next-steps"></a>다음 단계
- [입력 데이터 복제 설정](howto-data-in-replication.md) 방법을 알아보세요.
