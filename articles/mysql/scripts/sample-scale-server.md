---
title: "MySQL 서버용 Azure Database를 확장하는 Azure CLI 샘플 | Microsoft Docs"
description: "이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 MySQL 서버용 Azure Database를 다양한 성능 수준으로 확장합니다."
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
ms.openlocfilehash: c54262218e6174dc85905cb067c334cc6a401946
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI를 사용하여 MySQL용 Azure Database 모니터링 및 확장
이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 MySQL 서버용 단일 Azure Database를 다양한 성능 수준으로 확장합니다.

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

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresource/providers/Microsoft.DBforMySQL/servers/mysqlserver4demo" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az mysql server update \
--resource-group myresource \
--name mysqlserver4demo \
--compute-units 100
```

## <a name="clean-up-deployment"></a>배포 정리
스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.
```azurecli
az group delete --name myresource
```

## <a name="next-steps"></a>다음 단계
[MySQL용 Azure Database에 대한 Azure CLI 샘플](../sample-scripts-azure-cli.md)

