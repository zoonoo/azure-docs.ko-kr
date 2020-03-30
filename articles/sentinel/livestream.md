---
title: Azure Sentinel에서 실시간 검색을 사용하여 위협 감지| 마이크로 소프트 문서
description: 이 문서에서는 Azure Sentinel에서 라이브스트림 사냥을 사용하여 데이터를 추적하는 방법을 설명합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582129"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Azure Sentinel에서 실시간 검색을 사용하여 위협 감지

> [!IMPORTANT]
> Azure Sentinel에서 라이브 스트림 을 사냥하는 것은 현재 공개 미리 보기 중이며 점차적으로 테넌트에 롤아웃됩니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


실시간 검색어를 사용하여 이벤트가 발생할 때 새로 생성된 쿼리를 테스트하고, 일치 가 발견될 때 세션에서 알림을 받고, 필요한 경우 조사를 시작할 수 있는 대화형 세션을 만듭니다. Log Analytics 쿼리를 사용하여 실시간 스트리밍 세션을 빠르게 만들 수 있습니다.

- **이벤트가 발생할 때 새로 만든 쿼리 테스트**
    
    이벤트에 적극적으로 적용되는 현재 규칙에 충돌하지 않고 쿼리를 테스트하고 조정할 수 있습니다. 이러한 새 쿼리가 예상대로 작동하는지 확인한 후에는 세션을 경고로 상승시키는 옵션을 선택하여 사용자 지정 경고 규칙으로 쉽게 승격할 수 있습니다.

- **위협이 발생할 때 알림을 받습니다.**
    
    위협 데이터 피드를 집계된 로그 데이터와 비교하고 일치가 발생하면 알림을 받을 수 있습니다. 위협 데이터 피드는 잠재적 또는 현재 위협과 관련된 지속적인 데이터 스트림이므로 알림은 조직에 대한 잠재적 위협을 나타낼 수 있습니다. 사용자 지정 경고 규칙을 유지 관리하는 오버헤드 없이 잠재적인 문제에 대한 알림을 받으면 사용자 지정 경고 규칙 대신 라이브스트림 세션을 만듭니다.

- **조사 시작**
    
    호스트 또는 사용자와 같은 자산과 관련된 활성 조사가 있는 경우 해당 자산에서 발생하는 로그 데이터의 특정(또는 임의) 활동을 볼 수 있습니다. 해당 활동이 발생하면 알림을 받을 수 있습니다.


## <a name="create-a-livestream-session"></a>실시간 스트림 세션 만들기

기존 검색 쿼리에서 라이브 스트림 세션을 만들거나 세션을 처음부터 만들 수 있습니다.

1. Azure 포털에서 **Sentinel** > **위협 관리** > **사냥으로**이동합니다.

2. 사냥 쿼리에서 라이브스트림 세션을 만들려면 다음을 수행합니다.
    
    1. **쿼리** 탭에서 사용할 검색 쿼리를 찾습니다.
    2. 쿼리를 마우스 오른쪽 단추로 클릭하고 **라이브스트림에 추가를**선택합니다. 예를 들어:
    
    > [!div class="mx-imgBorder"]
    > ![Azure Sentinel 사냥 쿼리에서 라이브 스트림 세션 만들기](./media/livestream/livestream-from-query.png)

3. 처음부터 라이브 스트림 세션을 만들려면 다음을 수행하십시오. 
    
    1. **라이브스트림** 탭 선택
    2. **라이브스트림으로 이동을 선택합니다.**
    
4. **라이브스트림** 창에서:
    
    - 쿼리에서 실시간 스트리밍을 시작한 경우 쿼리를 검토하고 변경하려는 내용을 지정합니다.
    - 처음부터 라이브스트림을 시작한 경우 쿼리를 만듭니다. 

5. 명령 모음에서 **재생을** 선택합니다.
    
    명령 모음 아래의 상태 표시줄은 라이브스트림 세션이 실행 중인지 일시 중지되었는지 여부를 나타냅니다. 다음 예제에서는 세션이 실행되고 있습니다.
    
    > [!div class="mx-imgBorder"]
    > ![Azure Sentinel 사냥에서 라이브 스트림 세션 만들기](./media/livestream/livestream-session.png)

6. 명령 모음에서 **저장을** 선택합니다.
    
    **일시 중지를**선택하지 않으면 Azure 포털에서 로그아웃할 때까지 세션이 계속 실행됩니다.

## <a name="view-your-livestream-sessions"></a>라이브스트림 세션 보기

1. Azure 포털에서 **Sentinel** > **위협 관리** > **사냥** > **라이브스트림** 탭으로 이동합니다.

2. 보거나 편집할 라이브스트림 세션을 선택합니다. 예를 들어:
    
    > [!div class="mx-imgBorder"]
    > ![Azure Sentinel 사냥 쿼리에서 라이브 스트림 세션 만들기](./media/livestream/livestream-tab.png)
    
    선택한 라이브스트림 세션이 열리면 재생, 일시 중지, 편집 등이 열립니다.

## <a name="receive-notifications-when-new-events-occur"></a>새 이벤트가 발생할 때 알림 수신

새 이벤트에 대한 라이브스트림 알림은 Azure 포털 알림을 사용하기 때문에 Azure 포털을 사용할 때마다 이러한 알림이 표시됩니다. 예를 들어:

![라이브 스트림에 대 한 Azure 포털 알림](./media/livestream/notification.png)

알림을 선택하여 **라이브스트림** 창을 엽니다.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>실시간 스트림 세션을 경고로 승격

관련 라이브스트림 세션의 명령 모음에서 **경고하도록 승격을** 선택하여 라이브스트림 세션을 새 경고로 승격할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![실시간 스트림 세션을 경고로 승격](./media/livestream/elevate-to-alert.png)

이 작업은 라이브스트림 세션과 연결된 쿼리로 미리 채워진 규칙 만들기 마법사를 엽니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 사냥 라이브스트림을 사용하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협에 대한 사전 예방적 사냥](hunting.md)
- [노트북을 사용하여 자동화된 사냥 캠페인 실행](notebooks.md)
