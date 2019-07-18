---
title: Azure 데이터 공유 미리 보기를 모니터링 하는 방법
description: Azure 데이터 공유 미리 보기를 모니터링 하는 방법
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789099"
---
# <a name="monitor-azure-data-share-preview"></a>Azure 데이터 공유 미리 보기 모니터링 

이 문서에서는 Azure 데이터 공유 미리 보기를 사용 하 여 데이터 공유를 모니터링 하는 방법을 설명 합니다. 데이터 공급자는 관계를 공유 하 여 데이터의 다양 한 측면을 모니터링할 수 있습니다. 여부 데이터 소비자가 초대를 수락 하는 데이터 공유 여부는 공유 구독을 만들고 데이터 사용을 시작 하는 것은 물론 같은 세부 정보 모니터링을 모두 사용할 수 있습니다. 

데이터 소비자로 Azure 구독에 스냅숏이 트리거된를 모니터링할 수 있습니다. 

## <a name="monitor-invitation-status"></a>초대 상태 모니터링

뷰 상태 Sent 공유로 이동 하 여 데이터 공유 초대를 초대-> 합니다. 

![초대 상태](./media/invitation-status.png "초대 상태") 

초대에 저장할 수 있는 세 가지 상태는

* -보류 중인 데이터 공유 수신자가 아직 초대를 수락 하지.
* 수락 됨-데이터 공유 받는 사람이 초대를 수락 했습니다.
* 거부-데이터 공유 받는 사람이 초대를 거부 했습니다.

> [!IMPORTANT]
> 초대를 이미 승인 된 후 삭제 하면 해당 액세스를 해지 하 아닙니다. 데이터 소비자가 저장소 계정에 복사 되 고 이후 스냅숏 중지 하려는 경우 액세스를 통해 취소 해야 합니다 *구독을 공유* 탭 합니다. 

## <a name="monitor-share-subscriptions"></a>모니터 공유 구독

보기-> 공유 구독 전송 공유로 이동 하 여 공유 구독의 상태입니다. 이렇게 데이터 소비자에 게 초대를 수락한 후 만든 활성 구독에 대 한 정보를 제공 됩니다. 공유 구독을 선택 하 고 선택 하 여 데이터 소비자에 게 향후 업데이트를 중지할 수 있습니다 *Revoke*합니다. 

## <a name="snapshot-history"></a>스냅숏 기록 

내역 탭에는 있습니다 데이터 소비자의 테 넌 트에 복사 된 스냅숏을 확인할 수 있습니다. 빈도 및 스냅숏 간격 기간을 모니터링할 수 있습니다. 

![기록 스냅숏](./media/sent-shares.png "기록 스냅숏") 

실행된 시작 날짜를 클릭 하 여 실행 하는 각 스냅숏에 대 한 자세한 세부 정보를 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계 

자세한 내용은 [Azure 데이터 공유 용어](terminology.md)