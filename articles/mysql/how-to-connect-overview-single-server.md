---
title: 연결 및 쿼리 - 단일 서버 MySQL
description: Azure My SQL Database 단일 서버에 연결하고 쿼리를 실행하는 방법을 알려 주는 빠른 시작 링크입니다.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: def4794d091155cbf10a854d304d4390659626bf
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642164"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Azure Database for MySQL - 단일 서버 연결 및 쿼리 개요

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

다음 문서에는 Azure Database for MySQL 단일 서버로 연결하고 쿼리하는 방법을 알려주는 예제 링크가 포함되어 있습니다. 이 가이드에는 아래에서 지원되는 언어의 서버에 연결하는 데 쓸 수 있는 TLS 권장 사항과 라이브러리도 포함되어 있습니다.

## <a name="quickstarts"></a>빠른 시작

| 빠른 시작 | Description |
|---|---|
|[MySQL Workbench](connect-workbench.md)|이 빠른 시작에서는 MySQL Workbench 클라이언트를 사용하여 데이터베이스에 연결하는 방법을 보여 줍니다. MySQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트, 삭제할 수 있습니다.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|이 문서에서는 [Azure Cloud Shell](../cloud-shell/overview.md)에서 **mysql.exe** 를 실행하여 서버에 연결한 다음 문을 실행하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트, 삭제하는 방법을 보여 줍니다.|
|[Visual Studio를 사용하는 MySQL](https://www.mysql.com/why-mysql/windows/visualstudio)|MySQL 서버에 연결하는 데 MySQL for Visual Studio를 사용할 수 있습니다. MySQL for Visual Studio는 새 연결을 쉽게 설정하고 데이터베이스 개체를 사용할 수 있도록 서버 탐색기에 직접 통합됩니다.|
|[PHP](connect-php.md)|이 빠른 시작에서는 PHP를 사용하여 데이터베이스에 연결하고, MySQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[Java](connect-java.md)|이 빠른 시작에서는 Java를 사용하여 데이터베이스에 연결한 다음, MySQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.|
|[Node.JS](connect-nodejs.md)|이 빠른 시작에서는 Node.js를 사용하여 데이터베이스에 연결하고, MySQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[.NET(C#)](connect-csharp.md)|이 빠른 시작에서는 .NET(C#)을 사용하여 데이터베이스에 연결하고, MySQL 문을 사용하여 데이터를 쿼리하는 C# 프로그램을 만드는 방법을 보여 줍니다.|
|[Go](connect-go.md)|이 빠른 시작에서는 Go를 사용하여 데이터베이스에 연결하는 방법을 보여 줍니다. 데이터 쿼리 및 수정을 위한 Transact SQL 문도 보여 줍니다.|
|[Python](connect-python.md)|이 빠른 시작에서는 Python을 사용하여 데이터베이스에 연결하고 MySQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다. |
|[Ruby](connect-ruby.md)|이 빠른 시작에서는 Ruby를 사용하여 데이터베이스에 연결하고, MySQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[C++](connect-cpp.md)|이 빠른 시작에서는 C+++을 사용하여 데이터베이스에 연결하고 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|

## <a name="tls-considerations-for-database-connectivity"></a>데이터베이스 연결에 대한 TLS 고려 사항

TLS(전송 계층 보안)는 Microsoft에서 제공하거나 Azure Database for MySQL의 데이터베이스에 연결을 지원하는 모든 드라이버에서 사용됩니다. 특별한 구성은 필요하지 않지만 새로 만든 서버에는 TLS 1.2를 적용합니다. TLS 1.0 및 1.1을 사용하는 경우 서버를 위해 TLS 버전을 업데이트하기를 권합니다. [TLS를 구성하는 방법](howto-tls-configurations.md)을 참조하세요.

## <a name="libraries"></a>라이브러리

Azure Database for MySQL은 전 세계에서 가장 인기 있는 커뮤니티 버전의 MySQL 데이터베이스를 사용합니다. 따라서 다양한 프로그래밍 언어 및 드라이버와 호환됩니다. 세 가지 최신 버전의 MySQL 드라이버를 지원하는 것이 목표이며, MySQL 드라이버의 기능 및 사용 편의를 개선하기 위해 지속적으로 오픈 소스 커뮤니티의 작성자와 협력하고 있습니다.

Azure Database for MySQL 단일 서버와 호환되는 [드라이버](concepts-compatibility.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [덤프 및 복원을 사용한 마이그레이션](concepts-migrate-dump-restore.md)
- [가져오기 및 내보내기를 사용하여 데이터 마이그레이션](concepts-migrate-import-export.md)