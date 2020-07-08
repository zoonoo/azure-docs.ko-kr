---
title: 여러 작업 영역에서 한 번에 Azure 센티널 인시던트 사용 Microsoft Docs
description: Azure 센티널에서 동시에 여러 작업 영역에 있는 인시던트를 확인 하는 방법입니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83124184"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>여러 작업 영역에서 한 번에 인시던트 사용 

 Azure 센티널의 기능을 최대한 활용 하려면 단일 작업 영역 환경을 사용 하는 것이 좋습니다. 그러나 일부 경우에는 여러 테 넌 트에 걸친 [MSSP (관리 되는 보안 서비스 공급자)](./multiple-tenants-service-providers.md) 및 고객의 경우와 같이 여러 작업 영역을 포함 해야 하는 몇 가지 사용 사례가 있습니다. **여러 작업 영역 보기** 를 사용 하면 테 넌 트 간에도 여러 작업 영역에서 동시에 보안 인시던트를 보고 사용 하 여 조직의 보안 응답성을 완벽 하 게 관리 하 고 제어할 수 있습니다.

## <a name="entering-multiple-workspace-view"></a>여러 작업 영역 보기 입력

Azure 센티널을 열면 선택한 모든 테 넌 트 및 구독에서 액세스 권한이 있는 모든 작업 영역 목록이 표시 됩니다. 각 작업 영역 이름의 왼쪽에는 확인란이 있습니다. 단일 작업 영역의 이름을 클릭 하면 해당 작업 영역으로 이동 합니다. 여러 작업 영역을 선택 하려면 해당 확인란을 모두 클릭 한 다음 페이지 맨 위에 있는 **여러 작업 영역 보기** 단추를 클릭 합니다.

> [!IMPORTANT]
> 현재 여러 작업 영역 보기에서 동시에 표시 되는 최대 10 개의 작업 영역을 지원 합니다. 
> 
> 작업 영역을 10 개 이상 선택 하면 경고 메시지가 표시 됩니다.

작업 영역 목록에서 각 작업 영역과 연결 된 디렉터리, 구독, 위치 및 리소스 그룹을 볼 수 있습니다. 디렉터리는 테 넌 트에 해당 합니다.

   ![여러 작업 영역 선택](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>인시던트 작업

**여러 작업 영역 보기**에서는 현재 **인시던트** 화면만 사용할 수 있습니다. 일반적인 **인시던트** 화면과 같은 대부분의 방법으로 표시 되 고 함수를 검색 합니다. 그러나 다음과 같은 몇 가지 중요 한 차이점이 있습니다.

   ![여러 작업 영역에서 인시던트 보기](./media/multiple-workspace-view/incidents.png)

- 페이지 *열기 인시던트*, *새 인시던트*, *진행*중 등의 맨 위에 있는 카운터-선택한 모든 작업 영역에 대 한 숫자를 통칭 하 여 표시 합니다.

- 선택한 모든 작업 영역 및 디렉터리 (테 넌 트)의 인시던트를 하나의 통합 된 목록에서 볼 수 있습니다. 일반 **인시던트** 화면의 필터 외에도 작업 영역 및 디렉터리를 기준으로 목록을 필터링 할 수 있습니다.

- 인시던트를 선택한 모든 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다. 일부 작업 영역에 대 한 읽기 권한만 있는 경우 해당 작업 영역에서 인시던트를 선택 하면 경고 메시지가 표시 됩니다. 이러한 인시던트 나 선택한 다른 사용자에 대 한 권한이 있는 경우에도 해당 인시던트를 수정할 수 없습니다.

- 단일 인시던트를 선택 하 고 **전체 세부 정보 보기** 또는 **조사**를 클릭 하는 경우에는 해당 인시던트의 작업 영역의 데이터 컨텍스트에서 시작 하 여 다른 사용자가 없는 것입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 여러 Azure 센티널 작업 영역에서 동시에 인시던트를 보고 사용 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

