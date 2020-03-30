---
title: Azure 데이터 공유를 모니터링하는 방법
description: Azure 데이터 공유에서 초대 상태 모니터링, 구독 공유 및 스냅숏 기록 모니터링 방법 알아보기
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490513"
---
# <a name="monitor-azure-data-share"></a>Azure Data Share 모니터링  

이 문서에서는 Azure 데이터 공유를 사용하여 데이터 공유를 모니터링하는 방법을 설명합니다. 데이터 공급자는 데이터 공유 관계의 다양한 측면을 모니터링할 수 있습니다. 데이터 소비자가 데이터 공유초대를 수락했는지 여부, 공유 구독을 만들고 데이터를 사용하기 시작했는지 여부 와 같은 세부 정보는 모두 모니터링할 수 있습니다. 

데이터 소비자는 Azure 구독에 트리거된 스냅숏을 모니터링할 수 있습니다. 

## <a name="monitor-invitation-status"></a>초대 상태 모니터링

보낸 공유 -> 초대로 이동하여 데이터 공유 초대의 상태를 확인합니다. 

![초대 상태](./media/invitation-status.png "초대 상태") 

초대장이 있을 수 있는 상태는 세 가지가 있습니다.

* 보류 중 - 데이터 공유 받는 사람이 아직 초대를 수락하지 않았습니다.
* 수락됨 - 데이터 공유 수신자가 초대를 수락했습니다.
* 거부됨 - 데이터 공유 받는 사람이 초대를 거부했습니다.

> [!IMPORTANT]
> 초대가 수락된 후 삭제한 경우 액세스 취소와 동일하지 않습니다. 향후 스냅숏이 데이터 소비자 저장소 계정에 복사되지 않도록 하려면 *구독 공유* 탭을 통해 액세스를 취소해야 합니다. 

## <a name="monitor-share-subscriptions"></a>공유 구독 모니터링

보낸 공유 구독 -> 공유 구독으로 이동하여 공유 구독의 상태를 봅니다. 이렇게 하면 초대를 수락한 후 데이터 소비자가 만든 활성 구독에 대한 세부 정보를 제공합니다. 공유 구독을 선택하고 *해지를*선택하여 데이터 소비자에 대한 향후 업데이트를 중지할 수 있습니다. 

## <a name="snapshot-history"></a>스냅샷 기록 

기록 탭에서 데이터 소비자의 테넌트에 복사된 스냅샷을 볼 수 있습니다. 각 스냅샷 간격의 빈도와 기간을 모니터링할 수 있습니다. 

![스냅샷 기록](./media/sent-shares.png "스냅샷 기록") 

실행 시작 날짜를 클릭하여 실행되는 각 스냅숏에 대한 자세한 내용을 볼 수 있습니다. 

스냅샷 기록은 기본적으로 최대 30일까지 표시됩니다. 30일 이상의 기록 가치가 있는 기록이 필요한 경우 모니터링 -> 진단 설정으로 이동하여 **진단 추가 설정을**선택합니다. 이러한 로그를 저장할 저장소 계정을 선택해야 합니다. 

![스냅샷 기록](./media/diagnostic-settings.png "진단 설정") 

## <a name="next-steps"></a>다음 단계 

[Azure 데이터 공유 용어에](terminology.md) 대해 자세히 알아보기