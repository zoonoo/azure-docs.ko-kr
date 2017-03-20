---
title: "Azure CLI: 단일 SQL Database 만들기 및 쿼리 | Microsoft Docs"
description: "Azure CLI를 사용하여 SQL Database 논리 서버, 서버 수준 방화벽 규칙 및 데이터베이스를 만드는 방법에 대해 알아봅니다."
keywords: "SQL 데이터베이스 자습서, SQL 데이터베이스 만들기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>Azure CLI에서 단일 Azure SQL Database 만들기 및 쿼리

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 Azure SQL Database를 배포하는 방법에 대해 자세히 설명합니다.

시작하기 전에, Azure CLI가 Azure 설치되었는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인

[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>논리 서버 만들기

[az sql server create](/cli/azure/sql/server#create) 명령으로 논리 서버를 만듭니다. 다음 예제에서는 관리자 로그인 이름이 `ServerAdmin`이고 암호가 `ChangeYourAdminPassword1`인 리소스 그룹에 임의로 이름이 지정된 서버를 생성합니다. 이러한 미리 정의된 값은 필요에 따라 바꿉니다.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>서버 방화벽 규칙 구성

[az sql server firewall create](/cli/azure/sql/server/firewall-rule#create) 명령으로 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 통해 외부 응용 프로그램(예: SQL Server Management Studio 또는 SQLCMD 유틸리티)이 SQL Database 서비스 방화벽을 통해 SQL Database에 연결되도록 할 수 있습니다. 다음 예제에서는 미리 정의된 주소 범위(이 예제에서는 전체 가능한 IP 주소 범위)에 대해 방화벽 규칙을 만듭니다. 이러한 미리 정의된 값은 외부 IP 주소 또는 IP 주소 범위 값으로 바꿉니다. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>서버에 데이터베이스 만들기

[az sql db create](/cli/azure/sql/db#create) 명령으로 서버에 데이터베이스를 만듭니다. 다음 예제에서는 `mySampleDatabase`라는 빈 데이터베이스를 만듭니다. 이 미리 정의된 값은 필요에 따라 바꿉니다.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 모든 리소스를 제거하려면 다음 명령을 실행합니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- SQL Server Management Studio를 사용하여 연결 및 쿼리하려면 [SSMS를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.
- Visual Studio를 사용하여 연결하려면 [Visual Studio를 사용하여 연결 및 쿼리](sql-database-connect-query.md)를 참조하세요.
- SQL Database에 대한 기술적 개요는 [SQL Database 서비스 정보](sql-database-technical-overview.md)를 참조하세요.

