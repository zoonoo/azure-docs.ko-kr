---
title: VMware Solution by CloudSimple용 노드 삭제 - Azure
description: CloudSimple 배포를 사용하여 VMWare에서 노드를 삭제하는 방법을 알아봅니다. CloudSimple 노드는 측정됩니다. Azure Portal에서 사용되지 않는 노드를 삭제합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88142267"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple에서 노드 삭제

생성된 CloudSimple 노드는 측정됩니다.  노드 측정을 중지하려면 노드를 삭제해야 합니다.  Azure Portal에서 사용되지 않는 노드를 삭제합니다.

## <a name="before-you-begin"></a>시작하기 전에

다음 조건에서만 노드를 삭제할 수 있습니다.

* 노드를 사용하여 만든 프라이빗 클라우드가 삭제되었습니다.  프라이빗 클라우드를 삭제하려면 [Azure VMware Solution by CloudSimple 프라이빗 클라우드 삭제](delete-private-cloud.md)를 참조하세요.
* 프라이빗 클라우드를 축소하여 프라이빗 클라우드에서 노드가 제거되었습니다.  프라이빗 클라우드를 축소하려면 [Azure VMware Solution by CloudSimple 프라이빗 클라우드 축소](shrink-private-cloud.md)를 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="delete-cloudsimple-node"></a>CloudSimple 노드 삭제

1. **모든 서비스** 를 선택합니다.

2. **CloudSimple 노드** 를 검색합니다.

   ![CloudSimple 노드 검색](media/create-cloudsimple-node-search.png)

3. **CloudSimple 노드** 를 선택합니다.

4. 프라이빗 클라우드에 속하지 않는 노드 중에서 삭제할 노드를 선택합니다.  **프라이빗 클라우드 이름** 열에 노드가 속한 프라이빗 클라우드 이름이 표시됩니다.  프라이빗 클라우드에서 사용되지 않는 노드의 경우 값이 비어 있습니다. 

    ![CloudSimple 노드 선택](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 프라이빗 클라우드에 속하지 않는 노드만 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드](cloudsimple-private-cloud.md)에 대한 자세한 정보
