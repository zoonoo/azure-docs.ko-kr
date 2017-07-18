---
title: "PostgreSQL용 Azure Database에서 서버를 복원하는 방법 | Microsoft Docs"
description: "이 문서에서는 Azure CLI 명령줄을 사용하여 PostgreSQL용 Azure Database에서 서버를 백업 및 복원하는 방법을 설명합니다."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Azure CLI를 사용하여 PostgreSQL용 Azure Database에서 서버를 백업 및 복원하는 방법

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [PostgreSQL용 Azure Database 서버 및 데이터베이스](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="backup-happens-automatically"></a>자동으로 수행되는 백업
PostgreSQL용 Azure Database를 사용할 경우 데이터베이스 서비스가 자동으로 5분마다 서비스 백업을 수행합니다. 

백업은 기본 계층을 사용하는 경우 7일, 표준 계층을 사용하는 경우 35일 동안 사용할 수 있습니다. 자세한 내용은 [PostgreSQL용 Azure Database 가격 책정 계층](concepts-service-tiers.md)을 참조하세요.

이 자동 백업 기능을 사용하면 서버 및 모든 데이터베이스를 새 서버에 이전 특정 시점으로 복원할 수도 있습니다.

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>Azure CLI를 사용하여 이전 시점으로 데이터베이스 복원
PostgreSQL용 Azure Database를 사용하면 서버를 다시 이전 특정 시점으로 복원할 수 있습니다. 복원된 데이터는 새 서버로 복사되고 기존 서버는 그대로 유지됩니다. 예를 들어 테이블이 오늘 정오에 실수로 삭제된 경우 정오 바로 전 시간으로 복원할 수 있습니다. 그런 다음 서버의 복원된 복사본에서 누락된 테이블 및 데이터를 검색합니다. 

[az postgres server restore](/cli/azure/postgres/server#restore) Azure CLI 명령을 사용하여 복원을 수행합니다.

### <a name="run-the-restore-command"></a>복원 명령 실행
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

`az postgres server restore` 명령에는 다음과 같은 매개 변수가 필요합니다.
| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  원본 서버가 존재하는 리소스 그룹입니다.  |
| name | mypgserver-restored | 복원 명령에 의해 만들어진 새 서버의 이름입니다. |
| restore-point-in-time | 2017-04-13T13:59:00Z | 복원하려는 지정 시간을 선택합니다. 이 날짜 및 시간은 원본 서버의 백업 보존 기간 내에 있어야 합니다. ISO8601 날자 및 시간 형식을 사용합니다. 예를 들어 `2017-04-13T05:59:00-08:00`과 같은 고유한 현지 표준 시간대 또는 UTC Zulu 형식 `2017-04-13T13:59:00Z`를 사용할 수도 있습니다. |
| source-server | mypgserver-20170401 | 복원을 수행하려는 원본 서버의 이름 또는 ID입니다. |

서버를 지정 시간으로 복원하면 새 서버가 만들어지고 사용자가 지정한 지정 시간의 원본 서버를 복사합니다. 복원된 서버에 대한 위치 및 가격 책정 계층 값은 원본 서버와 동일합니다. 명령은 동기화되고 복원할 서버를 대기한 후에 반환됩니다. 

복원이 완료되면 생성된 새 서버를 찾습니다. 데이터가 예상대로 복원되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
[PostgreSQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)

