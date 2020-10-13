---
title: 연결 및 쿼리-단일 서버 PostgreSQL
description: 서버에 연결 하 고 쿼리를 실행 하는 방법을 보여 주는 Azure My SQL Database 빠른 시작에 대 한 링크입니다.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 924dbadc07f57e5928ecc63a24bf5e57d6213670
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938813"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Azure database for PostgreSQL에 대 한 연결 및 쿼리 개요-단일 서버

다음 문서에는 Azure Database for PostgreSQL 단일 서버로 연결 하 고 쿼리 하는 방법을 보여 주는 예제에 대 한 링크가 포함 되어 있습니다. 이 가이드에는 아래 지원 되는 언어의 서버에 연결 하는 데 사용할 수 있는 TLS 권장 사항 및 확장도 포함 되어 있습니다.

## <a name="quickstarts"></a>빠른 시작

| 빠른 시작 | Description |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|Pgadmin을 사용 하 여 서버에 연결 하 고 데이터베이스 개체의 생성, 유지 관리 및 사용을 간소화할 수 있습니다.|
|[Azure Cloud Shell에서 psql](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|이 문서에서는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 에서 [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) 을 실행 하 여 서버에 연결한 다음 문을 실행 하 여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제 하는 방법을 보여 줍니다. 개발 환경에 설치 된 경우 **psql** 을 실행할 수 있습니다.|
|[VS Code PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|VS Code 용 Azure 데이터베이스 확장 (미리 보기)을 사용 하면 풍부한 Intellisense를 사용 하 여 scrapbooks를 사용 하 여 로컬 및 클라우드에서 PostgreSQL 서버를 찾아보고 쿼리할 수 있습니다. |
|[PHP](connect-php.md)|이 빠른 시작에서는 PHP를 사용 하 여 데이터베이스에 연결 하 고 데이터베이스 개체 작업을 사용 하 여 데이터를 쿼리 하는 프로그램을 만드는 방법을 보여 줍니다.|
|[Java](connect-java.md)|이 빠른 시작에서는 Java를 사용 하 여 데이터베이스에 연결한 다음 데이터베이스 개체에 대 한 작업을 사용 하 여 데이터를 쿼리 하는 방법을 보여 줍니다.|
|[Node.js](connect-nodejs.md)|이 빠른 시작에서는 Node.js 사용 하 여 데이터베이스에 연결 하 고 데이터베이스 개체 작업을 사용 하 여 데이터를 쿼리 하는 프로그램을 만드는 방법을 보여 줍니다.|
|[.NET (C #)](connect-csharp.md)|이 빠른 시작에서는 use.NET (c #)를 사용 하 여 데이터베이스에 연결 하 고 데이터베이스 개체 작업을 사용 하 여 데이터를 쿼리 하는 c # 프로그램을 만드는 방법을 보여 줍니다.|
|[Go](connect-go.md)|이 빠른 시작에서는 Go를 사용하여 데이터베이스에 연결하는 방법을 보여 줍니다. 데이터 쿼리 및 수정을 위한 Transact SQL 문도 보여 줍니다.|
|[Python](connect-python.md)|이 빠른 시작에서는 Python을 사용 하 여 데이터베이스에 연결 하 고 데이터베이스 개체 작업을 사용 하 여 데이터를 쿼리 하는 방법을 보여 줍니다. |
|[Ruby](connect-ruby.md)|이 빠른 시작에서는 Ruby를 사용 하 여 데이터베이스에 연결 하 고 데이터베이스 개체 작업을 사용 하 여 데이터를 쿼리 하는 프로그램을 만드는 방법을 보여 줍니다.|


## <a name="tls-considerations-for-database-connectivity"></a>데이터베이스 연결에 대한 TLS 고려 사항

TLS (전송 계층 보안)는 Microsoft에서 제공 하거나 Azure Database for PostgreSQL의 데이터베이스에 연결 하는 데 지원 되는 모든 드라이버에서 사용 됩니다. 특별 한 구성은 필요 하지 않지만 새로 만든 서버에는 TLS 1.2을 적용 합니다. TLS 1.0 및 1.1를 사용 하는 경우 서버에 대 한 TLS 버전을 업데이트 하는 것이 좋습니다. [TLS를 구성 하는 방법을](howto-tls-configurations.md) 참조 하세요.


## <a name="postgresql-extensions"></a>PostgreSQL 확장
PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장은 단일 명령을 사용하여 데이터베이스에서 로드하거나 제거할 수 있는 단일 패키지에서 여러 관련 SQL 개체를 함께 번들로 묶습니다. 데이터베이스에 로드한 확장은 기본 제공 기능처럼 작동합니다.

- [Postgres 11 확장](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-11-extensions)
- [Postgres 10 확장](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-10-extensions)
- [Postgres 9.6 확장](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-96-extensions)
- [Postgres 9.5 확장](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-95-extensions)

자세한 내용은 [단일 서버에서 PostgreSQL 확장을 사용 하는 방법](concepts-extensions.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계 

- [덤프 및 복원을 사용 하 여 데이터 마이그레이션](howto-migrate-using-dump-and-restore.md)
- [가져오기 및 내보내기를 사용 하 여 데이터 마이그레이션](howto-migrate-using-export-and-import.md)
