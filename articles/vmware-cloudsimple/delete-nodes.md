---
title: 클라우드심플 - Azure를 통해 VM웨어 솔루션용 노드 삭제
description: CloudSimple 배포를 통해 VMWare에서 노드를 삭제하는 방법 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024741"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>클라우드 단순에 의해 Azure VMware 솔루션에서 노드 삭제

CloudSimple 노드는 생성되면 미터화됩니다.  노드의 계량을 중지하려면 노드를 삭제해야 합니다.  Azure 포털에서 사용되지 않는 노드를 삭제합니다.

## <a name="before-you-begin"></a>시작하기 전에

노드는 다음 조건에서만 삭제할 수 있습니다.

* 노드로 만든 프라이빗 클라우드가 삭제됩니다.  프라이빗 클라우드를 삭제하려면 [CloudSimple 프라이빗 클라우드에서 Azure VMware 솔루션 삭제를](delete-private-cloud.md)참조하십시오.
* 프라이빗 클라우드를 축소하여 노드가 프라이빗 클라우드에서 제거되었습니다.  프라이빗 클라우드를 축소하려면 [CloudSimple 프라이빗 클라우드로 Azure VMware 솔루션 축소를](shrink-private-cloud.md)참조하십시오.

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="delete-cloudsimple-node"></a>클라우드 단순 노드 삭제

1. **모든 서비스를**선택합니다.

2. 클라우드 **단순 노드 검색**.

   ![클라우드 단순 노드 검색](media/create-cloudsimple-node-search.png)

3. **클라우드 단순 노드를 선택합니다.**

4. 삭제할 프라이빗 클라우드에 속하지 않는 노드를 선택합니다.  **비공개 클라우드 이름** 열에는 노드가 속한 프라이빗 클라우드 이름이 표시됩니다.  사설 클라우드에서 노드를 사용하지 않으면 값이 비어 있습니다. 

    ![클라우드 단순 노드 선택](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 프라이빗 클라우드의 일부가 아닌 노드만 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 프라이빗 [클라우드에](cloudsimple-private-cloud.md) 대해 알아보기
