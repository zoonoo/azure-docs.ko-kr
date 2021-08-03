---
title: SnapCenter에서 Oracle Database의 주문형 백업 만들기
description: Oracle BareMetal Infrastructure의 SnapCenter에서 Oracle Database의 주문형 백업을 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/07/2021
ms.openlocfilehash: 699c070a3eeca2904f9620ca5f12c95c509210ab
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579159"
---
# <a name="create-on-demand-backup-of-your-oracle-database-in-snapcenter"></a>SnapCenter에서 Oracle Database의 주문형 백업 만들기

이 문서에서는 SnapCenter에서 Oracle Database의 주문형 백업을 만드는 작업을 안내합니다. 

일단 [SnapCenter를 구성](configure-snapcenter-oracle-baremetal.md)하고 나면 리소스 그룹을 만들 때 입력한 일정에 따라 데이터 파일, 제어 파일 및 보관 로그의 백업이 계속됩니다. 그러나 일반적인 데이터베이스 보호의 일부로 주문형 백업을 원할 수도 있습니다.

## <a name="steps-to-create-an-on-demand-backup"></a>주문형 백업을 만드는 단계

1. 왼쪽 메뉴에서 **리소스** 를 선택합니다. 그런 다음, **보기** 옆의 드롭다운 메뉴에서 **리소스 그룹** 을 선택합니다. 만들려는 주문형 백업에 해당하는 리소스 그룹 이름을 선택합니다.

2. 오른쪽 위에서 **지금 백업** 을 선택합니다.

3. 리소스 그룹 및 보호 정책이 주문형 백업에 대해 적절한지 확인합니다. 이 백업을 확인하려면 **백업 후 확인** 확인란을 선택합니다. **백업** 을 선택합니다.

백업이 완료되면 **리소스** 아래의 백업 목록에서 사용할 수 있습니다. 백업한 리소스 그룹과 연결된 데이터베이스를 선택합니다. 이 백업은 보호 정책을 만들 때 설정한 주문형 보존 정책에 따라 보존됩니다.

## <a name="next-steps"></a>다음 단계

SnapCenter에서 Oracle Database를 복원하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle Database 복원](restore-oracle-database-baremetal.md)
