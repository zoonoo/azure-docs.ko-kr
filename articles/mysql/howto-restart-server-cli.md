---
title: 서버 다시 시작-Azure CLI-Azure Database for MySQL
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure Database for MySQL 서버를 다시 시작 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 146c531805ff22544797c44c8df32b0e5d1e83b0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774226"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for MySQL 서버 다시 시작
이 항목에서는 Azure Database for MySQL 서버를 다시 시작하는 방법을 설명합니다. 유지 관리를 위해 서버를 다시 시작해야 할 수 있지만 이 경우 서버가 해당 작업을 수행할 때 잠깐 가동이 중단됩니다.

서비스가 다른 작업 중이면 서버가 다시 시작되지 않습니다. 예를 들어, 서비스가 vCore 크기를 조정하는 것과 같이 이전에 요청된 작업을 처리할 수 있습니다.

다시 시작을 완료하는 데 필요한 시간은 MySQL 복구 프로세스에 따라 달라집니다. 다시 시작 시간을 줄이려면 다시 시작 전에 서버에서 발생하는 작업의 양을 최소화하는 것이 좋습니다.

## <a name="prerequisites"></a>전제 조건
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for MySQL 서버](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 이 방법 가이드에서는 Azure CLI 버전 2.0 이상을 사용해야 합니다. 버전을 확인하려면 Azure CLI 명령 프롬프트에서 `az --version`을 입력합니다. 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.


## <a name="restart-the-server"></a>서버 다시 시작

다음 명령을 사용 하 여 서버를 다시 시작 합니다.

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>다음 단계

[Azure Database for MySQL에서 매개 변수를 설정 하는 방법](howto-configure-server-parameters-using-cli.md) 에 대해 알아봅니다.