---
title: 자동 증가 저장소-Azure CLI-Azure Database for PostgreSQL-단일 서버
description: 이 문서에서는 Azure Database for PostgreSQL 단일 서버에서 Azure CLI를 사용 하 여 저장소 자동 증가를 구성 하는 방법을 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b6f3875af4c7f0eace327d810c632a6bb217092
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534197"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for PostgreSQL 저장소 자동 증가-단일 서버
이 문서에서는 워크 로드에 영향을 주지 않고 확장 되도록 Azure Database for PostgreSQL server 저장소를 구성 하는 방법을 설명 합니다.

[저장소 제한에 도달](./concepts-pricing-tiers.md#reaching-the-storage-limit)하는 서버는 읽기 전용으로 설정 됩니다. 저장소 자동 증가를 사용 하는 경우 프로 비전 된 저장소 수가 100 미만인 서버에 대해 사용 가능한 저장소가 프로 비전 된 저장소의 1gb 또는 10%를 초과 하는 즉시 프로 비전 된 저장소 크기는 5gb 씩 증가 합니다. 프로 비전 된 저장소 수가 100 GB를 넘는 서버에서는 사용 가능한 저장소 공간이 프로 비전 된 저장소 크기의 5% 미만이 면 프로 비전 된 저장소 크기가 5% 증가 합니다. [여기](./concepts-pricing-tiers.md#storage) 에 지정 된 대로 최대 저장소 제한이 적용 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Database for PostgreSQL 서버가](quickstart-create-server-database-azure-cli.md)필요 합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="enable-postgresql-server-storage-auto-grow"></a>PostgreSQL 서버 저장소 자동 증가 사용

다음 명령을 사용 하 여 기존 서버에서 서버 자동 증가 저장소를 사용 하도록 설정 합니다.

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

다음 명령을 사용 하 여 새 서버를 만드는 동안 서버 자동 증가 저장소를 사용 하도록 설정 합니다.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>다음 단계

[메트릭에 대 한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.