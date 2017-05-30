---
title: "빠른 시작: MySQL용 Azure Database 서버 만들기 - Azure CLI | Microsoft Docs"
description: "이 빠른 시작에서는 Azure CLI를 사용하여 Azure 리소스 그룹에서 MySQL용 Azure Database 서버를 만드는 방법을 살펴봅니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/24/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9f78163e4ff1166a2abd94150d686256ee338286
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI를 사용한 MySQL용 Azure 데이터베이스 서버 만들기 
이 빠른 시작에서는 약 5분 안에 Azure CLI를 사용하여 Azure 리소스 그룹에서 MySQL용 Azure Database 서버를 만드는 방법을 살펴봅니다. 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다.

이 빠른 시작을 완료하려면 최신 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)을 설치했는지 확인합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인
[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```
명령 프롬프트 지침에 따라 브라우저에서 https://aka.ms/devicelog를 연 다음 **명령 프롬프트**에서 생성된 코드를 입력합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[az group create](https://docs.microsoft.com/cli/azure/group#create) 명령을 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제는 `westus` 위치에 `mycliresource`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>MySQL용 Azure Database 서버 만들기
**az mysql server create** 명령을 사용하여 MySQL용 Azure Database 서버를 만듭니다. 서버는 여러 데이터베이스를 관리할 수 있습니다. 일반적으로 각 프로젝트 또는 각 사용자에 대해 별도의 데이터베이스가 사용됩니다.

다음 예제에서는 `westus`에 있는 MySQL용 Azure Database 서버를 `mycliserver`라는 이름으로 `mycliresource` 리소스 그룹에 만듭니다. 서버에는 `myadmin` 이름과 `Password01!` 암호의 관리자 로그인이 있습니다. 서버는 서버의 모든 데이터베이스 간에 공유되는 **기본** 성능 계층과 **50** 계산 단위로 만들어집니다. 응용 프로그램 요구 사항에 따라 계산과 저장소의 크기를 확장하거나 축소할 수 있습니다.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![Azure CLI를 사용한 MySQL용 Azure 데이터베이스 서버 만들기 ](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>방화벽 규칙 구성
**az mysql server firewall-rule create** 명령을 사용하여 MySQL용 Azure Database 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 **mysql.exe** 명령줄 도구 또는 MySQL Workbench와 같은 외부 응용 프로그램에서 Azure MySQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 

다음 예제에서는 미리 정의된 주소 범위(이 예제에서는 전체 가능한 IP 주소 범위)에 대해 방화벽 규칙을 만듭니다.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>SSL 설정 구성
기본적으로 서버와 클라이언트 응용 프로그램 간에 SSL 연결이 적용됩니다.  이렇게 하면 인터넷을 통해 데이터 스트림을 암호화하여 "이동 중"인 데이터를 보호할 수 있습니다.  이 빠른 시작을 간단하게 하기 위해 서버에 대해 SSL 연결을 사용하지 않을 것입니다.  이런 설정은 프로덕션 서버에는 권장되지 않습니다.  자세한 내용은 [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요.

다음 예에서는 MySQL 서버에 SSL을 적용하지 않습니다.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

결과는 JSON 형식입니다. **fullyQualifiedDomainName** 및 **administratorLogin**을 기록해 둡니다.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>mysql.exe 명령줄 도구를 사용하여 서버에 연결
**mysql.exe** 명령줄 도구를 사용하여 서버에 연결하려면 컴퓨터에 MySQL이 설치되어 있어야 합니다.  MySQL은 [여기](https://dev.mysql.com/downloads/)에서 다운로드할 수 있습니다.

명령 프롬프트에 다음 명령을 입력합니다. 

1. **mysql** 명령줄 도구를 사용하여 서버에 연결:
```dos
 mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
```

2. 서버 상태 보기:
```dos
 mysql> status
```
모든 작업이 제대로 진행되었다면 명령줄에 다음이 출력될 것입니다.

```dos
C:\Users\v-chenyh>mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mycliserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> 다른 명령은 [MySQL 5.6 참조 설명서 - 4.5.1장](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)을 참조하세요.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>MySQL Workbench GUI 도구로 서버에 연결
1.    클라이언트 컴퓨터에서 MySQL Workbench 응용 프로그램을 실행합니다. MySQL Workbench는 [여기](https://dev.mysql.com/downloads/workbench/)에서 다운로드할 수 있습니다.

2.    **새 연결 설정** 대화 상자의 **매개 변수** 탭에 다음 정보를 입력합니다.

| **매개 변수** | **설명** |
|----------------|-----------------|
|    *연결 이름* | 이 연결에 대한 이름 지정(아무 이름이나 가능) |
| *연결 방법* | 표준(TCP/IP) 선택 |
| *호스트 이름* | mycliserver.mysql.database.azure.com(앞에서 기록한 서버 이름) |
| *포트* | 3306 |
| *사용자 이름* | myadmin@mycliserver(앞에서 기록한 서버 관리자 로그인) |
| *암호* | 관리자 계정 암호를 저장하세요. |

   ![새 연결 설정](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

**연결 테스트**를 클릭하여 모든 매개 변수가 올바르게 구성되었는지 테스트합니다.
이제 방금 만든 연결을 클릭하여 서버에 연결할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작/자습서에서 이러한 리소스가 필요하지 않으면 다음을 수행하여 삭제할 수 있습니다. 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI로 MySQL 데이터베이스 디자인](./tutorial-design-database-using-cli.md)

