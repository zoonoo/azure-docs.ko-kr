---
title: 자동 증가 스토리지 - Azure CLI - Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB에서 Azure CLI를 사용하여 자동 증가 스토리지를 사용하도록 설정하는 방법을 설명합니다.
author: rothja
ms.author: jroth
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 494f892bd4278192d78fea4fb6793d3090ba88ec
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552991"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Azure CLI를 사용하는 자동 증가 Azure Database for MariaDB 스토리지
이 문서에서는 워크로드에 영향을 주지 않고 확장되도록 Azure Database for MariaDB 서버 스토리지를 구성하는 방법을 설명합니다.

[스토리지 제한에 도달](concepts-pricing-tiers.md#reaching-the-storage-limit)하는 서버는 읽기 전용으로 설정됩니다. 프로비전된 스토리지가 100GB 미만인 서버에 대해 스토리지 자동 증가가 활성화된 경우 프로비전된 스토리지 크기는 사용 가능한 스토리지가 1GB보다 크거나 프로비전된 스토리지의 10%보다 적으면 즉시 5GB씩 증가합니다. 프프로비전된 스토리지가 100GB를 초과하는 서버의 경우 사용 가능한 스토리지 공간이 프로비전된 스토리지 크기의 5% 미만이면 프로비전된 스토리지 크기가 5%씩 증가합니다. [여기](concepts-pricing-tiers.md#storage)에 지정된 대로 최대 스토리지 제한이 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 가이드를 완료하려면 다음이 필요합니다.

- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-cli.md)가 필요합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="enable-mariadb-server-storage-auto-grow"></a>MariaDB 서버 스토리지 자동 증가 사용

다음 명령을 사용하여 기존 서버에서 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

다음 명령을 사용하여 새 서버를 만드는 동안 서버 자동 증가 스토리지를 사용하도록 설정합니다.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법](howto-alert-metric.md)에 대해 알아봅니다.
