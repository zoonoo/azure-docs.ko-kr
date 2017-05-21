---
title: "Azure CLI 스크립트 - PostgreSQL용 Azure Database 확장 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 메트릭을 쿼리한 후에 PostgreSQL 서버용 Azure Database를 다양한 성능 수준으로 확장합니다."
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
ms.date: 04/30/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 36134ca1a899c25ab896577815ce9108cae0d563
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Azure CLI를 사용하여 단일 PostgreSQL 서버 모니터링 및 확장
이 샘플 CLI 스크립트는 메트릭을 쿼리한 후에 PostgreSQL 서버용 단일 Azure Database를 다양한 성능 수준으로 확장합니다. 

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

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az postgres server update \
--resource-group myresourcegroup \
--name mypgserver-20170401 \
--compute-units 100
```

## <a name="clean-up-deployment"></a>배포 정리
스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>다음 단계
- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.
- PostgreSQL용 Azure Database의 추가 CLI 스크립트 샘플은 [PostgreSQL용 Azure Database 설명서](../sample-scripts-azure-cli.md)에서 찾을 수 있습니다.
- 확장에 대한 자세한 내용은 [서비스 계층](../concepts-service-tiers.md) 및 [계산 단위 및 저장 단위](../concepts-compute-unit-and-storage.md)를 참조하세요.
