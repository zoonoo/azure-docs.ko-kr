---
title: 서버 다시 시작-Azure CLI-Azure Database for PostgreSQL-단일 서버
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure Database for PostgreSQL 단일 서버를 다시 시작 하는 방법을 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f9881a4517f77587cecb2dcd04befaddb523965b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647650"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for PostgreSQL 단일 서버 다시 시작
이 항목에서는 Azure Database for PostgreSQL 서버를 다시 시작하는 방법을 설명합니다. 유지 관리를 위해 서버를 다시 시작해야 할 수 있지만 이 경우 서버가 해당 작업을 수행할 때 잠깐 가동이 중단됩니다.

서비스가 다른 작업 중이면 서버가 다시 시작되지 않습니다. 예를 들어, 서비스가 vCore 크기를 조정하는 것과 같이 이전에 요청된 작업을 처리할 수 있습니다.
 
다시 시작을 완료하는 데 필요한 시간은 PostgreSQL 복구 프로세스에 따라 달라집니다. 다시 시작 시간을 줄이려면 다시 시작 전에 서버에서 발생하는 작업의 양을 최소화하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for PostgreSQL 서버](quickstart-create-server-up-azure-cli.md)를 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="restart-the-server"></a>서버 다시 시작

다음 명령을 사용 하 여 서버를 다시 시작 합니다.

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL에서 매개 변수를 설정 하는 방법](howto-configure-server-parameters-using-cli.md) 에 대해 알아봅니다.
