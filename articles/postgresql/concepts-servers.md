---
title: Azure Database for PostgreSQL-단일 서버에서에서 서버 개념
description: 이 문서에서는 고려 사항 및 구성 하 고 Azure Database for PostgreSQL-단일 서버 관리에 대 한 지침을 제공 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: bc135e58d0fbabc809f3718915e9f4e35b8ed875
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067151"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-단일 서버
이 문서에서는 Azure Database for PostgreSQL-단일 서버 작업에 대 한 고려 사항 및 지침을 제공 합니다.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure 데이터베이스 서버란?
Azure Database for PostgreSQL-단일 서버 배포 옵션의에서 서버는 여러 데이터베이스에 대 한 중앙의 관리 지점. 온-프레미스 환경에서도 익숙할 수 있는 동일한 PostgreSQL 서버 구문입니다. 특히, PostgreSQL 서비스는 관리되며, 성능 보장을 제공하고, 서버 수준에서 액세스 권한 및 기능을 노출합니다.

PostgreSQL용 Azure 데이터베이스 서버:

- Azure 구독 내에서 만들어집니다.
- 데이터베이스에 대한 부모 리소스입니다.
- 데이터베이스에 대한 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 컨테이너로서 서버를 삭제하고 포함된 데이터베이스를 삭제합니다.
- 하위 지역에 리소스를 배치합니다.
- 서버 및 데이터베이스 액세스에 대한 연결 엔드포인트를 제공합니다. 
- 로그인, 방화벽, 사용자, 역할 구성 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다.
- 여러 버전으로 제공됩니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md)을 참조하세요.
- 사용자가 확장할 수 있습니다. 자세한 내용은 [PostgreSQL 확장](concepts-extensions.md)을 참조하세요.

PostgreSQL 서버용 Azure Database 내에서 하나 이상의 데이터베이스를 만들 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 가격은 가격 책정 계층, vCore, 저장소(GB)의 구성에 따라 서버별로 구성됩니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure 데이터베이스 서버에 연결하고 인증을 받으려면 어떻게 해야 하나요?
다음과 같은 요소를 활용하면 데이터베이스에 안전하게 액세스할 수 있습니다.

|||
|:--|:--|
| **인증 및 권한 부여** | PostgreSQL용 Azure 데이터베이스 서버는 네이티브 PostgreSQL 인증을 지원합니다. 서버의 관리자 로그인을 사용하여 서버에 연결하고 인증을 받을 수 있습니다. |
| **프로토콜** | 이 서비스는 PostgreSQL에서 사용되는 메시지 기반 프로토콜을 지원합니다. |
| **TCP/IP** | 이 프로토콜은 TCP/IP 및 Unix 도메인 소켓을 통해 지원됩니다. |
| **방화벽** | 데이터를 보호하기 위해, 방화벽 규칙은 권한이 있는 컴퓨터를 지정할 때까지 서버 및 해당 데이터베이스에 대한 모든 액세스를 금지합니다.  [Azure Database for PostgreSQL 서버 방화벽 규칙](concepts-firewall-rules.md)을 참조하세요. |

## <a name="managing-your-server"></a>서버 관리
[Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/postgres)를 사용하여 Azure Database for PostgreSQL 서버를 관리할 수 있습니다.

서버를 만드는 동안 관리 사용자의 자격 증명을 설정합니다. 관리 사용자는 서버에서 권한이 가장 높은 사용자입니다. azure_pg_admin 역할에 속합니다. 이 역할에는 전체 슈퍼 사용자 권한이 없습니다. 

PostgreSQL 슈퍼 사용자 특성은 관리 서비스에 속하는 azure_superuser에게 할당됩니다. 이 역할에 대해서는 액세스 권한이 없습니다.

Azure Database for PostgreSQL 서버에는 기본 데이터베이스가 있습니다. 
- **postgres** - 서버가 만들어지면 연결할 수 있는 기본 데이터베이스입니다.
- **azure_maintenance** - 이 데이터베이스는 관리 서비스를 제공하는 프로세스를 사용자 작업으로부터 분리하는 데 사용됩니다. 이 데이터베이스에 대해서는 액세스 권한이 없습니다.
- **azure_sys** - 쿼리 저장소용 데이터베이스입니다. 쿼리 저장소가 꺼져 있으면 이 데이터베이스에는 데이터가 누적되지 않습니다(기본 설정). 자세한 내용은 [쿼리 저장소 개요](concepts-query-store.md)를 참조하세요.


## <a name="server-parameters"></a>서버 매개 변수
PostgreSQL 서버 매개 변수는 서버의 구성을 확인합니다. Azure Database for PostgreSQL에서 매개 변수 목록은 Azure Portal 또는 Azure CLI를 사용하여 보고 편집할 수 있습니다. 

Azure Database for PostgreSQL에서 구성 가능한 매개 변수는 Postgres의 관리되는 서비스이며 로컬 Postgres 인스턴스에 있는 매개 변수의 하위 집합입니다(Postgres 매개 변수에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.6/static/runtime-config.html)를 참조하세요). Azure Database for PostgreSQL 서버를 생성할 때 각 매개 변수의 기본값을 사용할 수 있습니다. 변경 내용을 적용하기 위해 서버를 다시 시작하거나 슈퍼 사용자 액세스가 필요한 일부 매개 변수는 사용자가 구성할 수 없습니다.


## <a name="next-steps"></a>다음 단계
- 서비스 개요는  [Azure Database for PostgreSQL 개요](overview.md)를 참조하세요.
-  **서비스 계층**에 따른 특정 리소스 할당량 및 제한 사항에 대한 자세한 내용은  [서비스 계층](concepts-pricing-tiers.md)을 참조하세요.
- 서비스 연결에 대한 자세한 내용은  [Azure Database for PostgreSQL에 대한 연결 라이브러리](concepts-connection-libraries.md)를 참조하세요.
- [Azure Portal](howto-configure-server-parameters-using-portal.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 통해 서버 매개 변수를 보고 편집합니다.
