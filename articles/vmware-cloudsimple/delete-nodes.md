---
title: VMware 솔루션에 대 한 노드 삭제 (AVS)-Azure
description: AVS 배포를 사용 하 여 VMWare에서 노드를 삭제 하는 방법을 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024741"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Azure VMware 솔루션에서 AVS로 노드 삭제

AVS 노드는 생성 되 면 계량 됩니다. 노드 계량을 중지 하려면 노드를 삭제 해야 합니다. Azure Portal에서 사용 되지 않는 노드를 삭제 합니다.

## <a name="before-you-begin"></a>시작하기 전에

노드는 다음 조건 에서만 삭제할 수 있습니다.

* 노드를 사용 하 여 만든 AVS 사설 클라우드가 삭제 됩니다. AVS 사설 클라우드를 삭제 하려면 [Avs 사설 클라우드에서 Azure VMware 솔루션 삭제](delete-private-cloud.md)를 참조 하세요.
* 노드가 avs 사설 클라우드를 축소 하 여 AVS 사설 클라우드에서 제거 되었습니다. AVS 사설 클라우드를 축소 하려면 [Avs 사설 클라우드에서 Azure VMware 솔루션 축소](shrink-private-cloud.md)를 참조 하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="delete-avs-node"></a>AVS 노드 삭제

1. **모든 서비스**를 선택합니다.

2. **AVS 노드**를 검색 합니다.

   ![AVS 노드 검색](media/create-cloudsimple-node-search.png)

3. **AVS 노드**를 선택 합니다.

4. 삭제할 AVS 사설 클라우드에 속하지 않는 노드를 선택 합니다. **AVS 사설 클라우드 이름** 열은 노드가 속한 Avs 사설 클라우드 이름을 표시 합니다. 노드가 AVS 사설 클라우드에서 사용 되지 않는 경우에는 값이 비어 있게 됩니다. 

    ![AVS 노드 선택](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> AVS 사설 클라우드에 속하지 않는 노드만 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [AVS 사설 클라우드에](cloudsimple-private-cloud.md) 대해 알아보기
