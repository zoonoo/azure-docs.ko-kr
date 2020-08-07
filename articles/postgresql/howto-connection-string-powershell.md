---
title: PowerShell을 사용 하 여 연결 문자열 생성-Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL에 연결 하기 위한 연결 문자열을 생성 하는 Azure PowerShell 예제를 제공 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: a8e82c1863fde7a7580300606949e00a34c4e58c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909903"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>PowerShell을 사용 하 여 Azure Database for PostgreSQL 연결 문자열을 생성 하는 방법

이 문서에서는 Azure Database for PostgreSQL 서버에 대 한 연결 문자열을 생성 하는 방법을 보여 줍니다. 연결 문자열을 사용 하 여 다양 한 응용 프로그램의 Azure Database for PostgreSQL에 연결할 수 있습니다.

## <a name="requirements"></a>요구 사항

이 문서에서는 다음 가이드에서 만든 리소스를 시작 지점으로 사용 합니다.

* [빠른 시작: PowerShell을 사용 하 여 Azure Database for PostgreSQL 서버 만들기](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>연결 문자열 가져오기

`Get-AzPostgreSqlConnectionString`Cmdlet은 Azure Database for PostgreSQL에 응용 프로그램을 연결 하기 위한 연결 문자열을 생성 하는 데 사용 됩니다. 다음 예에서는 **mydemoserver**에서 PHP 클라이언트에 대 한 연결 문자열을 반환 합니다.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

매개 변수에 유효한 값은 `Client` 다음과 같습니다.

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for PostgreSQL 서버 매개 변수 사용자 지정](howto-configure-server-parameters-using-powershell.md)
