---
title: 기존 Azure Data Share에 데이터 세트 추가
description: Azure Data Share의 기존 데이터 공유에 데이터 세트를 추가하고 같은 수신자와 공유하는 방법을 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910545"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Azure Data Share의 기존 공유에 데이터 세트를 추가하는 방법

이 문서에서는 Azure Data Share를 사용하여 기존 데이터 공유에 데이터 세트를 추가하는 방법을 설명합니다. 이렇게 하면 새 공유를 만들 필요 없이 동일한 수신자와 더 많은 데이터를 공유할 수 있습니다.

공유를 만들 때 데이터 세트를 추가하는 방법에 대한 자세한 내용은 [데이터 공유](share-your-data.md) 자습서를 참조하세요.

## <a name="navigate-to-a-sent-data-share"></a>전송된 데이터 공유로 이동

Azure Data Share에서 보낸 공유로 이동하여 **데이터 세트** 탭을 선택합니다. 데이터 세트를 더 추가하려면 **+ 데이터 세트 추가** 단추를 클릭합니다.

![도메인 추가가 선택된 모습을 보여 주는 스크린샷](./media/how-to/how-to-add-datasets/add-datasets.png)

오른쪽 패널에서 추가하려는 데이터 세트 유형을 선택하고 **다음** 을 클릭합니다. 추가하려는 데이터의 구독 및 리소스 그룹을 선택합니다. 드롭다운 화살표를 사용하여 추가할 데이터를 찾은 후 옆에 있는 상자를 선택합니다.

![데이터를 선택할 수 있는 Blob Storage 추가 창을 보여 주는 스크린샷](./media/how-to/how-to-add-datasets/add-datasets-side.png)

**데이터 세트 추가** 를 클릭하면 데이터 세트가 공유에 추가됩니다. 참고: 새 데이터 세트를 보기 위해 소비자는 스냅샷을 트리거해야 합니다. 구성된 스냅샷 설정이 있는 경우 소비자는 다음에 예약된 스냅샷이 완료되면 새 데이터 세트를 볼 수 있습니다. 스냅샷 설정이 구성되지 않은 경우 소비자는 업데이트를 받기 위해 데이터의 전체 또는 증분 복사본을 수동으로 트리거해야 합니다. 스냅샷에 대한 자세한 내용은 [스냅샷](terminology.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[기존 데이터 공유에 수신자를 추가](how-to-add-recipients.md)하는 방법을 자세히 알아봅니다.