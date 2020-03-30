---
title: 기존 Azure 데이터 공유에 데이터 집합 추가
description: Azure 데이터 공유의 기존 데이터 공유에 데이터 집합을 추가하고 동일한 받는 사람과 공유하는 방법을 알아봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490540"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Azure 데이터 공유의 기존 공유에 데이터 집합을 추가하는 방법

이 문서에서는 Azure 데이터 공유를 사용하여 기존 데이터 공유에 데이터 집합을 추가하는 방법을 설명합니다. 이렇게 하면 새 공유를 만들지 않고도 동일한 받는 사람과 더 많은 데이터를 공유할 수 있습니다.

공유를 만들 때 데이터 집합을 추가하는 방법에 대한 자세한 내용은 [데이터 공유](share-your-data.md) 자습서를 참조하세요.

## <a name="navigate-to-a-sent-data-share"></a>보낸 데이터 공유로 이동

Azure 데이터 공유에서 보낸 공유로 이동하여 **데이터** 집합 **+ Add Datasets** 탭을 선택합니다.

![데이터 세트 추가](./media/how-to/how-to-add-datasets/add-datasets.png)

오른쪽 패널에서 추가할 데이터 집합 유형을 선택한 다음 **다음을**클릭합니다. 추가하려는 데이터의 구독 및 리소스 그룹을 선택합니다. 드롭다운 화살표를 사용하여 데이터 옆에 있는 확인란을 찾아 추가합니다.

![데이터 세트 추가](./media/how-to/how-to-add-datasets/add-datasets-side.png)

**데이터 집합 추가를**클릭하면 데이터 집합이 공유에 추가됩니다. 참고: 새 데이터 집합을 보려면 소비자가 스냅샷을 트리거해야 합니다. 구성된 스냅숏 설정이 있는 경우 소비자는 다음 예약된 스냅숏이 완료되면 새 데이터 집합을 볼 수 있습니다. 스냅숏 설정을 구성하지 않으면 소비자는 업데이트를 수신하기 위해 데이터의 전체 또는 증분 복사본을 수동으로 트리거해야 합니다. 스냅샷에 대한 자세한 내용은 [스냅샷](terminology.md)을 참조하십시오.

## <a name="next-steps"></a>다음 단계
[기존 데이터 공유에 받는 사람을 추가하는](how-to-add-recipients.md)방법에 대해 자세히 알아보세요.