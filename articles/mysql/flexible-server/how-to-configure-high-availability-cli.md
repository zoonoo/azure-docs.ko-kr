---
title: 영역 중복 고가용성 서버 관리 - Azure CLI - Azure Database for MySQL 유연한 서버
description: 이 문서에서는 Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버에서 영역 중복 고가용성을 구성하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 6f441572ea57789f6700194a24e7f1af663d0f4f
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109750360"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Azure CLI를 사용한 Azure Database for MySQL 유연한 서버의 영역 중복 고가용성 관리

> [!NOTE]
> Azure Database for MySQL - 유연한 서버는 공개 미리 보기로 제공됩니다. 

이 문서에서는 유연한 서버에서 서버를 만들 당시 영역 중복 고가용성 구성을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다. 서버를 만든 후에도 영역 중복 고가용성을 사용하지 않도록 설정할 수 있습니다. 서버를 만든 후에는 영역 중복 고가용성을 사용할 수 없습니다.

고가용성 기능은 서로 다른 영역에서 물리적으로 분리되는 주 복제본과 대기 복제본을 프로비전합니다. 자세한 정보는 [고가용성 개념 설명서](./concepts/../concepts-high-availability.md)를 참조하세요. 고가용성을 사용하거나 사용하지 않도록 설정해도 VNET 구성, 방화벽 설정 및 백업 보존을 비롯한 다른 설정은 변경되지 않습니다. 고가용성을 사용하지 않도록 설정해도 애플리케이션 연결 및 작업에는 영향을 주지 않습니다.

> [!IMPORTANT]
> 영역 중복 고가용성은 제한된 지역 집합에서 사용할 수 있습니다. 지원되는 지역을 [여기](./overview.md#azure-regions)에서 검토하세요. 

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- 최신 버전으로 Azure CLI를 설치하거나 업그레이드합니다. [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
-  [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure 계정에 로그인합니다. Azure 계정에 대한 **구독 ID** 를 참조하는 **id** 속성을 기록해 둡니다.

    ```azurecli-interactive
    az login
    ````

- 구독이 여러 개인 경우 ```az account set``` 명령을 사용하여 서버를 만들려는 적절한 구독을 선택합니다.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>서버를 만드는 동안 고가용성 사용
고가용성을 사용하여 범용 또는 메모리 최적화 가격 책정 계층을 사용하는 서버만을 만들 수 있습니다. 만든 시간 동안에 대해서만 서버에 대해 고가용성을 사용하도록 설정할 수 있습니다.

**사용법:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**예:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>고가용성 사용 안 함

[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) 명령을 사용하여 고가용성을 사용하지 않도록 설정할 수 있습니다. 고가용성을 사용하여 서버를 만든 경우에만 고가용성을 사용하지 않도록 설정할 수 있습니다. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**예:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md)에 대한 자세한 정보
-   [영역 중복 고가용성](./concepts-high-availability.md)에 대한 자세한 정보