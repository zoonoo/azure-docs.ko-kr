---
title: "MySQL 서버용 단일 Azure Database를 만들고 방화벽 규칙을 구성하는 Azure CLI 샘플 | Microsoft Docs"
description: "이 샘플 CLI 스크립트는 MySQL 서버용 Azure Database를 만들고 서버 수준 방화벽 규칙을 구성합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI를 사용하여 MySQL 서버 만들기 및 방화벽 규칙 구성
이 샘플 CLI 스크립트는 MySQL 서버용 Azure Database를 만들고 서버 수준 방화벽 규칙을 구성합니다. 스크립트가 성공적으로 실행되면 모든 Azure 서비스 및 구성된 IP 주소에서 MySQL 서버에 액세스할 수 있습니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>배포 정리
 스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거합니다.
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| **명령** | **참고 사항** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| az mysql server create | 데이터베이스를 호스팅하는 MySQL 서버를 만듭니다. |
| az mysql server firewall create | 입력한 IP 주소 범위의 서버에서 서버 및 서버의 데이터베이스에 액세스할 수 있도록 방화벽 규칙을 만듭니다. |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
[MySQL용 Azure Database에 대한 Azure CLI 샘플](../sample-scripts-azure-cli.md)

