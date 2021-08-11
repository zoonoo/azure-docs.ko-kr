---
title: Azure Data Share 모니터링 방법
description: Azure Data Share에서 초대 상태, 공유 구독 및 스냅샷 기록을 모니터링하는 방법에 대해 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910477"
---
# <a name="monitor-azure-data-share"></a>Azure Data Share 모니터링  

이 문서에서는 Azure Data Share를 사용하여 데이터 공유를 모니터링하는 방법을 설명합니다. 데이터 공급자는 데이터 공유 관계의 다양한 측면을 모니터링할 수 있습니다. 데이터 소비자가 데이터 공유에 대한 초대를 수락했는지 여부 및 공유 구독을 만들고 데이터 사용을 시작했는지 여부와 같은 세부 정보를 모두 모니터링할 수 있습니다. 

데이터 소비자는 Azure 구독으로 트리거된 스냅샷을 모니터링할 수 있습니다. 

## <a name="monitor-invitation-status"></a>초대 상태 모니터링

보낸 공유 -> 초대로 이동하여 데이터 공유 초대의 상태를 확인합니다. 

![초대 상태](./media/invitation-status.png "초대 상태") 

초대는 다음과 같은 세 가지 상태가 될 수 있습니다.

* 보류 중 - 데이터 공유 수신자가 아직 초대를 수락하지 않았습니다.
* 수락됨 - 데이터 공유 수신자가 초대를 수락했습니다.
* 거부됨 - 데이터 공유 수신자가 초대를 거절했습니다.

> [!IMPORTANT]
> 이미 수락된 후에 초대를 삭제하는 것은 액세스 권한을 해지하는 것과 동일하지 않습니다. 후속 스냅샷이 데이터 소비자 스토리지 계정에 더 이상 복사되지 않도록 하려면 *공유 구독* 탭을 통해 액세스 권한을 해지해야 합니다. 

## <a name="monitor-share-subscriptions"></a>공유 구독 모니터링

보낸 공유 -> 공유 구독으로 이동하여 공유 구독의 상태를 확인합니다. 그러면 데이터 소비자가 초대를 수락한 후 만든 활성 구독에 대한 세부 정보가 제공됩니다. 공유 구독을 선택하고 *해지* 를 선택하여 데이터 소비자에 대한 향후 업데이트를 중지할 수 있습니다. 

## <a name="snapshot-history"></a>스냅샷 기록 

공유의 **기록** 탭에서 데이터 공급자의 데이터가 데이터 소비자의 데이터 저장소로 복사되는 경우를 확인할 수 있습니다. 각 스냅샷의 빈도, 기간 및 상태를 모니터링할 수 있습니다. 

![Azure Portal에서 보낸 공유를 보여 주는 스크린샷](./media/sent-shares.png "스냅샷 기록") 

실행 시작 날짜를 클릭하여 각 스냅샷 실행에 대한 자세한 정보를 볼 수 있습니다. 그런 다음, 각 데이터 세트의 상태를 클릭하여 전송된 데이터 양, 복사된 파일/레코드 수, 스냅샷 기간, 사용된 vCore 수 및 오류 메시지(있는 경우)를 확인합니다. 

최대 30일의 스냅샷 기록이 표시됩니다. 30일이 넘는 기록을 저장하고 확인해야 하는 경우 진단 설정을 활용할 수 있습니다.

## <a name="diagnostic-setting"></a>진단 설정

로그 데이터 또는 이벤트를 저장하도록 진단 설정을 구성할 수 있습니다. 모니터링 -> 진단 설정을 선택하고 **진단 설정 추가** 를 선택합니다. 관심 있는 로그 데이터 또는 이벤트와 저장하거나 보낼 위치를 선택합니다. 

![Azure Portal의 진단 설정 페이지를 보여 주는 스크린샷](./media/diagnostic-settings.png "진단 설정") 

## <a name="next-steps"></a>다음 단계 

[Azure Data Share 용어](terminology.md)에 대해 자세히 알아보기