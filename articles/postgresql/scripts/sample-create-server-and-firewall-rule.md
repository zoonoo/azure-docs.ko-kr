---
title: "Azure CLI 스크립트 - PostgreSQL용 Azure Database 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - PostgreSQL 서버용 Azure Database를 만들고 서버 수준 방화벽 규칙을 구성합니다."
services: postgresql
author: salonisonpal
ms.author: salonis
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
ms.openlocfilehash: 0f61358aa655a44ac7982c80eb1d796667c47bf4
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI를 사용하여 PostgreSQL 서버용 Azure Database 만들기 및 방화벽 규칙 구성
이 샘플 CLI 스크립트는 PostgreSQL 서버용 Azure Database를 만들고 서버 수준 방화벽 규칙을 구성합니다. 스크립트가 성공적으로 실행되면 모든 Azure 서비스 및 구성된 IP 주소에서 PostgreSQL 서버에 액세스할 수 있습니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트
```azurecli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a PostgreSQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az postgres server create \
--name mypgserver-20170401 \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az postgres server firewall-rule create \
--resource-group myresourcegroup \
--server mypgserver-20170401 \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>배포 정리
스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>다음 단계
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.
- PostgreSQL용 Azure Database의 추가 CLI 스크립트 샘플은 [PostgreSQL용 Azure Database 설명서](../sample-scripts-azure-cli.md)에서 찾을 수 있습니다.

