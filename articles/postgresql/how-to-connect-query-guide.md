---
title: 연결 및 쿼리 - 단일 서버 PostgreSQL
description: Azure Database for PostgreSQL 단일 서버에 연결하고 쿼리를 실행하는 방법을 알려 주는 빠른 시작 링크입니다.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 1506ce311fe443247050a36e1b9fa4600360ac6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604144"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Azure database for PostgreSQL- 단일 서버 개요 연결 및 쿼리

다음 문서에는 Azure Database for PostgreSQL 단일 서버로 연결하고 쿼리하는 방법을 알려주는 예제 링크가 포함되어 있습니다. 이 가이드에는 아래에서 지원되는 언어의 서버에 연결하는 데 쓸 수 있는 TLS 권장 사항 및 확장 기능도 포함되어 있습니다.

## <a name="quickstarts"></a>빠른 시작

| 빠른 시작 | Description |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|Pgadmin을 사용하여 서버에 연결하고 데이터베이스 개체의 생성, 유지 관리 및 사용을 간소화할 수 있습니다.|
|[Azure Cloud Shell의 psql](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|이 문서에서는 [Azure Cloud Shell](../cloud-shell/overview.md)에서 [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html)을 실행하여 서버에 연결한 다음 문을 실행하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 알려줍니다. 개발 환경에 설치된 경우 **psql** 을 실행할 수 있습니다.|
|[VS Code를 사용한 PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|VS Code용 Azure 데이터베이스 확장(미리 보기)을 사용하면 풍부한 Intellisense가 있는 스크랩북을 사용해 로컬 및 클라우드에서 PostgreSQL 서버를 찾아보고 쿼리할 수 있습니다. |
|[PHP](connect-php.md)|이 빠른 시작에서는 PHP를 사용하여 데이터베이스에 연결하고 데이터베이스 개체 작업을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[Java](connect-java.md)|이 빠른 시작에서는 Java를 사용하여 데이터베이스에 연결한 다음 데이터베이스 개체 작업을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.|
|[Node.JS](connect-nodejs.md)|이 빠른 시작에서는 Node.js를 사용하여 데이터베이스에 연결하고 데이터베이스 개체 작업을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[.NET(C#)](connect-csharp.md)|이 빠른 시작에서는 .NET(C#)을 사용하여 데이터베이스에 연결하고 데이터베이스 개체 작업을 사용하여 데이터를 쿼리하는 C# 프로그램을 만드는 방법을 보여 줍니다.|
|[Go](connect-go.md)|이 빠른 시작에서는 Go를 사용하여 데이터베이스에 연결하는 방법을 보여 줍니다. 데이터 쿼리 및 수정을 위한 Transact SQL 문도 보여 줍니다.|
|[Python](connect-python.md)|이 빠른 시작에서는 Python을 사용하여 데이터베이스에 연결한 다음 데이터베이스 개체 작업을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다. |
|[Ruby](connect-ruby.md)|이 빠른 시작에서는 Ruby를 사용하여 데이터베이스에 연결하고 데이터베이스 개체 작업을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|

## <a name="tls-considerations-for-database-connectivity"></a>데이터베이스 연결에 대한 TLS 고려 사항

TLS(전송 계층 보안)는 Microsoft에서 제공하거나 Azure Database for PostgreSQL의 데이터베이스에 연결을 지원하는 모든 드라이버에서 사용됩니다. 특별한 구성은 필요하지 않지만 새로 만든 서버에는 TLS 1.2을 적용합니다. TLS 1.0 및 1.1을 사용하는 경우 서버를 위해 TLS 버전을 업데이트하기를 권합니다. [TLS를 구성하는 방법](howto-tls-configurations.md)을 참조하세요.

## <a name="postgresql-extensions"></a>PostgreSQL 확장

PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장은 단일 명령을 사용하여 데이터베이스에서 로드하거나 제거할 수 있는 단일 패키지에서 여러 관련 SQL 개체를 함께 번들로 묶습니다. 데이터베이스에 로드한 확장은 기본 제공 기능처럼 작동합니다.

- [Postgres 11 확장](./concepts-extensions.md#postgres-11-extensions)
- [Postgres 10 확장](./concepts-extensions.md#postgres-10-extensions)
- [Postgres 9.6 확장](./concepts-extensions.md#postgres-96-extensions)

자세한 내용은 [단일 서버에서 PostgreSQL 확장을 사용하는 방법](concepts-extensions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [덤프 및 복원을 사용한 마이그레이션](howto-migrate-using-dump-and-restore.md)
- [가져오기 및 내보내기를 사용하여 데이터 마이그레이션](howto-migrate-using-export-and-import.md)
