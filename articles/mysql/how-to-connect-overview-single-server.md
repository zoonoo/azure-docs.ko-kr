---
title: 연결 및 쿼리-단일 서버 MySQL
description: 서버에 연결 하 고 쿼리를 실행 하는 방법을 보여 주는 Azure My SQL Database 빠른 시작에 대 한 링크입니다.
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 229011f11ad6898555f59b063910d80a679070e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940445"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Azure database for MySQL에 대 한 연결 및 쿼리 개요-단일 서버
다음 문서에는 Azure Database for MySQL 단일 서버로 연결 하 고 쿼리 하는 방법을 보여 주는 예제에 대 한 링크가 포함 되어 있습니다. 이 가이드에는 아래 지원 되는 언어의 서버에 연결 하는 데 사용할 수 있는 TLS 권장 사항 및 라이브러리도 포함 되어 있습니다.

## <a name="quickstarts"></a>빠른 시작

| 빠른 시작 | Description |
|---|---|
|[MySQL Workbench](connect-workbench.md)|이 빠른 시작에서는 MySQL 워크 벤치 클라이언트를 사용 하 여 데이터베이스에 연결 하는 방법을 보여 줍니다. 그런 다음 MySQL 문을 사용 하 여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제할 수 있습니다.|
|[Azure Cloud Shell](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|이 문서에서는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 에서 **mysql.exe** 를 실행 하 여 서버에 연결한 다음 문을 실행 하 여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제 하는 방법을 보여 줍니다.|
|[Visual Studio를 사용 하는 MySQL](https://www.mysql.com/why-mysql/windows/visualstudio)|Mysql 서버에 연결 하기 위해 Visual Studio 용 MySQL을 사용할 수 있습니다. Visual Studio 용 MySQL은 새 연결을 쉽게 설정 하 고 데이터베이스 개체를 사용할 수 있도록 서버 탐색기에 직접 통합 됩니다.|
|[PHP](connect-php.md)|이 빠른 시작에서는 PHP를 사용 하 여 데이터베이스에 연결 하 고 MySQL 문을 사용 하 여 데이터를 쿼리 하는 프로그램을 만드는 방법을 보여 줍니다.|
|[Java](connect-java.md)|이 빠른 시작에서는 Java를 사용 하 여 데이터베이스에 연결한 다음 MySQL 문을 사용 하 여 데이터를 쿼리 하는 방법을 보여 줍니다.|
|[Node.JS](connect-nodejs.md)|이 빠른 시작에서는 Node.js를 사용 하 여 데이터베이스에 연결 하 고 MySQL 문을 사용 하 여 데이터를 쿼리 하는 프로그램을 만드는 방법을 보여 줍니다.|
|[.NET (C #)](connect-csharp.md)|이 빠른 시작에서는 use.NET (c #)를 사용 하 여 데이터베이스에 연결 하 고 MySQL 문을 사용 하 여 데이터를 쿼리 하는 c # 프로그램을 만드는 방법을 보여 줍니다.|
|[Go](connect-go.md)|이 빠른 시작에서는 Go를 사용하여 데이터베이스에 연결하는 방법을 보여 줍니다. 데이터 쿼리 및 수정을 위한 Transact SQL 문도 보여 줍니다.|
|[Python](connect-python.md)|이 빠른 시작에서는 Python을 사용 하 여 데이터베이스에 연결 하 고 MySQL 문을 사용 하 여 데이터를 쿼리 하는 방법을 보여 줍니다. |
|[Ruby](connect-ruby.md)|이 빠른 시작에서는 Ruby를 사용 하 여 데이터베이스에 연결 하 고 MySQL 문을 사용 하 여 데이터를 쿼리 하는 프로그램을 만드는 방법을 보여 줍니다.|
|[C++](connect-cpp.md)|이 빠른 시작에서는 c + +를 사용 하 여 데이터베이스에 연결 하 고 쿼리 데이터를 사용 하는 프로그램을 만드는 방법을 보여 줍니다.|


## <a name="tls-considerations-for-database-connectivity"></a>데이터베이스 연결에 대한 TLS 고려 사항

TLS (전송 계층 보안)는 Microsoft에서 제공 하거나 Azure Database for MySQL의 데이터베이스에 연결 하는 데 지원 되는 모든 드라이버에서 사용 됩니다. 특별 한 구성은 필요 하지 않지만 새로 만든 서버에는 TLS 1.2을 적용 합니다. TLS 1.0 및 1.1를 사용 하는 경우 서버에 대 한 TLS 버전을 업데이트 하는 것이 좋습니다. [TLS를 구성 하는 방법을](howto-tls-configurations.md) 참조 하세요.


## <a name="libraries"></a>라이브러리

Azure Database for MySQL은 전 세계에서 가장 인기 있는 커뮤니티 버전의 MySQL 데이터베이스를 사용합니다. 따라서 다양 한 프로그래밍 언어 및 드라이버와 호환 됩니다. 세 가지 최신 버전의 MySQL 드라이버를 지원하는 것이 목표이며, MySQL 드라이버의 기능 및 사용 편의를 개선하기 위해 지속적으로 오픈 소스 커뮤니티의 작성자와 협력하고 있습니다.

Azure Database for MySQL 단일 서버와 호환 되는 [드라이버](concepts-compatibility.md) 를 확인 합니다. 


## <a name="next-steps"></a>다음 단계 
- [덤프 및 복원을 사용 하 여 데이터 마이그레이션](concepts-migrate-dump-restore.md)
- [가져오기 및 내보내기를 사용 하 여 데이터 마이그레이션](concepts-migrate-import-export.md)
