---
title: 자동 증가 스토리지 - Azure CLI - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에서 Azure CLI를 사용하여 자동 증가 스토리지를 사용하도록 설정하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3126adbae6cb719bf19dc549e83dfc55af56f7d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110035"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Azure CLI를 사용하는 자동 증가 Azure Database for MySQL 스토리지
이 문서에서는 워크로드에 영향을 주지 않고 확장되도록 Azure Database for MySQL 서버 스토리지를 구성할 수 있는 방법을 설명합니다.

[스토리지 제한에 도달](./concepts-pricing-tiers.md#reaching-the-storage-limit)하는 서버는 읽기 전용으로 설정됩니다. 프로비저닝된 스토리지가 100GB 미만인 서버에 대해 스토리지 자동 증가가 사용하도록 설정된 경우, 프로비저닝된 스토리지 크기는 사용 가능한 스토리지가 1GB보다 크거나 프로비저닝된 스토리지의 10%보다 적으면 즉시 5GB씩 증가합니다. 프로비저닝된 스토리지가 100GB를 초과하는 서버의 경우, 사용 가능한 스토리지 공간이 프로비저닝된 스토리지 크기의 5% 미만이면 프로비저닝된 스토리지 크기가 5%씩 증가합니다. [여기](./concepts-pricing-tiers.md#storage)에 지정된 대로 최대 스토리지 제한이 적용됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-cli.md)가 필요합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="enable-mysql-server-storage-auto-grow"></a>MySQL 서버 스토리지 자동 증가 사용

다음 명령을 사용하여 기존 서버에서 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

다음 명령을 사용하여 새 서버를 만드는 동안 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>다음 단계

[메트릭 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.