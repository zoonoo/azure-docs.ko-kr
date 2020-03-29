---
title: 자동 증가 저장소 - Azure CLI - MySQL용 Azure 데이터베이스
description: 이 문서에서는 MySQL용 Azure 데이터베이스의 Azure CLI를 사용하여 저장소를 자동으로 늘리는 방법을 설명합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 44ce852aaf2ed5839650132c6eae95728c27dc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062626"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Azure CLI를 사용하여 MySQL 저장소에 대한 Azure 데이터베이스 자동 증가
이 문서에서는 워크로드에 영향을 주지 않고 증가하도록 MySQL 서버 저장소용 Azure 데이터베이스를 구성하는 방법에 대해 설명합니다.

저장소 [제한에 도달하는](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)서버는 읽기 전용으로 설정됩니다. 저장소 자동 증가가 활성화된 경우 100GB 미만의 프로비저닝된 저장소가 있는 서버의 경우 사용 가능한 저장소가 프로비저닝된 저장소의 1GB 또는 10% 미만이 면 즉시 프로비저닝된 저장소 크기가 5GB 증가합니다. 프로비저닝된 저장소가 100GB를 초과하는 서버의 경우 여유 저장소 공간이 프로비저닝된 저장소 크기의 5% 미만일 때 프로비저닝된 저장소 크기가 5% 증가합니다. [여기에](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) 명시된 최대 저장소 제한이 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [MySQL 서버용 Azure 데이터베이스](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 이 방법 가이드에서는 Azure CLI 버전 2.0 이상을 사용해야 합니다. 버전을 확인하려면 Azure CLI 명령 프롬프트에서 `az --version`을 입력합니다. 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="enable-mysql-server-storage-auto-grow"></a>MySQL 서버 스토리지 자동 증가 지원

다음 명령을 사용하여 기존 서버에서 서버가 저장소를 자동으로 확장하도록 설정합니다.

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

다음 명령을 사용하여 새 서버를 만드는 동안 서버가 저장소를 자동으로 성장하도록 설정합니다.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법에](howto-alert-on-metric.md)대해 알아봅니다.