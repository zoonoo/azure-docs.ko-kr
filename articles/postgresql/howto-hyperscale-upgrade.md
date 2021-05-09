---
title: 서버 그룹 업데이트 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 이 문서는 Azure Database for PostgreSQL – 하이퍼스케일(Citus)에서 PostgreSQL 및 Citus를 업그레이드하는 방법을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518901"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>하이퍼스케일(Citus) 서버 그룹 업그레이드

이 지침은 모든 서버 그룹 노드에서 새로운 주 버전의 PostgreSQL로 업그레이드하는 방법을 설명합니다.

## <a name="test-the-upgrade-first"></a>먼저 업그레이드 테스트

하이퍼스케일(Citus)은 Citus 확장을 포함한 [데이터베이스 확장](concepts-hyperscale-extensions.md)도 업그레이드하므로 PostgreSQL를 업그레이드하면 생각보다 더 많은 변경이 발생할 수 있습니다.
프로덕션 환경을 업그레이드하기 전에 새로운 PostgreSQL 및 Citus 버전을 사용하여 애플리케이션을 테스트하는 것이 좋습니다.

편리한 테스트 방법은 [특정 시점 복원](concepts-hyperscale-backup.md#restore)을 사용하여 서버 그룹의 복사본을 만드는 것입니다. 복사본을 업그레이드하고 이에 대해 애플리케이션을 테스트합니다. 모든 것이 제대로 작동하는지 확인한 후 원래 서버 그룹을 업그레이드합니다.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Azure Portal에서 서버 그룹 업그레이드

1. 하이퍼스케일(Citus) 서버 그룹의 **개요** 섹션에서 **업그레이드** 단추를 선택합니다.
1. PostgreSQL 및 Citus의 현재 버전을 보여주는 대화 상자가 나타납니다.
   **업그레이드 대상** 목록에서 새 PostgreSQL 버전을 선택합니다.
1. **업그레이드 후 Citus 버전** 의 값이 필요한지 확인합니다.
   이 값은 선택한 PostgreSQL 버전에 따라 변경됩니다.
1. 계속하려면 **업그레이드** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [지원되는 PostgreSQL 버전](concepts-hyperscale-versions.md)에 대해 알아봅니다.
* 하이퍼스케일(Citus) 서버 그룹에서 각 PostgreSQL 버전으로 패키징된 [확장](concepts-hyperscale-extensions.md)을 확인합니다.
