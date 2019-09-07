---
title: Azure CLI를 사용 하 여 Azure Database for MySQL 저장소 자동 증가
description: 이 문서에서는 Azure Database for MySQL에서 Azure CLI를 사용 하 여 저장소 자동 증가를 사용 하도록 설정 하는 방법을 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: c9faaa5d011a32dfbaa5a841d3bce824f7ba5c9d
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390570"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for MySQL 저장소 자동 증가
이 문서에서는 워크 로드에 영향을 주지 않고 확장 되도록 Azure Database for MySQL server 저장소를 구성 하는 방법을 설명 합니다.

[저장소 제한에 도달](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)하는 서버는 읽기 전용으로 설정 됩니다. 저장소 자동 증가를 사용 하는 경우 프로 비전 된 저장소 수가 100 미만인 서버에 대해 사용 가능한 저장소가 프로 비전 된 저장소의 1gb 또는 10%를 초과 하는 즉시 프로 비전 된 저장소 크기는 5gb 씩 증가 합니다. 프로 비전 된 저장소 수가 100 GB를 넘는 서버에서는 사용 가능한 저장소 공간이 프로 비전 된 저장소 크기의 5% 미만이 면 프로 비전 된 저장소 크기가 5% 증가 합니다. [여기](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) 에 지정 된 대로 최대 저장소 제한이 적용 됩니다.

## <a name="prerequisites"></a>전제 조건
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 이 방법 가이드에서는 Azure CLI 버전 2.0 이상을 사용해야 합니다. 버전을 확인하려면 Azure CLI 명령 프롬프트에서 `az --version`을 입력합니다. 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="enable-mysql-server-storage-auto-grow"></a>MySQL server storage 자동 증가 사용

다음 명령을 사용 하 여 기존 서버에서 서버 자동 증가 저장소를 사용 하도록 설정 합니다.

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

다음 명령을 사용 하 여 새 서버를 만드는 동안 서버 자동 증가 저장소를 사용 하도록 설정 합니다.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>다음 단계

[메트릭에 대 한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.