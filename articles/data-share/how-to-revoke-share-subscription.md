---
title: Azure Data Share에서 공유 구독 철회
description: Azure Data Share를 사용하여 수신자의 공유 구독을 철회하는 방법을 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87511856"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Azure Data Share에서 소비자의 공유 구독을 철회하는 방법

이 문서에서는 Azure Data Share를 사용하여 소비자 한 명 이상의 공유 구독을 철회하는 방법을 설명합니다. 구독을 철회하여 소비자가 더 이상 스냅샷을 트리거하지 않도록 합니다. 소비자가 아직 스냅샷을 트리거하지 않은 경우 공유 구독이 철회되면 소비자에게 데이터가 수신되지 않습니다. 이전에 스냅샷을 트리거한 적이 있으면 최신 데이터가 계정에 유지됩니다.

## <a name="navigate-to-a-sent-data-share"></a>전송된 데이터 공유로 이동

Azure Data Share에서 전송된 공유로 이동하여 **공유 구독** 탭을 선택합니다.

![공유 구독 철회](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

공유 구독을 삭제할 수신자 옆의 확인란을 선택하고 **철회** 를 클릭합니다. 이 소비자에게 더 이상 해당 데이터에 대한 업데이트가 수신되지 않습니다.

## <a name="next-steps"></a>다음 단계
[데이터 공유 모니터링](how-to-monitor.md) 방법을 자세히 알아보세요.