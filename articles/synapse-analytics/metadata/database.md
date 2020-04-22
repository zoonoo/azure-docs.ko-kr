---
title: Azure Synapse Analytics 공유 데이터베이스
description: Azure Synapse Analytics는 Apache Spark에서 테이블을 만들면 SQL 주문형(미리 보기) 및 SQL 풀 엔진에서 액세스할 수 있는 공유 메타데이터 모델을 제공합니다.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420217"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics 공유 데이터베이스

Azure Synapse Analytics를 사용하면 여러 컴퓨팅 작업 영역 엔진이 Spark 풀(미리 보기), SQL 주문형(미리 보기) 엔진, SQL 풀 간에 데이터베이스와 테이블을 공유할 수 있습니다.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Spark 작업을 사용하여 만든 데이터베이스는 작업 영역에 있는 모든 현재 및 미래의 Spark 풀(미리 보기)과 SQL 주문형 엔진에 동일한 이름으로 표시됩니다.

메타데이터 동기화가 활성화된 작업 영역에 SQL 풀이 있거나 메타데이터 동기화가 활성화된 새 SQL 풀을 만드는 경우 이러한 Spark 생성 데이터베이스는 SQL 풀 데이터베이스의 특수 스키마에 자동으로 매핑됩니다. 

각 스키마 이름은 Spark 데이터베이스 이름 다음에 `$` 접두사를 붙이는 방식으로 지정됩니다. Spark에서 생성한 데이터베이스의 외부 테이블과 관리형 테이블은 모두 해당하는 특수 스키마에서 외부 테이블로 공개됩니다.

또한 `default`라는 Spark 기본 데이터베이스는 SQL 주문형 컨텍스트에서 `default`라는 데이터베이스로 표시되고, 메타데이터 동기화가 켜진 SQL 풀 데이터베이스에서 `$default`로 표시됩니다.

데이터베이스가 SQL 주문형 및 SQL 풀에 비동기적으로 동기화되므로 해당 테이블이 표시될 때까지 시간이 걸립니다.

## <a name="manage-a-spark-created-database"></a>Spark에서 만든 데이터베이스 관리

Spark를 사용하여 Spark에서 만든 데이터베이스를 관리합니다. 예를 들어 Spark 풀 작업을 통해 삭제하고 Spark에서 테이블을 만듭니다.

SQL주문형을 사용하여 Spark에서 만든 데이터베이스에 개체를 만들거나 데이터베이스를 삭제하려고 시도하면 작업이 성공합니다. 그러나 원래 Spark 데이터베이스는 변경되지 않습니다.

SQL 풀에서 동기화된 스키마를 삭제하거나 테이블을 이 풀에 만들려고 시도하면 Azure에서 오류를 반환합니다.

## <a name="handling-of-name-conflicts"></a>이름 충돌 처리

Spark 데이터베이스 이름이 기존 SQL 주문형 데이터베이스 이름과 충돌하는 경우 SQL 주문형에서 Spark 데이터베이스에 접미사가 추가됩니다. SQL 주문형의 접미사는 `_<workspace name>-ondemand-DefaultSparkConnector`입니다.

예를 들어 `mydb`라는 Spark 데이터베이스가 Azure Synapse 작업 영역 `myws`에 생성되고 해당 이름을 사용하는 SQL 주문형 데이터베이스가 이미 있는 경우에는 `mydb_myws-ondemand-DefaultSparkConnector` 이름을 사용하여 SQL 주문형의 Spark 데이터베이스를 참조해야 합니다.

> [!CAUTION]
> 주의: 이 동작에 대한 종속성을 사용하지 마세요.

## <a name="security-model"></a>보안 모델

Spark 데이터베이스와 테이블은 SQL 엔진에서 동기화되는 해당 표현과 함께 기본 스토리지 수준에서 보호됩니다.

데이터베이스를 만드는 보안 주체는 해당 데이터베이스의 소유자로 간주되며, 데이터베이스와 해당 개체에 대한 모든 권한을 갖습니다.

사용자 또는 보안 그룹과 같은 보안 주체에게 데이터베이스에 대한 액세스 권한을 부여하려면 `warehouse` 디렉터리의 기본 폴더와 파일에 대한 적절 한 POSIX 폴더 및 파일 권한을 제공해야 합니다. 

예를 들어 보안 주체가 데이터베이스의 테이블을 읽을 수 있으려면 `warehouse` 디렉터리의 데이터베이스 폴더에서 시작하는 모든 폴더는 해당 보안 주체에 할당된 `X` 및 `R` 권한이 있어야 합니다. 또한 파일(예: 테이블의 기본 데이터 파일)에는 `R` 권한이 필요합니다. 

보안 주체가 데이터베이스에 개체를 만들거나 데이터베이스의 개체를 삭제할 수 있는 기능이 필요한 경우 `warehouse` 폴더의 폴더와 파일에 대한 추가 `W` 권한이 필요합니다.

## <a name="examples"></a>예

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Spark 데이터베이스 만들기 및 연결 - SQL 주문형

먼저 작업 영역에서 이미 만든 Spark 클러스터를 사용하여 `mytestdb`라는 새 Spark 데이터베이스를 만듭니다. 예를 들어 Spark C# Notebook을 사용하는 경우 다음 .NET for Spark 문을 사용하면 됩니다.

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

잠시 후 SQL 주문형의 데이터베이스를 볼 수 있습니다. 예를 들어 SQL 주문형에서 다음 명령문을 실행합니다.

```sql
SELECT * FROM sys.databases;
```

결과에 `mytestdb`가 포함되어 있는지 확인합니다.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>SQL 풀에서 Spark 데이터베이스 공개

이전 예제에서 데이터베이스를 만들었으므로, 이제 작업 영역에서 메타데이터 동기화를 사용하도록 설정하는 `mysqlpool`이라는 SQL 풀을 만듭니다.

`mysqlpool` SQL 풀에 대해 다음 명령문을 실행합니다.

```sql
SELECT * FROM sys.schema;
```

결과에서 새로 만든 데이터베이스의 스키마를 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 공유 메타데이터에 대한 자세한 정보](overview.md)
- [Azure Synapse Analytics의 공유 메타데이터 테이블에 대한 자세한 정보](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
