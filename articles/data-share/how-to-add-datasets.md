---
title: 기존 Azure 데이터 공유에 데이터 집합 추가
description: Azure 데이터 공유의 기존 데이터 공유에 데이터 집합을 추가 하 고 같은 받는 사람과 공유 하는 방법에 대해 알아봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: c4aee1eb268c124586388eacf8c908c20d799bc9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110898"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Azure 데이터 공유의 기존 공유에 데이터 집합을 추가 하는 방법

이 문서에서는 Azure 데이터 공유를 사용 하 여 기존 데이터 공유에 데이터 집합을 추가 하는 방법을 설명 합니다. 이렇게 하면 새 공유를 만들 필요 없이 동일한 받는 사람에 게 더 많은 데이터를 공유할 수 있습니다.

공유를 만들 때 데이터 집합을 추가 하는 방법에 대 한 자세한 내용은 [데이터 공유](share-your-data.md) 자습서를 참조 하세요.

## <a name="navigate-to-a-sent-data-share"></a>전송 된 데이터 공유로 이동 합니다.

Azure 데이터 공유에서 보낸 공유로 이동 하 여 **데이터 집합** 탭을 선택 합니다. **데이터 집합 추가 단추를** 클릭 하 여 데이터 집합을 더 추가 합니다.

![데이터 세트 추가](./media/how-to/how-to-add-datasets/add-datasets.png)

오른쪽 패널에서 추가 하려는 데이터 집합 유형을 선택 하 고 **다음**을 클릭 합니다. 추가 하려는 데이터의 구독 및 리소스 그룹을 선택 합니다. 드롭다운 화살표를 사용 하 여 추가할 데이터 옆의 상자를 찾아 선택 합니다.

![데이터 세트 추가](./media/how-to/how-to-add-datasets/add-datasets-side.png)

**데이터 집합 추가**를 클릭 하면 데이터 집합이 공유에 추가 됩니다. 참고: 새 데이터 집합을 보려면 소비자가 스냅숏을 트리거해야 합니다. 구성 된 스냅숏 설정이 있는 경우 소비자는 다음에 예약 된 스냅숏이 완료 되 면 새 데이터 집합을 볼 수 있습니다. 스냅숏 설정이 구성 되지 않은 경우 소비자는 업데이트를 받기 위해 데이터의 전체 또는 증분 복사본을 수동으로 트리거해야 합니다. 스냅숏에 대 한 자세한 내용은 [스냅숏](terminology.md)을 참조 하십시오.

## <a name="next-steps"></a>다음 단계
[기존 데이터 공유에 받는 사람을 추가](how-to-add-recipients.md)하는 방법에 대해 자세히 알아보세요.