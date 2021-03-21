---
title: Azure Cosmos DB에서 온라인 백업 및 주문형 데이터 복원.
description: 이 문서에서는 자동 백업, 주문형 데이터 복원의 작동 방식에 대해 설명 합니다. 연속 백업 모드와 정기적 백업 모드의 차이점에 대해서도 설명 합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525435"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB에서 자동 온라인 백업 및 주문형 데이터 복원
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 자동으로 데이터의 백업을 정기적으로 수행합니다. 자동 백업은 데이터베이스 작업의 성능이나 가용성에 영향을 주지 않고 수행됩니다. 모든 백업은 스토리지 서비스에 별도로 저장됩니다. 자동 백업은 Azure Cosmos 계정, 데이터베이스 또는 컨테이너를 실수로 삭제하거나 업데이트하고 나중에 데이터를 복구해야 하는 시나리오에서 유용합니다. 두 가지 백업 모드가 있습니다.

* **정기적 백업 모드** -이 모드는 모든 기존 계정에 대 한 기본 백업 모드입니다. 이 모드에서 백업은 정기적으로 수행 되며 지원 팀에 요청을 만들어 데이터를 복원 합니다. 이 모드에서는 계정에 대 한 백업 간격 및 보존을 구성 합니다. 최대 보존 기간은 한 달로 연장 됩니다. 최소 백업 간격은 1 시간 일 수 있습니다.  자세히 알아보려면 [정기적 백업 모드](configure-periodic-backup-restore.md) 문서를 참조 하세요.

* **연속 백업 모드** (현재 공개 미리 보기 상태)-Azure Cosmos DB 계정을 만드는 동안이 모드를 선택 합니다. 이 모드를 사용 하면 지난 30 일 이내에 모든 시점으로 복원할 수 있습니다. 자세한 내용은 [연속 백업 모드 소개](continuous-backup-restore-introduction.md), [Azure Portal](continuous-backup-restore-portal.md)를 사용 하 여 연속 백업 구성, [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)및 [리소스 관리자](continuous-backup-restore-template.md) 문서를 참조 하세요.

  > [!NOTE]
  > 연속 백업을 사용 하 여 새 계정을 구성 하는 경우 Azure Portal, PowerShell 또는 CLI를 통해 셀프 서비스 복원을 수행할 수 있습니다. 계정이 연속 모드로 구성 된 경우 다시 정기적 모드로 전환할 수 없습니다. 현재 정기적 백업 모드를 사용 하는 기존 계정은 연속 모드로 변경할 수 없습니다.  

## <a name="next-steps"></a>다음 단계

다음으로 계정에 대 한 정기적 및 지속적인 백업 모드를 구성 하 고 관리 하는 방법을 알아볼 수 있습니다.

* [정기적 백업 정책을 구성 하 고 관리](configure-periodic-backup-restore.md) 합니다.
* [연속 백업](continuous-backup-restore-introduction.md) 모드는 무엇입니까?
* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용 하 여 연속 백업을 구성 하 고 관리 합니다.
* 연속 백업 모드를 사용 하 여 데이터를 복원 하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md) 합니다.
