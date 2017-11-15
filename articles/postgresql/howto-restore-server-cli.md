---
title: "PostgreSQL용 Azure Database에서 서버를 백업 및 복원하는 방법 | Microsoft Docs"
description: "Azure CLI를 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법을 알아봅니다."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 0cfce63b1523f939dc2d706dba771e56ce9ccd6c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-by-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법

PostgreSQL용 Azure Database를 사용하여 7~35일 이전 날짜로 서버 데이터베이스를 복원합니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [PostgreSQL용 Azure Database 서버 및 데이터베이스](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Azure 명령줄 인터페이스를 로컬로 설치하여 사용하는 경우 이 방법 가이드를 사용하려면 Azure 명령줄 인터페이스 버전 2.0 이상을 사용해야 합니다. 버전을 확인하려면 Azure 명령줄 인터페이스 명령 프롬프트에서 `az --version`을 입력합니다. 설치하거나 업그레이드하려면 [Azure 명령줄 인터페이스 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="backup-happens-automatically"></a>자동으로 수행되는 백업
PostgreSQL용 Azure Database를 사용하는 경우 데이터베이스 서비스에서 자동으로 5분마다 서비스 백업을 수행합니다. 

기본 계층의 경우 백업은 7일간 사용할 수 있습니다. 표준 계층의 경우 백업은 35일간 사용할 수 있습니다. 자세한 내용은 [PostgreSQL용 Azure Database 가격 책정 계층](concepts-service-tiers.md)을 참조하세요.

이 자동 백업 기능을 사용하여 서버 및 해당 데이터베이스를 이전 날짜 또는 특정 시점으로 복원할 수 있습니다.

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>Azure 명령줄 인터페이스를 사용하여 이전 특정 시점으로 데이터베이스 복원
PostgreSQL용 Azure Database를 사용하여 서버를 이전 특정 시점으로 복원할 수 있습니다. 복원된 데이터는 새 서버에 복사되고 기존 서버는 그대로 유지됩니다. 예를 들어 테이블이 오늘 정오에 실수로 삭제된 경우 정오 바로 전 시간으로 복원할 수 있습니다. 그런 다음 서버의 복원된 복사본에서 누락된 테이블 및 데이터를 검색할 수 있습니다. 

서버를 복원하려면 Azure 명령줄 인터페이스 [az postgres server restore](/cli/azure/postgres/server#restore) 명령을 사용합니다.

### <a name="run-the-restore-command"></a>복원 명령 실행

서버를 복원하려면 Azure 명령줄 인터페이스 명령 프롬프트에서 다음 명령을 입력합니다.

```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

`az postgres server restore` 명령에는 다음과 같은 매개 변수가 필요합니다.
| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  원본 서버가 있는 리소스 그룹입니다.  |
| name | mypgserver-restored | 복원 명령에 의해 만들어진 새 서버의 이름입니다. |
| restore-point-in-time | 2017-04-13T13:59:00Z | 복원할 특정 시점을 선택합니다. 이 날짜 및 시간은 원본 서버의 백업 보존 기간 내에 있어야 합니다. ISO8601 날자 및 시간 형식을 사용합니다. 예를 들어 `2017-04-13T05:59:00-08:00`과 같이 현지 표준 시간대를 사용할 수 있습니다. UTC Zulu 형식을 사용할 수도 있습니다(예: `2017-04-13T13:59:00Z`). |
| source-server | mypgserver-20170401 | 복원을 수행하려는 원본 서버의 이름 또는 ID입니다. |

서버를 이전 특정 시점으로 복원하는 경우 새 서버가 만들어집니다. 지정된 특정 시점의 원본 서버 및 해당 데이터베이스가 새 서버에 복사됩니다.

복원된 서버에 대한 위치 및 가격 책정 계층 값은 원본 서버와 같게 유지됩니다. 

`az postgres server restore` 명령은 동기식입니다. 서버가 복원된 후 다른 특정 시점에 대해 이 프로세스를 반복하기 위해 이 서버를 다시 사용할 수 있습니다. 

복원 프로세스가 완료된 후 새 서버를 찾아 데이터가 예상대로 복원되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
[PostgreSQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)
