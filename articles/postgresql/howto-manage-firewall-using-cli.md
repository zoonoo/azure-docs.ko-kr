---
title: "Azure CLI를 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리 | Microsoft Docs"
description: "Azure CLI를 사용하여 PostgreSQL용 Azure Database 방화벽 규칙을 만들고 관리하는 방법을 설명합니다."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Azure CLI를 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리
관리자는 서버 수준 방화벽 규칙을 사용하여 특정 IP 주소 또는 IP 주소 범위에서 PostgreSQL용 Azure Database 서버에 대한 액세스를 관리할 수 있습니다. 편리한 Azure CLI 명령을 사용하면 서버를 관리하는 방화벽 규칙을 만들고, 업데이트하고, 삭제하며, 표시할 수 있습니다. PostgreSQL용 Azure Database 방화벽에 대한 개요는 [PostgreSQL용 Azure Database 서버 방화벽 규칙](concepts-firewall-rules.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [PostgreSQL용 Azure Database 서버 및 데이터베이스](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 명령줄 유틸리티 설치

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>PostgreSQL용 Azure Database에 대한 서버 방화벽 규칙 구성
**az postgres server firewall-rule** 명령은 Azure CLI에서 방화벽 규칙을 만들고, 삭제, 나열, 표시 및 업데이트하는 데 사용됩니다. 이 

## <a name="login-to-azure-and-list-servers"></a>Azure에 로그인 및 서버 나열
Azure 계정으로 Azure CLI를 안전하게 연결합니다. **az login** 명령을 사용하여 이를 수행합니다.
1. 명령줄에서 다음 명령을 실행합니다.
```azurecli
az login
```
이 명령은 다음 단계에서 사용할 코드를 출력합니다.

2. 웹 브라우저를 사용하여 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 페이지를 열고 코드를 입력합니다.

3. 프롬프트가 나타나면 Azure 자격 증명을 사용하여 로그인합니다.

4. 로그인 권한이 부여되면 구독 목록은 콘솔에 인쇄됩니다.
원하는 구독의 ID를 복사하여 앞으로 진행하는 데 사용되는 현재 구독을 설정합니다.
```azurecli
az account set --subscription {your subscription id}
```
5. 이름이 정확하지 않은 경우 리소스 그룹 및 사용자의 구독에 대한 PostgreSQL용 Azure Databases 서버를 나열합니다.

```azurecli
az postgres server list --resource-group myresourcegroup
```
나열된 이름 특성은 작동할 PostgreSQL 서버를 지정하는 데 사용됩니다. 필요한 경우 이름 특성을 사용하여 이름이 올바른지 확인하기 위해 해당 서버에 대한 세부 정보를 확인합니다.

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>방화벽 규칙 나열 
서버 이름 및 리소스 그룹 이름을 사용하여 기존 서버 방화벽 규칙을 서버에 나열합니다. 서버 이름 특성은 **--name** 스위치가 아닌 **--server** 스위치에서 지정됩니다.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
출력은 있을 경우 기본적으로 JSON 형식으로 규칙을 나열하게 됩니다. 더 읽기 쉬운 테이블 형식에 대해 스위치 **--output table**을 출력으로 사용할 수도 있습니다.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>방화벽 규칙 만들기
PostgreSQL용 Azure Database 서버 이름 및 리소스 그룹 이름을 사용하여 서버에 새로운 방화벽 규칙을 만듭니다. 규칙에 사용할 이름, 규칙에 대한 시작 IP 및 끝 IP를 제공하여 액세스를 허용할 IP 주소의 범위를 지정합니다.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
액세스를 허용할 단일 IP 주소에 대해 이 예제에서와 같이 시작 IP 및 끝 IP와 동일한 주소를 제공합니다.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
성공하면 명령 출력은 사용자가 만든 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

## <a name="update-firewall-rule"></a>방화벽 규칙 업데이트 
PostgreSQL용 Azure Database 서버 이름 및 리소스 그룹 이름을 사용하여 서버에 기존 방화벽 규칙을 업데이트합니다. 기존 방화벽 규칙의 이름과 업데이트할 시작 IP 및 끝 IP 특성을 입력합니다.
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
성공하면 명령 출력은 업데이트한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.
> [!NOTE]
> 방화벽 규칙이 존재하지 않는 경우 업데이트 명령으로 생성됩니다.

## <a name="show-firewall-rule-details"></a>방화벽 규칙 세부 정보 표시
PostgreSQL용 Azure Database 서버 이름 및 리소스 그룹 이름을 사용하여 서버에서 기존 방화벽 규칙 세부 정보를 표시합니다. 기존 방화벽 규칙의 이름을 입력합니다.
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
성공하면 명령 출력은 지정한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력은 오류 메시지 텍스트를 대신 표시합니다.

## <a name="delete-firewall-rule"></a>방화벽 규칙 삭제
PostgreSQL용 Azure Database 서버 이름 및 리소스 그룹 이름을 사용하여 서버에서 기존 방화벽 규칙을 삭제합니다. 기존 방화벽 규칙의 이름을 제공합니다.
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
성공하면 출력은 없습니다. 실패하면 오류 메시지 텍스트가 반환됩니다.

## <a name="next-steps"></a>다음 단계
- 마찬가지로 웹 브라우저를 통해 [Azure Portal을 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-portal.md)를 확인할 수 있습니다.
- [PostgreSQL용 Azure Database 서버 방화벽 규칙](concepts-firewall-rules.md) 자세히 알아보기
- PostgreSQL용 Azure Database 서버 연결에 대한 도움말은 [PostgreSQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)를 참조하세요.

