---
title: AdventureWorks 샘플 데이터베이스를 SQL Managed Instance 복원
description: AdventureWorks 샘플 데이터베이스를 SQL Managed Instance 복원
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d238a889648e789087a0803f6b50288318462c7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629066"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>AdventureWorks 샘플 데이터베이스를 SQL Managed Instance으로 복원-Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=tsql&preserve-view=true) 는 자습서 및 예제에서 자주 사용 되는 OLTP 데이터베이스를 포함 하는 예제 데이터베이스입니다. [SQL Server 샘플 GitHub 리포지토리의](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)일부로 Microsoft에서 제공 하 고 유지 관리 합니다.

이 문서에서는 SQL Managed Instance Azure Arc로 복원 된 AdventureWorks 예제 데이터베이스를 가져오는 간단한 프로세스에 대해 설명 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>AdventureWorks 백업 파일 다운로드

AdventureWorks 백업 (.bak) 파일을 SQL Managed Instance 컨테이너에 다운로드 합니다. 이 예제에서는 명령을 사용 하 여 `kubectl exec` SQL Managed Instance 컨테이너 내부에서 명령을 원격으로 실행 하 여 .bak 파일을 컨테이너에 다운로드 합니다. `wget`SQL Managed Instance 컨테이너 내에 가져오려는 다른 데이터베이스 백업 파일이 있는 경우이 파일을 통해 액세스할 수 있는 위치에서이 파일을 다운로드 합니다. SQL Managed Instance 컨테이너 내부에 있으면 표준 T-sql을 사용 하 여 쉽게 복원할 수 `RESTORE DATABASE` 있습니다.

이러한 명령을 실행 하 여 실행 하기 전에 pod 이름 및 네임 스페이스 이름 값으로 대체 하는 .bak 파일을 다운로드 합니다.
> [!NOTE]
>  GitHub에서 파일을 다운로드 하려면 컨테이너가 443 이상 인터넷에 연결 되어 있어야 합니다.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

예제

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>AdventureWorks 데이터베이스 복원

마찬가지로 exec 명령을 실행 하 여 `kubectl` `sqlcmd` sql Managed Instance 컨테이너에 포함 된 CLI 도구를 사용 하 여 데이터베이스를 복원 하는 t-sql 명령을 실행할 수 있습니다.

이와 같은 명령을 실행 하 여 데이터베이스를 복원 합니다. 실행 하기 전에 pod 이름, 암호 및 네임 스페이스 이름의 값을 바꿉니다.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
예제

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
